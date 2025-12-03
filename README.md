📘 Spring Boot Kafka Wikimedia Real-World Project
This project is a real-time Kafka producer that streams live data from Wikimedia’s SSE (Server-Sent Events) endpoint and
publishes it to a Kafka topic.
It is built using Spring Boot, Kafka, LaunchDarkly EventSource, and OkHttp.
🏗️ Project Structure
springboot-kafka-realworldproject
│
├── pom.xml                (packaging = pom)
│
└── kafka-producer-wikimedia (Module)
├── pom.xml            (packaging = jar)
├── src/main/java/com/kafka
│ ├── KafkaTopicConfig.java
│ ├── SpringBootApplicationProducer.java
│ ├── WikimediaChangesHandler.java
│ └── WikimediaChangesProducer.java
└── src/main/resources/application.properties
⚙️ Initial Setup
The main project was created using Spring Initializer, then:
git init
Initial commit
New branch created: feature/kafka-real-world
All development done inside this branch
Root project was converted to a multi-module setup:
<packaging>pom</packaging>
A new module kafka-producer-wikimedia was added with:
<packaging>jar</packaging>
📦 Module Dependencies
The following dependencies were added:
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
🔧 Kafka Producer Configuration
application.properties:
spring.kafka.producer.bootstrap-servers=localhost:9092
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
🧵 Kafka Topic Creation
@Bean
public NewTopic topic() {
return TopicBuilder.name("wikimedia_recentchange").build();
}
📡 Wikimedia Handler
WikimediaChangesHandler implements EventHandler and sends SSE event data to Kafka:
kafkaTemplate.send(topic, messageEvent.getData());
🌐 Real-Time Producer Logic
A custom OkHttp client is necessary for stable SSE streaming:
OkHttpClient client = new OkHttpClient.Builder()
.readTimeout(0, TimeUnit.MILLISECONDS)
.retryOnConnectionFailure(true)
.addInterceptor(chain -> chain.proceed(
chain.request().newBuilder()
.header("User-Agent", "Mozilla/5.0")
.build()))
.build();
EventSource setup:
EventSource eventSource = new EventSource.Builder(handler, URI.create(url))
.client(client)
.build();
eventSource.start();
🚀 Running the Producer
Start the Spring Boot application:
mvn spring-boot:run
The producer streams real-time Wikimedia changes and publishes them to Kafka.
🔍 Testing the Consumer
In a separate terminal:
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic wikimedia_recentchange
You should see live Wikimedia update events.
✅ Status
✔ Project created successfully
✔ Kafka producer working
✔ Real-time SSE streaming from Wikimedia
✔ Messages verified on Kafka consumer
✔ Ready to merge into main branch

