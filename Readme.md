# MiniURL
MiniURL use Base62 algorithm to shorten longer URLs.
## Functional Requirements
- Shorten long URLs to shorter ones
- Redirect users to original URL when they access shortened URL
## Non-Functional Requirements:
- High availability
- Minimal latency
- Scalability
- URL should be unique
- Secure

## Traffic Assumptions
- Daily Active Users (DAU): 1 million
- Monthly Active Users (MAU): 30 million

### URL Creation:
- Each user creates on average 5 shortened URLs per day
- Daily new URLs = 5 million
- Monthly new URLs = 150 million
- Yearly new URLs = 1.8 billion

### URL Redirection (Read):
- Each shortened URL is accessed 10 times per day on average
- Daily redirections = 50 million
- QPS (Queries Per Second) for redirections:
  - Average: 50 million / 86400 ≈ 580 QPS
  - Peak: 2x average = 1160 QPS

### Write operations:
- New URLs per second = 5 million / 86400 ≈ 58 QPS
- Peak: 2x average = 116 QPS


## Storage Calculations

### Single URL Record:
- short_url: 7 bytes
- original_url: 2048 bytes (max)
- user_id: 8 bytes
- creation_date: 8 bytes
- expiration_date: 8 bytes
- click_count: 8 bytes
Total: ~2.1 KB per record

### Storage Requirements:
- Daily new storage: 5 million * 2.1 KB = 10.5 GB
- Monthly new storage: 315 GB
- Yearly new storage: 3.78 TB

### Database Indices:
- short_url index: 7 bytes * 1.8 billion = 12.6 GB
- user_id index: 8 bytes * 1.8 billion = 14.4 GB

## Cache Requirements
Assuming we want to cache 20% of daily active URLs:
- Daily active URLs = 50 million
- Cache entries = 10 million
- Size per cache entry = ~2.1 KB
- Total cache size needed = 21 GB

### Redis Cache Configuration:
- Multiple Redis instances
- Each instance: 32 GB RAM
- Total Redis servers: 2-3 for redundancy

## Bandwidth Calculations
### Incoming traffic:
- URL creation: 58 QPS * 2.1 KB = 121.8 KB/s
- Peak: 243.6 KB/s

### Outgoing traffic:
- URL redirections: 580 QPS * 2.1 KB = 1.218 MB/s
- Peak: 2.436 MB/s