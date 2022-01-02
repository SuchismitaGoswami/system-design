## Rate Limit system design
This is also known as throttling

## Why needed
1. Avoiding API from abuse
2. Improving security around the API
3. Reducing cost due to auto-scaling when system needs to handle huge requests 
4. Avoiding cascading failure when auto-scaling is not enabled. Multiple requests can make one replication down due to load, this can eventually cause cascading failures of entire system. -> Making system unavailable.

## Different Rate Limiting Algorithms
1. Token Bucket -> No of requests are limited to the no of tokens in the bucket and its refill window.
2. Leaky Bucket (Easy to implement using a queue)
3. Fixed Windows  
   1. How many no of request could be served in a given time frame
   2. Disadvantage 
      1. It introduces **clustering around the edge** -> if the no of request increases at the begining and the ending of two subsequent time frames, it can cause the system down due to load as it needs to handle 2X requests.
      2. Difficult to implment in distributed environment.

4. Sliding Windows -> avoid the issue with fixed window. 



## System Design 

### Functional Requirements

1. It needs to allow requests based on some strategy

### Non functional Requirements
1. Accurate
2. Scalable (It should be able to handle large no of hosts requesting access to the service)
3. Low Latency (fast)
4. Availability (This is not strictly required as by default all the requests could be handled)
5. Fault Tolerant (Not mandatory)


### Choose the rate limiting algorithms
Refer the `Different Rate Limiting Algorithms` section

### High level design

[Job Scheduler] - Resposible for scheduling the RuleRetriever task 
[Rule Retriever] - Resposible for 


## References
- https://www.youtube.com/watch?v=X3G8gdh9GKE&list=PLJN9ydlFnJsiEH_U9guHTNs6H2tH00h9S&index=3
- https://www.youtube.com/watch?v=FU4WlwfS3G0