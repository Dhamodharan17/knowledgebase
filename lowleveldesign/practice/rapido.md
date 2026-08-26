## Rapido

Flow -> user request ride -> system assign driver -> driver accept ride -> reaches user location and drops -> ride ends

### Functional Requirement

- rider should be able to request a ride
- system should be able to fetch drivers based on algorhtim.
- drivers should be able to accept/reject ride
- driver should be able to update status "REACHED", RIDE COMPLETED.

### Core Entities

- Driver
- Rider
- Request
- Ride
- Ride Status

### Class Design

- RapidoFacade
  - rider.requestride()
  - searchService.searchDrivers()
  - driver.accept()
  - driver.changestatus("reached driver location")
  - driver.changestatus("order done")

- SearchService (Strategy Pattern)
- RideStatus Enum
- Ride
  - id
  - driver
  - rider
  - timestamp
  - location
  - fare
- User
- Driver
- Rider
