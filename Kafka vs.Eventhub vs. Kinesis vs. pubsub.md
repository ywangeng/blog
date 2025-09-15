In the world of real-time data processing, message streaming platforms are essential for moving and processing events 
reliably at scale.

Here’s a quick comparison of four key players on performance:


| Feature      | Apache Kafka                                | Azure Event Hubs            | AWS Kinesis                             | Google Cloud Pub/Sub                    |
|--------------|---------------------------------------------|-----------------------------|-----------------------------------------|-----------------------------------------|
| Throughput   | Millions of events/sec (depends on cluster) | Millions/sec per namespace  | 1 MB/sec per shard (scales with shards) | Millions of msgs/sec (serverless auto)  |
| Latency      | < 10 ms (with tuning)                       | ~ tens of ms                | ~70 ms (avg)                            | ~ tens of ms                            |
| Scaling      | Horizontal via partitions/brokers           | Partitions, auto-inflate    | Manual/provisioned shards               | Fully serverless auto-scaling           |
| Delivery     | At-least-once, exactly-once with Streams    | At-least-once, exactly-once | At-least-once                           | At-least-once, DLQ, ordering available  |
| Ops Overhead | High (self-managed)                         | Low (PaaS)                  | Medium (AWS-managed but shard mgmt)     | Lowest (serverless, no infra to manage) |

### Takeaways:
- Kafka: best for control, customisation, and very high throughput use cases.
- Event Hubs: ideal for Azure-centric workloads needing scale without ops.
- Kinesis: great fit for AWS-heavy environments where deep integration matters.
- Pub/Sub: perfect for serverless, “set and forget” streaming at scale.


[1] https://kafka.apache.org/documentation/ \
[2] https://pavolkutaj.medium.com/explaining-the-difference-between-kafka-and-kinesis-9eca5e58627b \
[3] https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-about \
[4] https://www.svix.com/resources/faq/kafka-vs-azure-event-hub/ \
[5] https://docs.aws.amazon.com/kinesis/ \
[6] https://en.wikipedia.org/wiki/Amazon_Kinesis \
[7] https://cloud.google.com/pubsub/docs/overview \
[8] https://ramanisandeep.wordpress.com/2020/06/02/choosing-between-apache-kafka-amazon-kinesis-microsoft-event-hubs-and-google-pub-sub/ 
