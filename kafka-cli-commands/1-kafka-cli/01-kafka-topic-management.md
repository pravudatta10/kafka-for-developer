# Kafka CLI — Topic Management

## Environment Setup

These notes use Kafka running in Docker on Windows, with IntelliJ IDEA as the development environment.

| Setting | Value |
|---|---|
| Operating system | Windows |
| IDE | IntelliJ IDEA |
| Kafka container | `kafka-for-developer-broker` |
| Kafka bootstrap server inside container | `localhost:9092` |
| Kafka bootstrap server from Windows | `localhost:19092` |
| Kafka UI | http://localhost:9999 |

### How to execute commands

The Kafka CLI commands in this document are written in their standard form.

Because Kafka runs inside Docker, prepend the following to each command when executing it from the IntelliJ terminal:

```powershell
docker exec -it kafka-for-developer-broker
```

For commands that don't require interactive input, `docker exec` without `-it` is also fine.

**Example**

Standard Kafka command:

```bash
kafka-topics --bootstrap-server localhost:9092 --list
```

Command for my Docker environment:

```powershell
docker exec kafka-for-developer-broker kafka-topics --bootstrap-server localhost:9092 --list
```

---

## 1. List topics

```bash
kafka-topics --bootstrap-server localhost:9092 --list
```

Lists all topics in the Kafka cluster.

## 2. Create `first_topic`

```bash
kafka-topics --bootstrap-server localhost:9092 --topic first_topic --create
```

Creates a topic using the broker's default partition count and replication factor.

## 3. Create `second_topic` with three partitions

```bash
kafka-topics --bootstrap-server localhost:9092 --topic second_topic --create --partitions 3
```

Creates a topic with three partitions.

## 4. Create `third_topic` with replication factor 2

```bash
kafka-topics --bootstrap-server localhost:9092 --topic third_topic --create --partitions 3 --replication-factor 2
```

In my single-broker environment, this command is expected to fail because replication factor 2 requires at least two suitable brokers.

## 5. Create `third_topic` with replication factor 1

```bash
kafka-topics --bootstrap-server localhost:9092 --topic third_topic --create --partitions 3 --replication-factor 1
```

Creates the topic with three partitions and one replica per partition. This is suitable for my single-broker learning setup.

## 6. List topics again

```bash
kafka-topics --bootstrap-server localhost:9092 --list
```

Displays all available topics.

## 7. Describe `first_topic`

```bash
kafka-topics --bootstrap-server localhost:9092 --topic first_topic --describe
```

Displays topic metadata, including partitions, leaders, replicas, and in-sync replicas (ISR).

## 8. Delete `first_topic`

```bash
kafka-topics --bootstrap-server localhost:9092 --topic first_topic --delete
```

Requests deletion of the topic and its records. Do not execute this command if you want to preserve your practice data.

---

## Key Concepts

| Concept | Meaning |
|---|---|
| Topic | A named stream of records |
| Partition | An ordered log within a topic |
| Replication factor | Number of replicas per partition |
| Leader | Replica responsible for handling reads and writes |
| ISR | In-sync replicas |
| Bootstrap server | Address used by a client to connect to Kafka |

## Practice Checklist

- [ ] List topics.
- [ ] Create `first_topic`.
- [ ] Create `second_topic` with three partitions.
- [ ] Test replication factor 2 and observe the error.
- [ ] Create `third_topic` with replication factor 1.
- [ ] Describe `first_topic`.
- [ ] Inspect topics in Kafbat UI.

## Interview Questions

1. What is a Kafka topic?
2. What is the difference between a topic and a partition?
3. Why does Kafka use partitions?
4. What is a replication factor?
5. Why does replication factor 2 fail with one broker?
6. What are a partition leader and ISR?
7. What does the `--describe` command show?
8. What happens when a topic is deleted?

## Summary

I learned how to list, create, describe, and delete Kafka topics, and how partition counts and replication factors affect topic configuration.
