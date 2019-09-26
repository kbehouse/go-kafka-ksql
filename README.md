# Golang with Kafka and KSQL

Modify from https://kafka-tutorials.confluent.io/join-a-stream-to-a-table/ksql.html

Test KSQL **Join** Function using **Golang** producer and consumer

## Description

3 topics: *movies*, *ratings*, and *rated_movies* (see statements_json.sql)

rated_movies **join** movies and ratings topics, so rated_movies will wait the 2 topics have data

## Install

Install [librdkafka](https://github.com/edenhill/librdkafka)

## Run

Run docker-compose

```
docker-compose up
```

### Create Topics

Create topics: movies, ratings, and rated_movies

```
statements=$(< ./statements_json.sql) && \
    echo '{"ksql":"'$statements'", "streamsProperties": {}}' | \
        curl -X "POST" "http://localhost:8088/ksql" \
             -H "Content-Type: application/vnd.ksql.v1+json; charset=utf-8" \
             -d @- | \
        jq
```

check topic list

```
docker exec -it broker kafka-topics --list --bootstrap-server localhost:9092
```

### Echo Topics 

```
go run topic_echo.go -t movies
go run topic_echo.go -t ratings
go run topic_echo.go -t rated_movies
```

### Producer

Check only data in movies topic, none in rated_movies

```
go run producer_movies.go
```

See **join** result, data in ratings  and rated_movies topic

```
go run producer_ratings.go
```