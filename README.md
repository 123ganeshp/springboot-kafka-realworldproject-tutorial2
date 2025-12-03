# Spring Boot Kafka Wikimedia Real-World Project

This project is a real-time Kafka producer that streams live data from Wikimedia’s SSE (Server-Sent Events) endpoint and publishes it to a Kafka topic. It is built using Spring Boot, Apache Kafka, LaunchDarkly EventSource, and OkHttp.

---

## Project Structure

springboot-kafka-realworldproject
│
├── pom.xml (packaging = pom)
│
└── kafka-producer-wikimedia (Module)
├── pom.xml (packaging = jar)
├── src/main/java/com/kafka
│   ├── KafkaTopicConfig.java
│   ├── SpringBootApplicationProducer.java
│   ├── WikimediaChangesHandler.java
│   └── WikimediaChangesProducer.java
└── src/main/resources/application.properties

---

## Initial Setup

1. Created the main project using Spring Initializer.
2. Ran "git init" and made the initial commit.
3. Created a new branch: feature/kafka-real-world.
4. Developed all Kafka producer code inside this branch.
5. Converted the root project into a multi-module Maven project using:
   packaging = pom
6. Added a new module named kafka-producer-wikimedia using:
   packaging = jar

---

## Module Dependencies

Added the following dependencies in the module pom.xml:

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-kafka</artifactId>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>

<dependency>
    <groupId>com.launchdarkly</groupId>
    <artifactId>okhttp-eventsource</artifactId>
    <version>2.5.0</version>
</dependency>

<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
    <version>4.9.3</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.20.1</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.20.1</version>
</dependency>

---

## Kafka Producer Configuration

application.properties:

spring.kafka.producer.bootstrap-servers=localhost:9092  
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer  
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

---

## Kafka Topic Creation

@Bean  
public NewTopic topic() {  
return TopicBuilder.name("wikimedia_recentchange").build();  
}

---

## Wikimedia Handler (Event Handler)

The handler receives SSE event data and publishes it to Kafka:

kafkaTemplate.send(topic, messageEvent.getData());

---

## Real-Time Producer Logic

Created a custom OkHttp client for stable SSE streaming:

OkHttpClient client = new OkHttpClient.Builder()
.readTimeout(0, TimeUnit.MILLISECONDS)
.retryOnConnectionFailure(true)
.addInterceptor(chain -> chain.proceed(
chain.request().newBuilder()
.header("User-Agent", "Mozilla/5.0")
.build()))
.build();

EventSource configuration:

EventSource eventSource = new EventSource.Builder(handler, URI.create(url))
.client(client)
.build();

eventSource.start();

---

## Running the Producer

Run the module using:

mvn spring-boot:run

This will start streaming live Wikimedia updates and publish them to Kafka.

---

## Testing using Kafka Consumer

Open another terminal and run:

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic wikimedia_recentchange

You will see live events flowing from Wikimedia.

---

## Project Status

- Project created successfully
- Kafka producer implemented
- Real-time SSE streaming confirmed
- Messages validated in Kafka consumer
- Safe to merge into main branch

