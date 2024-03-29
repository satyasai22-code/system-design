# Queues
## Problem:

Pizza Service (payment respone)<->(requests payment) Payment Service (watch < or >)
- Pizza service requests tons of requests but payment service is slow
- Payment service is a bottleneck and slows down Pizza and other services dependent on it
- Pizza service is stuck with Payment Service as the flow is synchronous
- The fastness of entire system depends on slowest component(here payment system)
- Producers and Consumers with Queue in between
   
   Producers(Pizza Service) -> Queue -> Consumers(Payment Service)

- This type of system decouples services and enhance scalibility. Also producers, consumers are independent of each other and can be developed in diff languages
- But asynchronous architecture has downsides. Ex: did payment service process succesfully, is message still in queue, did payment fail? All these are unanswered as producer doesn't recieve response from consumer.

- Eg Queues: Apache Kafka, Amazon SQS, Rabbit MQ, Apache ActiveMQ

Pros: 
- Buffering (one service put message and other service can pick on its own time and pace)
- Request spikes smoothing
- Message durability(message is lost but stored in queue when a service is down)

Cons:
- System complexity
- increased latency

### Messaging Models: 2 types

Most queues support both the models
- Message Queue: 
  - Message arrives in queue and it will be processed only once(even though there are mulitple replicas exists for resiliency. If replica is down, then queue will assign the message to another replica)
  - It indicated an asynchronous Action
  - ensures message is delivered exactly once
  - message can arrive out of order
- Publisher/Subscriber (Pub/Sub): 
    - Notify to multiple services when an event is happened. Eg: Payments Service succesful payment should be notified to Reciepts, Billing Service. Here the message will be multiplexed or repeated for each of the consumers(Billing, Reciepts Service).
    - Notification
    - At least once delivery
    - Messages are always in order since there are no retries

More Read: https://dzone.com/articles/comparing-publish-subscribe-messaging-and-message
