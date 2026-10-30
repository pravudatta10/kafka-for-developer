# Kafka CLI — Consuming Messages

## Environment Setup

These notes use Kafka running in Docker on Windows, with IntelliJ IDEA as the development environment.

| Setting | Value |
|---|---|
| Operating system | Windows |
| IDE | IntelliJ IDEA |
| Kafka container | `kafka-for-developer-broker` |
| Kafka bootstrap server inside container | `localhost:9092` |
| Kafka bootstrap server from Windows | `localhost:19092` |
| Kafka UI | [http://localhost:9999](http://localhost:9999) |

### How to execute commands

Prepend the following to standard Kafka CLI commands when executing them from IntelliJ terminal:

```powershell
docker exec -it kafka-for-developer-broker
```

For non-interactive commands, `docker exec` without `-it` is sufficient.

---

## 1. Create `second_topic` with three partitions

```bash
kafka-topics --bootstrap-server localhost:9092 --topic second_topic --create --partitions 3 --replication-factor 1
```

Creates a topic with three partitions and replication factor 1.

## 2. Start a console consumer

```bash
kafka-console-consumer --bootstrap-server localhost:9092 --topic second_topic
```

Starts an interactive consumer that continuously reads new messages from `second_topic`.

By default, it reads messages from the current consumer position rather than replaying all existing messages.

Press `Ctrl + C` to exit.

## 3. Produce messages using Round Robin partitioning

Open another terminal and execute:

```bash
kafka-console-producer --bootstrap-server localhost:9092 --producer-property partitioner.class=org.apache.kafka.clients.producer.RoundRobinPartitioner --topic second_topic
```

Enter messages:

```text
message 1
message 2
message 3
message 4
message 5
message 6
```

Uses the `RoundRobinPartitioner` to distribute messages across available partitions in round-robin order.

**Note:** This example uses messages without keys. Keyed messages follow key-based partitioning behavior instead.

## 4. Consume messages from the beginning

```bash
kafka-console-consumer --bootstrap-server localhost:9092 --topic second_topic --from-beginning
```

Reads available messages starting from the earliest offset, including previously produced messages.

## 5. Display message metadata while consuming

```bash
kafka-console-consumer --bootstrap-server localhost:9092 --topic second_topic --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --property print.partition=true --from-beginning
```

Displays additional information for each consumed message:

- Timestamp
- Message key
- Message value
- Partition number

Example output format:

```text
CreateTime:...    Partition:0    Key:null    Value:message 1
CreateTime:...    Partition:1    Key:null    Value:message 2
CreateTime:...    Partition:2    Key:null    Value:message 3
```

Actual partition assignments and output formatting may vary.

---

## Key Concepts

| Concept | Meaning |
|---|---|
| Console Consumer | CLI tool used to read messages from Kafka topics |
| `--from-beginning` | Starts consuming from the earliest available offset |
| Consumer position | Offset from which a consumer reads messages |
| Round Robin Partitioner | Distributes unkeyed messages across partitions in round-robin order |
| `partitioner.class` | Configures the producer's partitioning strategy |
| `print.timestamp` | Displays message timestamps |
| `print.key` | Displays message keys |
| `print.value` | Displays message values |
| `print.partition` | Displays partition numbers |

## Practice Checklist

- [ ] Create `second_topic` with three partitions.
- [ ] Start a console consumer.
- [ ] Produce messages from another terminal.
- [ ] Observe messages arriving in the consumer.
- [ ] Stop the consumer using `Ctrl + C`.
- [ ] Restart the consumer using `--from-beginning`.
- [ ] Produce messages using `RoundRobinPartitioner`.
- [ ] Display timestamps, keys, values, and partition numbers.
- [ ] Verify partition distribution using Kafbat UI.

## Interview Questions

1. What is Kafka Console Consumer?
2. What is the difference between consuming normally and using `--from-beginning`?
3. What is a consumer offset?
4. What is Round Robin partitioning?
5. How does Kafka distribute messages across multiple partitions?
6. What is the purpose of `partitioner.class`?
7. How can you display message keys and values using the console consumer?
8. What is the purpose of `print.partition`?
9. Can multiple consumers read from the same topic?
10. What happens when a consumer stops and starts again?

## Summary

I learned how to consume Kafka messages using the console consumer, replay messages from the beginning, distribute messages across multiple partitions using Round Robin partitioning, and inspect message metadata including timestamps, keys, values, and partition numbers.