# MAS Java
Mas stands for Medical Answering Service, it is a company that specializes in medical transportation.

## Repository
The Repository is broken up into many parts, the four directories I care about at the moment are:
1. mas-core-utils
2. mas-rest-service
3. mas-db-service
4. mas-core-model

## mas-core-utils
A place where helper functions/classes are kept i.e. in class MedAnsweringUtils

```java
/**
   * Utility method to calculate time taken since a given number in millis
   *
   * @param given time in mills to calculate the difference
   * @return the difference in millis between now and the given time
*/
public static long timeFromInMillis(long given) {
  long now = System.currentTimeMillis();
  return (given == 0 || given < now) ? 0 : now - given;
}
```

## mas-rest-service
The directory for endpoints attempting to reach various services in the mas backend. The general flow
for updating the database starts here and moves as follows: 

```java
    1. TSHybrid  //Endpoint with path /v5.5/call  
    2. HybridEndPoint  //Interface  
    3. HybridEndPointImp  //Class that performs basic validation  
    4. RestController  //Interface
    5. RestControllerImpl  //Class that hits mas-db-service
    6. DbServiceController  //Interface 
    7. ControllerImpl //Class that will make changes in DB and update/fetches records
```

Here's the flow for enableDriver in mas-rest-service: (run the server from the RestServicesApplication class 
server runs locally on port 7010.)

Starting in **TSHybridEndpointAPI** the endpoint is hit by `localhost:7010/v5.5/call` and includes 
headers. 

Some additional headers defined might look like this:   
```
Accept: application/json
Bearer: TWFzZGV2ZWxvcGVyMjAxOSE
Content-Type: application/json
x-trans-provider-99: 57093
x-user-id: x-trans-provider-99`
```

Then inside the endpoint api, if the @RequestHeaders match they are passed to the postCall. 
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
It does this by checking the call field, which is provided by the terascript post req and then using the 
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
[this part i'm unsureof but somehow these disableDriver() and enableDriver() function call the rest 
service controllers]

```java
public interface TransProviderDriverRestServiceApi {

  @RequestMapping("/enableDriver/{tpId}/{userId}/{driverId}")
  @GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
  ResponseEntity<Boolean> enableDriver(@PathVariable("tpId") int tpId, @PathVariable("userId") int userId, @PathVariable("driverId") int driverId);
}
```
inside this interface there are mappings to endpoints, these methods are provided by spring | hibernate. 
when the endpoint is hit the enableDriver function is run with the variables from the endpoint.

The implementation of the interface is simply the interface with Impl appended, this pattern is repeated for other controllers 
in the db-service.

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
