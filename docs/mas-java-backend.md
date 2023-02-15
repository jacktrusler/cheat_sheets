# MAS Java
Mas stands for Medical Answering Service, it is a company that specializes in medical transportation.

## Repository
The Repository is broken up into many parts, the three directories I care about at the moment are:
1. mas-rest-service
2. mas-db-service
3. mas-core-model

## Summary

*(If you want the full implementation, including methods, continue after the summary.)*  
To summarize the flow, it goes like this

1. Terascript makes an api request using the 
`http://localhost:7010/v5.5/call` endpoint

2. Inside this request, extra headers are included 
[additional headers](#additional-headers) and the body of the request includes a field called `call`
which references the function to be called.

3. The TSHybridEndpointApiController checks to see what the call string equals and passes it along to
HybridEndPointImpl as defined by the HybridEndPoint interface.

4. Calls method in TransProviderDriverRestServiceApi, which builds a uri using the WebClient object:  
`http://localhost:6010/tp/enableDisableDriver/{tpId}/{userId}/{driverId}/{statusFlag}` to hit the [mas-db-service](#mas-db-service).

5. The request is captured by a spring annotation 
```
@RequestMapping("/tp/enableDisableDriver/{tpId}/{userId}/{driverId}/{statusFlag}")
@GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
Boolean enableDisableDriver(@PathVariable("tpId") Integer tpId, @PathVariable("userId") Integer userId,
                            @PathVariable("driverId") Integer driverId, @PathVariable("statusFlag") Boolean statusFlag);
```
and the variables are passed into the method related to this `@RequestMapping`

6. The database is accessed using a repository object `TPDriverRepository tpDriverRepository` and returns
either an entity or a data transfer object (DTO)

7. The DTO is possibly manipulated or simply returned (in this case it is changing a status flag),
and then a response is sent to `http://localhost:7010` and finally back to the caller.

## mas-rest-service
The directory for endpoints attempting to reach various services in the mas backend. The general flow
for updating the database starts here and moves as follows: 

```java
    1. TSHybrid  //Endpoint with path /v5.5/call  
    2. HybridEndPoint  //Interface  
    3. HybridEndPointImpl  //Class that performs basic validation  
    4. RestController  //Interface
    5. RestControllerImpl  //Class that hits mas-db-service
    6. DbServiceController  //Interface 
    7. ControllerImpl //Class that will make changes in DB and update/fetches records
```

Here's the flow for enableDriver in mas-rest-service: (run the server from the RestServicesApplication class 
server runs locally on port 7010.)

Starting in **TSHybridEndpointAPI** the endpoint is hit by `localhost:7010/v5.5/call` and includes 
headers. 

### Additional Headers
Some additional headers defined might look like this:   
```
Accept: application/json
Bearer: TWFzZGV2ZWxvcGVyMjAxOSE
Content-Type: application/json
x-trans-provider-99: 57093
x-user-id: x-trans-provider-99`
```

The body of the request might look like this:
```json
{
    "TPRequest": {
        "sessionIdentifier": "e2714d9f-d42c-4c8a-afe6-745942107903",
        "call": "enableDriver",
        "version": "1",
        "driverId": 1881532
    }
}
```
where the `call` field is the function that will be called.

Then inside the endpoint api, if the @RequestHeaders match they are passed to the postCall. (Need to confirm this is true, I'm not sure how 
this works in spring, required = false seems to make the fields optional??)
```java
@PostMapping(
      value = "/call",
      consumes = MediaType.APPLICATION_JSON_VALUE,
      produces = MediaType.APPLICATION_JSON_VALUE
  )
public interface TSHybridEnpointAPI {
  ResponseEntity<HybridTSTPResponse> postCall(@RequestBody HybridTPRequestEnvelope hybridRequest,
      @RequestHeader(name = "x-user-name", required = false) String userName,
      @RequestHeader(name = "x-call-name-override", required = false) String responseCallName,
      @RequestHeader(name = "x-trans-provider-ids", required = false) String transProviderIds,
      @RequestHeader(name = "x-trans-provider-99", required = false) String transProvider99,
      @RequestHeader(name = "x-user-id", required = false) String userId,
      //... way more potential headers)
}
```
After this the implementation of the interface is responsible for making the call to the rest services. 
It does this by checking the call field, which is provided by the body of the terascript post req and then using the 
method that it relates to. These methods are defined in the various EndPoint classes.

```java 
public class TSHybridEndpointApiController implements TSHybridEndpointAPI {
  private final HybridEndPoint hybridEndPoint;
  private final HybridSessionEndPoint hybridSessionEndPoint;
  private final TransportationProviderDriverService transportationProviderDriverService;
  private final HybridTripEndPoint hybridTripEndPoint;

  //...many more checks to see what the call string equals
  } else if (StringUtils.equals(envelope.getRequest().getCall(), "enableDriver")) {
    response = hybridEndPoint.enableDriver(transProvider99, userId, envelope);
  } else if (StringUtils.equals(envelope.getRequest().getCall(), "disableDriver")) {
    response = hybridEndPoint.disableDriver(transProvider99, userId, envelope);
  }
}
```
enableDriver function is called in the TSHybridApiController which is then used to call the function in HybridEndPointImpl. 

```java
//HybridEndPointImpl

  public HybridTSTPResponse enableDriver(String transProviderId, String userId, HybridTPRequestEnvelope hybridRequest) throws HybridException {
    //always return success call status
    HybridTSTPResponse result = HybridTSTPResponse.builder()
            .callName(hybridRequest.getRequest().getCall()).callStatus(CallStatus.SUCCESS.getCode())
            .build();

    Integer tpId = EndpointUtils.getIntegerFromObject(transProviderId);
    Integer user = EndpointUtils.getIntegerFromObject(userId);
    if (tpId == null) {
      log.warn("No TP ID provided in the header");
      result.setResponseStatus(ResponseStatus.INVALID_VALUE.getCode());
    } else if(user == null) {
      log.warn("No user ID provided in the header");
      result.setResponseStatus(ResponseStatus.INVALID_VALUE.getCode());
    } else {
      HybridTPRequest hybridTPRequest = hybridRequest.getRequest();
      Optional<Integer> driverId = Optional.empty();
      Object exists = hybridTPRequest.getAdditionalFields().get(DRIVER_ID_INPUT);

      if (Objects.nonNull(exists)) {
        Integer driverNum = EndpointUtils.getIntegerFromObject(exists);
        if (Objects.isNull(driverNum)) {
          log.error("Invalid driverId must be number");
          result.setResponseStatus(ResponseStatus.INVALID_VALUE.getCode());
        } else {
          driverId = Optional.of(driverNum);
        }
      } else {
        log.error("Missing driver ID");
        result.setResponseStatus(ResponseStatus.MISSING_VALUE.getCode());
        return result;
      }
      //---------------------------------
      // Here is the line of interest
      //---------------------------------
      ResponseEntity<Boolean> driverResult =
              transProviderDriverService.enableDriver(tpId, user, driverId.get());

      if (driverResult != null && driverResult.getBody() != null) {
        Boolean driverStatus = driverResult.getBody();
        if(driverStatus) {
          //SUCCESS because we have values
          result.setResponseStatus(ResponseStatus.SUCCESS.getCode());
        } else {
          result.setResponseStatus(ResponseStatus.DRIVER_NOT_FOUND.getCode());
        }
      } else {
        //NO_RESULTS on empty body
        result.setResponseStatus(ResponseStatus.NO_RESULTS.getCode());
      }
    }
    return result;
  }
```
The following is the interface that defines the api implementation, the endpoints are not used at the moment (1/24/2023). So instead
it's just acting like a plain old java implementation, without the springboot functionality.

```java
public interface TransProviderDriverRestServiceApi {

  @RequestMapping("/enableDriver/{tpId}/{userId}/{driverId}")
  @GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
  ResponseEntity<Boolean> enableDriver(@PathVariable("tpId") int tpId, @PathVariable("userId") int userId, @PathVariable("driverId") int driverId);
}
```
- NOTE: inside this interface there are mappings to endpoints, these methods are provided by spring | hibernate. 
when the endpoint is hit the enableDriver function is run with the variables from the endpoint.

- NOTE: Currently 1/24/2023 these endpoints are just placeholders, while this functionality works (i.e. with postman) it is not yet 
integrated into mas-services

While this api endpoint will call the function, the uri is not used currently, instead the function is called directly from the 
HybridEndPointImpl.

```java
public class TransProviderDriverRestServiceApiImpl implements TransProviderDriverRestServiceApi {

  private static final String ENABLE_DISABLE_DRIVER = "/tp/enableDisableDriver/{tpId}/{userId}/{driverId}/{statusFlag}";

  private final WebClient dbServiceClient;

  @Autowired
  public TransProviderDriverRestServiceApiImpl(WebClient dbServiceClient) {
    this.dbServiceClient = dbServiceClient;
  }

  @Override
  public ResponseEntity<Boolean> enableDriver(int tpId, int userId, int driverId) {
        log.info("----------[ enableDriver ]----------");
        log.info("tpId: {}", tpId);
        log.info("userId: {}", userId);
        log.info("driverId: {}", driverId);

        Boolean status;
        try {
            status = this.dbServiceClient.get()
                    .uri(uriBuilder -> uriBuilder.path(ENABLE_DISABLE_DRIVER)
                            .build(tpId, userId, driverId, Boolean.TRUE)).retrieve().bodyToMono(Boolean.class).block();
            return ResponseEntity.ok(status);

        } catch (Exception e) {
            log.error("Failed" + e.getMessage());
        }
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }
    //...More methods
}
```

Inside the try block a uri is built and then sent to the db service running on port 6010. This is done 
by creating a WebClient object called dbServiceClient which has a get() method on it. This get method 
uses the built uri to send a request to the ENABLE\_DISABLE\_DRIVER path with the associated variable paths. 
After the request is sent a response is returned, this response is finally sent to the client.

## mas-db-service

When the uri is sent to 6010 it is captured by the mas-db-service that is listening on that port. 
This is done by a dependency injected method from springboot in a DB Rest service interface. 

```java
@RestController
public interface DBTPDriverRestService {

  @RequestMapping("/tp/enableDisableDriver/{tpId}/{userId}/{driverId}/{statusFlag}")
    @GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
    Boolean enableDisableDriver(@PathVariable("tpId") Integer tpId, @PathVariable("userId") Integer userId,
                                @PathVariable("driverId") Integer driverId, @PathVariable("statusFlag") Boolean statusFlag);
}
```
These variables get passed along to the method enableDisableDriver which is in the DBTPDriverRestServiceImpl.

```java
public class DBTPDriverRestServiceImpl implements DBTPDriverRestService {

  private final UserRepository userRepository;

  @Override
  public Boolean enableDisableDriver(Integer tpId, Integer userId, Integer driverId, Boolean statusFlag) {
      TPDriverEntity tpDriver = dbtpDriverRepository.findByIdAndTpId(driverId, tpId);
      if (Objects.nonNull(tpDriver)) {
          // with this we don't need to detach entities anymore
          TPDriverEntity originalValue = SerializationUtils.clone(tpDriver);

          tpDriver.setDriverStatusActive(statusFlag);
          dbtpDriverRepository.save(tpDriver);

          List<UserEntity> userList = userRepository.findAllByTransProviderIdAndTransProviderDriverId(tpId, driverId);
          if (userList.size() > 0) {
              for (UserEntity user : userList) {
                  user.setStatus(statusFlag);
              }
              userRepository.saveAll(userList);
          }
          // Add Record in Change Log Table
          logChanges(userId, originalValue, tpDriver);

          return true;
      }
      return false;
  }
}
```

This method finally returns a boolean to TransProviderDriverRestServiceApiImpl which returns the boolean
along with a status back to the client with the ResponseEntity object.

## mas-core-model
Also to note here, the database is altered using the TPDriverEntity object that is retreived from the 
dbtpDriverRepository. These DTOs are found in the mas-core-model folder, where many of the interfaces 
and models are stored. Here is what the TPDriverEntity looks like: 

```java
@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Table(name = "Trans_Provider_Drivers")
public class TPDriverEntity implements IDriver {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "ID")
  private Integer id;

  @Column(name = "Trans_Provider_ID")
  private Integer tpId;

  @Column(name = "Driver_Status", columnDefinition = "TINYINT", length = 1)
  private Boolean driverStatusActive;

  //...more fields

}
```

This class is a Object Relational Map to the database table fields. The methods on it are provided by lombok
and thus to change the driver status you call `setDriverStatusActive(statusFlag)`.
While entities are technically DTOs, some classes in the repository are explicitly appended with Dto. 
This is to indicate that these data transfer objects are getting data from multiple tables, where as 
entites only get data from the table they're defined with. 

## Notes on Hibernate, Spring and JPA

Hibernate provides an implementation of the Java Persistance Api (JPA). What this means is that 
the application's data outlives the process, objects can live beyond the scope of the JVM.

Hibernate allows us to write Hibernate Query Language, which is the language that maps java objects
to SQL queries. 

Spring framework provides a lot of functionality in creating database objects, defining endpoints, 
and automatic method generation. 

Example of a hibernate query: 
```java
@Query(
      "Select new com.mas.db.jackson5.ActivityJoin(al.id, al.recordId, a.label) " +
          "FROM " +
          "ActivityLog AS al " +
          "JOIN " +
          "Activities AS a ON (a.id = al.id) " +
          "WHERE al.recordId < 100 ")
  public List<GamerJoin> findRecordsUnder100();
```

Hibernate uses objects that are persisted in memory to create queries that interact directly with 
a relational database. In this example it uses 2 java objects -- ActivityLog.java and Activities.java 
and then it uses a join object -- ActivityJoin.java 

```java 
@Getter
@Setter
@AllArgsConstructor
public class ActivityJoin {
  private Integer activityId;
  private Integer recordId;
  private String label;
}
```
Hibernate 
