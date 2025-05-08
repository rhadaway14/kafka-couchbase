START
docker-compose up -d

CONFIGURE TOPICS
docker exec -it scratch-kafka-1 kafka-topics --bootstrap-server localhost:9092 --create --topic couchbase-source-topic --partitions 1 --replication-factor 1
docker exec -it scratch-kafka-1 kafka-topics --bootstrap-server localhost:9092 --create --topic couchbase-sink-topic --partitions 1 --replication-factor 1

CONFIG COUCHBASE
user: Administrator
password: password
bucket: test
scope: _default
collection: _default

DEPLOY CONNECTORS
curl -X POST -H "Content-Type: application/json" --data @./configs/quickstart-couchbase-source.json http://localhost:8083/connectors
curl -X POST -H "Content-Type: application/json" --data @./configs/quickstart-couchbase-sink.json http://localhost:8083/connectors

TEST 
docker exec -it scratch-kafka-1 kafka-console-producer --bootstrap-server localhost:9092 --topic couchbase-sink-topic
{"id": "test123", "name": "Test Document", "description": "This is a test message for Couchbase."}

check UI