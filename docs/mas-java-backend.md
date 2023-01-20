# MAS Java
Mas stands for Medical Answering Service, it is a company that specializes in medical transportation.

## Repository
The Repository is broken up into many parts, the three directories I care about at the moment are:
1. mas-core-utils
2. mas-rest-service
3. mas-db-service

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

    mas-rest-services/src/main/java/com/mas/rest/controllers/TransProviderDriverRestServiceApi

inside this interface there are mappings to endpoints, these methods are provided by spring/hibernate. 
when the endpoint is hit the enableDriver function is run with the variables from the endpoint.

```java
//Inside interface TransProviderDriverRestServiceApi

/**
   * Rest method to enable driver for a given ID
   *
   * @param tpId     the TpId to get the drivers for
   * @param driverId the driver ID to update status of
   * @return ResponseEntity encapsulating the HTTPStatus and the boolean status
   */
@RequestMapping("/enableDriver/{tpId}/{userId}/{driverId}")
@GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
ResponseEntity<Boolean> enableDriver(@PathVariable("tpId") int tpId, @PathVariable("userId") int userId, @PathVariable("driverId") int driverId);
```
The implementation of the interface is in the following location: 

    mas-rest-services/src/main/java/com/mas/rest/controllers/impl/TransProviderDriverRestServiceApiImpl
the name is simply the interface with Impl appended, this pattern is repeated for other controllers 
in the db-service.

```java
//Inside class TransProviderDriverRestServiceApiImpl

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
```
