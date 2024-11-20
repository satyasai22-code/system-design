# URL Shortener

POST Long URL(input) -> TinyURL(server) -> Stores short url in DB

GET short URL(input) -> TInyURL -> Actual URL from DB

## Requirement Analysis
- Short URL length: What should be the length of short url? Interviewer may say as much short as possible. Can it be 2 or 3 len? How do we come to the conlusion? Look at the traffic and decide
- Traffic(Ask interiviewer following details or you assume):
   - 10M URL/day => 3650M URL/yr
   - Support for 100 yrs => 365 B URL/yr
   - Characters supported: 0-9/a-z/A-z => 62 characters
   - Now do permutation => 62^6 ~ 56B, 62^7 ~ 3.5 Trillion
- From above, 7 characters serves our purpose
- How to generate hash value?
   - Hash Functions
      - MD5 => Generates 128 bit => 16 bytes (32 Hexa digits) => Generates 32 chars. Does  not serve our purpose as we need only 7 chars.
      - SHA-1 => 160 bit => 20 bytes => 40 hexa digits => 40 chars. Doesn't server our purpose. Even if we trim 40 to 7 char, then lot of collisons or duplicates. 
   - Base62 (62 coz of 62 chars)
      - Any decimal can be converted to any base
      - ID generator is needed to form a decimal
      - Length could differ as base is 62 (for 1000 its g8,for 100000 length differs, but we need 7).
      - ID generator is the main part
        - Can we use DB id? Problem: 10M/day cant be put in single DB. Needs distributed DB. Now we need to make sure no duplicate ids generated(Synchronization Problem).
        - use Ticketing Server (Centralized auto increment server)? But if it is down? Single point of failure. So no use 
        - use Snowflake(1 bit, timestamp(41 bit), machine id(10 bit), seq no(12 bit))? Possible. Timestamp makes it unique. If 2 req comes at same timestamp, then different machine id solved the issue. If 2 req comes from same machine timestamp resolves the conflicts. Twitter uses this
        - use ZooKeeper? Most preferred one. Distributed application, can coordinate with each other reliabily. Creates range (0-1M, 1M-2M,...11-12M,..). Now Each thread is allocated particular Range. T1(Worker Thread 1 or server)-> R1(Range), T2 -> R2...If range is filled up, Ti picks new Range. This guarantees unique Id in distributed environemt
      - Length Differs? Sol: Use padding (';', '@')
- SnowFlake Approach:
   - sign (1 bit unused)
   - timestamp(41 bit => 2^41 - 1 milli seconds => 69.73 yrs)
   - machine id(10 bit => 1024 concurrent machines)
   - sequence Number(12 bit, for given timestamp in milli seconds, 4096 concurrent ids. It resets to 0 after each milli second).
   - generated id is based on above values
   - Each milli sec(timestamp), 1024 machines can parallely generate unique ids for 69.73 yrs. In case multiple servers request at same timestamp, sequence id is incremented.
   - Total 2^63 unique IDs

 Design:
                          / TinyURL - S1   -> Cache(for each data centre)
    User -> Load Balancers - TinyURL - S2  -> DB (for each data centre) RDB (ID, shortURL, LongURL)
                           \ TinyURL - S3  -> Zookeeper(For each data centre)

Review:
 - Generate 7 chars
 - Use Base 62 instead of hash functions
 - Biggest problem with base 62 is generating a unique number
 - Do padding if base62 is less than req char(here 7).
 - Zoo keeper is not a unique ID generator. But it helps to coordinating with each other. Put logic in zoo keeper that takes care of range and alloting range to servers.