# Systems Thinking

## Tools

- Redis
- RabbitMQ / Kafka
- Load Balancers

### Redis

- great for caching

### RabbitMQ / Kafka

- for queues

### Load Balancers or Nginx


### Topics to consider

- Latency
- Rate limiting
- Connection pools
- Read replicas

Understand how the data flows between these components then you can start thinking about scalability.

Build a messaging app that uses

- Redis to cache the 50 latest messages
- A background job queue to process email notifications
- A rate limiteer to block spammy users (connecting different users to each other in private rooms)

## References

- [Programming Is Not Enough](https://www.youtube.com/watch?v=bZa2uicOTAE)