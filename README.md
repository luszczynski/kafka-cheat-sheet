# Kafka Cheat Sheet

First, set your kafka home environment.

KAFKA_BIN=/opt/kafka/bin
ZOOKEEPER_HOST=ip-zookeeper
BROKER_HOST=ip-broker

## Topic Operations

### List topics

```bash
$KAFKA_BIN/kafka-topics.sh \
    --list \
    --zookeeper $ZOOKEEPER_HOST:2181
```

### Describe topic

```bash
$KAFKA_BIN/kafka-topics.sh \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --topic first_topic \
    --describe
```

### Create topic

```bash
$KAFKA_BIN/kafka-topics.sh \
    --create \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --replication-factor 1 \
    --partitions 1 \
    --topic my-topic
```

### Alter topic

#### Alter retention time

```bash
$KAFKA_BIN/kafka-topics.sh \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --alter \
    --entity-type topics \
    --entity-name my-topic \
    --add-config retention.ms=1000
```

#### Delete retention time

```bash
$KAFKA_BIN/kafka-topics.sh \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --alter \
    --entity-type topics \
    --entity-name my-topic \
    --delete-config retention.ms
```

### List topics under-replicated

```bash
$KAFKA_BIN/kafka-topics.sh \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --describe \
    --under-replicated-partitions
```

### Delete topic

```bash
$KAFKA_BIN/kafka-topics.sh \
    --delete \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --topic my-topic
```

### Get earlist offset

```bash
$KAFKA_BIN/kafka-run-class.sh \
    kafka.tools.GetOffsetShell \
    --broker-list $BROKER_HOST:9092 \
    --topic mytopic \
    --time -2
```

### Get latest offset

```bash
$KAFKA_BIN/kafka-run-class.sh \
    kafka.tools.GetOffsetShell \
    --broker-list $BROKER_HOST:9092 \
    --topic mytopic \
    --time -1
```

## Partition Operations

### Add partitions

```bash
$KAFKA_BIN/kafka-topics.sh \
    --alter \
    --topic my-topic \
    --partitions 8
```

### Reassign partitions

```bash
$KAFKA_BIN/kafka-reassign-partitions.sh \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --reassignment-json-file increase-replication-factor.json  \
    --execute

$KAFKA_BIN/kafka-reassign-partitions.sh \
    --zookeeper $ZOOKEEPER_HOST:2181 \
    --reassignment-json-file increase-replication-factor.json  \
    --verify
```

## Consumer

### List consumer groups

```bash
$KAFKA_BIN/kafka-console-consumer.sh \
    --new-consumer \
    --list \
    --bootstrap-server $BROKER_HOST:9092
```

### Consuming message from the beginning

```bash
$KAFKA_BIN/kafka-console-consumer.sh \
    --bootstrap-server $BROKER_HOST:9092 \
    --topic my-topic \
    --from-beginning
```

### Read one message

```bash
$KAFKA_BIN/kafka-console-consumer.sh \
    --bootstrap-server $BROKER_HOST:9092 \
    --topic my-topic \
    --max-messages 1
```

### Read from __consumer_offsets

```bash
$KAFKA_BIN/kafka-console-consumer.sh \
    --bootstrap-server $BROKER_HOST:9092 \
    --topic __consumer_offsets \
    --formatter 'kafka.coordinator.GroupMetadataManager$OffsetsMessageFormatter' \
    --max-messages 1
```

### Consume using consumer group

```bash
$KAFKA_BIN/kafka-consumer-groups.sh \
    --topic my-topic \
    --new-consumer \
    --bootstrap-server $BROKER_HOST:9092 \
    --consumer-property group.id=my-group
```

### Topics to which group is subscribed

```bash
$KAFKA_BIN/kafka-consumer-groups \
    --bootstrap-server $BROKER_HOST:9092 \
    --group mygroup \
    --describe
```

### Reset offset for a consumer group in a topic

```bash
# There are many other resetting options
# --shift-by <positive_or_negative_integer> / --to-current / --to-latest / --to-offset <offset_integer>
# --to-datetime <datetime_string> --by-duration <duration_string>
$KAFKA_BIN/kafka-consumer-groups \
    --bootstrap-server $BROKER:9092 \
    --group <group_id> \
    --topic <topic_name> \
    --reset-offsets \
    --to-earliest \
    --execute
```

### Describe consumer group

```bash
$KAFKA_BIN/kafka-consumer-groups.sh \
    --bootstrap-server $BROKER_HOST:9092 \
    --describe \
    --group my-group
```

### Check offset for consumer group

```bash
$KAFKA_BIN/kafka-consumer-offset-checker.sh  \
    --zookeeper localhost:2181 \
    --group my-group \
    --topic my-topic
```

## Producer

### Send message using file

```bash
$KAFKA_BIN/kafka-console-producer.sh \
    --broker-list $BROKER_HOST:9092 \
    --topic my-topic < messages.txt
```

### Send message using standard input

```bash
$KAFKA_BIN/kafka-console-producer \
    --broker-list $BROKER_HOST:9092 \
    --topic my-topic
```

### Send message using string

```bash
echo "My Message" | $KAFKA_BIN/kafka-console-producer.sh \
    --broker-list $BROKER_HOST:9092 \
    --topic my-topic
```

## ACLs

```bash
$KAFKA_BIN/kafka-acls.sh \
    --authorizer-properties zookeeper.connect=$ZOOKEEPER_HOST:2181 \
    --add \
    --allow-principal User:Gus \
    --consumer \
    --topic my-topic \
    --group my-group
```

```bash
$KAFKA_BIN/kafka-acls.sh
    --authorizer-properties zookeeper.connect=$ZOOKEEPER_HOST:2181 \
    --add \
    --allow-principal User:Gus \
    --producer \
    --topic my-topic
```

### List topics ACLs

```bash
$KAFKA_BIN/kafka-acls.sh \
    --authorizer-properties zookeeper.connect=$ZOOKEEPER_HOST:2181 \
    --list \
    --topic my-topic
```
