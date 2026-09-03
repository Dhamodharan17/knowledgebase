# Uber

## Requirement

### Functional Requirement

- user enter source and destination & get fare
- user should request ride based on ETA ??
- driver should accept/reject request
- driver should navigate to pickup and drop location

### Non-Functional Requirement

1. CAP Theroem
   - Strong Consistency : 1 Driver - 1 Rider Matching
   - High Availability outside
2. High throughput in crowded place
3. low latency driver matching

## Core Entities & APIs

### Core Entities

- Ride, Rider, Driver, Location

### APIs

- getEstimate(src, dest)
- requestRide()
- updateDriverLocation()

## High Level Design

- Estimate Fare - Ride Service which will connect with 3rd party service to get fare and updates ride information to our database
- Update Location - drivers will be updating their location
  - Communication : Websocket over long polling
  - Database : NoSQL+Geo index support -> MongoDB/Dynamo which have native 2d sphere index
- Request Ride : fetch + filter available drivers, push notification, patch driver decision to same Ride object

## Deep Dive

- low latency search
  - Posgres (Btree 1d indexing so slow)
  - Post GIS(Quad Tree) for uneven data
  - GeoHash(Redis) for even data
- Consistency on 1 Driver: 1 Rider Match
  - AP1 : maintain different status and reserved time stamp in table, unblock drivers is status not changed to booking after 10 minutes.(complex data model and not scalble)
  - AP2 : fixed crob job to cleanup status, drivers reseved in 12:09 wil exipry in 1 minute not 10 minutes
  - AP3 : Distributed Lock with ttl (fail over) ; drivers -> fetch from db and filter in redis.
- High Throughput
  - horizontal scaling
  - shard by location
  - poll from service ??

## ![Alt Text](./assets/amazon.png)

Core Design Corrections & ValidationsConsistency (1 Driver - 1 Rider) -> The CP/AP Trade-off: Your analysis correctly identifies that Strong Consistency is non-negotiable for driver assignment (CP). However, suggesting a cron-job based cleanup (AP2) to resolve a CP problem is flawed. Cron jobs add deterministic delay and eventually break the CP guarantee during concurrent high-load requests. AP3 (Distributed Lock with TTL) is the correct approach. The system must lock a driver_id in Redis for $\approx$15 seconds before publishing the ride request to that driver. This prevents other matching instances from selecting that same driver simultaneously. The TTL elegantly handles the timeout (driver reject or ignore).High Throughput & Spatio-Temporal Data: Your deep dive analysis for Geo-sharding is correct. For high throughput, sharding the database by a Hierarchical Geo-Spatial Index (like S2 or H3) is mandatory, not by arbitrary application location IDs. S2/H3 mapping allows for extremely efficient, index-only filtering of active drivers within proximate grid cells.The Role of Kafka (Missing Component): A core missing piece is a high-throughput message bus. Driving updates (Location $\to$ WebSocket) must be streamed into Kafka immediately. Kafka decouples the firehose of location updates from the Ride Matcher. This allows a separate worker pool (e.g., Flink) to aggregate and update the final 'Search Index' (Redis Geo) without blocking the client.

Revised Architectural Flow:Location Stream: Drivers send WSS updates $\to$ Kafka $\to$ Flink Workers $\to$ Update Redis Geo Index.Ride Request (CP flow): Ride Service receives request $\to$ Query Redis Geo for nearby drivers $\to$ ACQUIRE DISTRIBUTED LOCKS on candidate drivers $\to$ Publish Ride to selected driver (via WSS/Push).
