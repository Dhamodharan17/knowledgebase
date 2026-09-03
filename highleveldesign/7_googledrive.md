# Google Drive

## Requirement

### Functional Requirement

- upload, download, auto sync, file sharing

### Non-Functional Requirement

1. CAP Theroem
2. Low Latency

## Core Entities & APIs

### Core Entities

### APIs

## High Level Design

- postgres for meta data about file actual link in s3 and chunk information
- s3 for actual content

## Deep Dive

1. chunked & resumable uploads
   1. client side chunking (trade off)
   2. idempotent resumable
   3. resent missing chunks (verify fingerprint)
2. Delta sync : after one word edit, don't upload complete document
   1. Content Defined Duplication
3. Data Dupliaction : Content Addressable Storage (hash each block)
4. Sync Changes : client side constant poll server

## ![Alt Text](./assets/amazon.png)

## External Resources

-<https://spacecomplexity.ai/blog/dropbox-system-design-interview-guide>
