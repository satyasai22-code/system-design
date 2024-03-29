# Rabbit MQ
- Based on AMQP protocol
- By default Messages are stored until one of the consumer retrived them and then deleted(this behaviour can be changed)
- used often to schedule heavy tasks under background(processing resizing of images, payments processing, running reports etc)
- Distribute tasks between server where each gets some work to do
- More read: https://www.cloudamqp.com/blog/part1-rabbitmq-for-beginners-what-is-rabbitmq.html
### Routing keys
- Every rabbitmq message has a key 
- It reperesents the address of the queue it needs to go

### Exchanges
- is a router or load balancer that recieves msg and puts them in crct queue  with the help of bindings and routing keys. A binding is a link between a queue and an exchange.
- supports 3 ways of working
  - **Direct Exchange**: puts message in a queue whose binding key matches the routing key of the message. If multiple consumer, then puts them in round robin manner. Eg: If the queue is bound to the exchange with the binding key "pdfprocess", a message published to the exchange with a routing key "pdfprocess" is routed to that queue.
  - **Topic**: The topic exchange does a wildcard match between the routing key and the routing pattern specified in the binding
  - **Headers**: Similar to  Topic exchange, Headers exchanges use the message header attributes for routing .
  - **Fanout**: 