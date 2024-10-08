# kafka-demo
Demoing a containerized kafka cluster on docker / podman with docker compose

* Uses the official apache/kafka docker image, version 3.8.0
* Does not use zookeeper

## Starting the containers
* Create /opt/kafka1/data and ensure write permissions
* Create /opt/kafka2/data and ensure write permissions
* Create /opt/kafka3/data and ensure write permissions
* `podman compose up` or `docker compose up`

## Running commands within docker

Log into the container

```bash
podman exec -it kafka /bin/bash
```

Inside the container, kafka binaries are in `/opt/kafka/bin`. Create a topic:

```bash
cd /opt/kafka/bin

kafka-topics.sh --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

List topics:
```bash
kafka-topics.sh --list --bootstrap-server localhost:9092
```

Produce a message:
```bash
kafka-console-producer.sh --topic test-topic --bootstrap-server localhost:9092

```

Consume messages:
```bash
kafka-console-consumer.sh --topic test-topic --from-beginning --bootstrap-server localhost:9092

```


## Running commands from the host

Install Kafka CLI Tools from https://kafka.apache.org/downloads

Example for Windows:

```bash
kafka-topics.bat --list --bootstrap-server localhost:9092
```



## Testing 3-node cluster

create topic with replication factor of 3:

```bash
podman exec -it kafka1 /bin/bash
kafka-topics.sh --create --topic replicated-topic --bootstrap-server kafka1:9092 --partitions 3 --replication-factor 3
```

Create message:

```bash
kafka-console-producer.sh --topic replicated-topic --bootstrap-server kafka1:9092
```

Consume message:

```bash
kafka-console-consumer.sh --topic replicated-topic --from-beginning --bootstrap-server kafka1:9092
```


## Performance test

```bash
./kafka-producer-perf-test.sh --topic test-topic --num-records 100000 --record-size 512 --throughput -1 --producer-props bootstrap.servers=localhost:9092

```