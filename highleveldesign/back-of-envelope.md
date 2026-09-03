# Back Of Envelope

## Common Numbers

| Power of 10 | Unit  | Meaning     |
| ----------- | ----- | ----------- |
| 10^3        | 1KB   | thousand    |
| 10^6        | 1MB   | Million     |
| 10^9        | 1GB   | Billion     |
| 10^12       | 1TB   | Trillion    |
| 10^15       | 1PB   | Quadrillion |
| 10^5        | 1 Day |             |

## Common Data Sizes

| Data Type                       | Typical Size  |
| ------------------------------- | ------------- |
| UUID/GUID                       | 16 bytes      |
| Integer (64-bit)                | 8 bytes       |
| Timestamp                       | 8 bytes       |
| URL (average)                   | 100-200 bytes |
| Tweet (280 chars + metadata)    | ~1 KB         |
| User profile record             | 1-5 KB        |
| Photo (compressed JPEG)         | 200 KB - 2 MB |
| Short video (1 min, compressed) | 5-50 MB       |

## QPS Estimation

```text
Quick QPS = (DAU × Actions per Day) / 100,000
```

**Example**

Social media post viewing, Given:

- 100 million DAU
- Each user views 50 posts per day

```text
QPS = (100M × 50) / 100,000 = 50,000 QPS
```

## Peak QPS

```text
Peak QPS = Average QPS × Peak Multiplier
```

- use 3x as a safe default multiplier for consumer applications

## Read vs Write QPS

- Breaking down read and write traffic separately is critical because they usually scale differently.

## Storage Estimation

```text
Total Storage = Data per Record × Records per Day × Days Retained × Overhead Factor
```

Example : Media Storage Example: Photo Sharing

```text
Given:
- 10 million photos uploaded per day
- Store original (2 MB) and thumbnail (200 KB)
- Retention: indefinite

Daily storage = 10M × (2 MB + 0.2 MB) = 10M × 2.2 MB = 22 TB/day
Monthly = 22 TB × 30 = 660 TB/month
Annual = 660 TB × 12 ≈ 8 PB/year

After 3 years: ~24 PB
```

## Bandwidth Estimation

```text
Bandwidth = QPS × Average Data Size per Request
```

Example

```text
Given:
- 50,000 QPS
- Average request: 1 KB (incoming)
- Average response: 10 KB (outgoing)

Ingress = 50,000 × 1 KB = 50 MB/s = 400 Mbps
Egress = 50,000 × 10 KB = 500 MB/s = 4 Gbps

With rough overhead (headers, retries, replication traffic):
Total egress: ~5.2 Gbps
```

## Concurrent Users

```text
Concurrent Users = DAU × (Sessions per Day × Avg Session Length) / Peak Window

Where Peak Window is the part of the day where most activity happens, often 4-8 hours.
```

Example : Social app sessions

```text
Given:
- 1 million DAU
- 3 sessions per user per day
- 10 minutes average session
- 6 peak hours (4 PM - 10 PM)

Concurrent = 1M × (3 × 10 min) / (6 hours × 60 min)
           = 1M × 30 / 360
           = 83,333 concurrent users during peak

This means we should design for roughly 80K concurrent active users during the busy window, not 1M simultaneous users.
```

---

source : <https://algomaster.io/learn/system-design-interviews/estimation-cheatsheet>
