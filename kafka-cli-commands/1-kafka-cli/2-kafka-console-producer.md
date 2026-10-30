# Kafka CLI — Producing Messages

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

Prepend the following to standard Kafka CLI commands when executing them from the IntelliJ terminal:

```powershell
docker exec -it kafka-for-developer-broker
```

For non-interactive commands, `docker exec` without `-it` is sufficient.

**Note:** These exercises use `localhost:9092` because commands execute inside the Kafka container.

---

## 1. Create `first_topic`

```bash
kafka-topics --bootstrap-server localhost:9092 --topic first_topic --create --partitions 1 --replication-factor 1
```

Creates a topic with one partition and replication factor 1.

## 2. Start a console producer

```bash
kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic
```

Starts an interactive producer connected to `first_topic`.

Enter messages one by one:

```text
Hello World
My name is Conduktor
I love Kafka
```

Press `Ctrl + C` to exit the producer.

## 3. Produce messages with `acks=all`

```bash
kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic --producer-property acks=all
```

Enter messages:

```text
some message that is acked
just for fun
fun learning!
```

Configures the producer to wait for acknowledgements from all replicas in the in-sync replica set (ISR).

In this single-broker environment, only one replica is available.

## 4. Produce messages to a non-existing topic

```bash
kafka-console-producer --bootstrap-server localhost:9092 --topic new_topic
```

Enter:

```text
hello world!
```

Kafka can automatically create the topic if automatic topic creation is enabled.

## 5. List topics

```bash
kafka-topics --bootstrap-server localhost:9092 --list
```

Displays all topics available in the Kafka cluster.

## 6. Describe `new_topic`

```bash
kafka-topics --bootstrap-server localhost:9092 --topic new_topic --describe
```

Displays topic metadata, including partition count, leader, replicas, and ISR.

By default, the topic uses the broker's configured default partition count.

## 7. Configure the default partition count

Kafka's default partition count is configured using:

```properties
num.partitions=3
```

The setting is available in the Kafka broker configuration (`server.properties`).

For this Docker environment, configure the equivalent setting in Docker Compose:

```yaml
KAFKA_NUM_PARTITIONS: 3
```

Recreate the broker with the updated configuration for it to take effect.

**Note:** This setting applies to newly auto-created topics. It does not change the partition count of existing topics.

## 8. Produce to another non-existing topic

```bash
kafka-console-producer --bootstrap-server localhost:9092 --topic new_topic_2
```

Enter:

```text
hello again!
```

If automatic topic creation is enabled, Kafka creates `new_topic_2` using the configured default partition count.

## 9. Verify the new topic

List topics:

```bash
kafka-topics --bootstrap-server localhost:9092 --list
```

Describe `new_topic_2`:

```bash
kafka-topics --bootstrap-server localhost:9092 --topic new_topic_2 --describe
```

Expected configuration after setting `num.partitions=3`:

| Property | Value |
|---|---|
| Topic | `new_topic_2` |
| Partition count | 3 |
| Replication factor | 1 |

## 10. Produce messages with keys

```bash
kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic --property parse.key=true --property key.separator=:
```

Enter messages using the `key:value` format:

```text
example key:example value
name:Stephane
```

Kafka interprets the input as:

| Key | Value |
|---|---|
| `example key` | `example value` |
| `name` | `Stephane` |

The `parse.key=true` property enables key parsing.

The `key.separator=:` property defines the separator between the message key and value.

Messages with the same key are routed to the same partition when using the default key-based partitioning strategy.

---

## Key Concepts

| Concept | Meaning |
|---|---|
| Console Producer | CLI tool used to publish messages to Kafka topics |
| `acks=all` | Waits for acknowledgements from all replicas in the ISR |
| Automatic topic creation | Creates a topic when a producer sends to a non-existing topic, if enabled |
| `num.partitions` | Default partition count for newly auto-created topics |
| `parse.key` | Enables parsing message keys from producer input |
| `key.separator` | Separates the key and value in producer input |
| Message Key | Determines partition assignment under the default key-based partitioning strategy |

## Practice Checklist

- [ ] Create `first_topic` with one partition.
- [ ] Start a console producer.
- [ ] Produce multiple messages.
- [ ] Exit the producer using `Ctrl + C`.
- [ ] Produce messages using `acks=all`.
- [ ] Produce a message to non-existing topic `new_topic`.
- [ ] List and describe `new_topic`.
- [ ] Configure the default partition count to 3.
- [ ] Produce a message to `new_topic_2`.
- [ ] Verify that `new_topic_2` has three partitions.
- [ ] Produce messages with keys.
- [ ] Inspect topics and messages using Kafbat UI.

## Interview Questions

1. What is Kafka Console Producer?
2. What is the purpose of `acks=all`?
3. What happens when a producer sends a message to a non-existing topic?
4. What is automatic topic creation?
5. What does `num.partitions` configure?
6. Does changing `num.partitions` affect existing topics?
7. What is the purpose of a message key?
8. How does Kafka determine partition assignment using a message key?
9. What is the difference between producing messages with and without keys?
10. What do `parse.key` and `key.separator` do?

## Summary

I learned how to produce messages using Kafka Console Producer, configure producer acknowledgements, test automatic topic creation, configure default partitions, and publish messages with keys.