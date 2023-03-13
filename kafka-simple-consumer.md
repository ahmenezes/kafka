# Connecting to Kafka Programmatically in Java

Your supermarket company is using Kafka to track and process purchase data to dynamically keep track of inventory changes. They have a topic called inventory purchases, data about the type, plus the number of items purchased that get published to this topic.

You have been asked to begin the process of writing a Java application that can consume this data. As an initial step, your current task is to write a simple Java program that can consume messages from the Kafka topic and print it to the console.

You can find a starter java project here: https://github.com/linuxacademy/content-ccdak-kafka-simple-consumer Clone the starter project into your home folder and implement the Kafka consumer inside the Main class.

This project includes a basic project framework and a Gradle build to allow you to compile run the code easily. From within the leading project directory, you can run the code in the Main class with the command ./gradlew run.

You can find some examples of Kafka consumer code in the Kafka Consumer API Javadocs. The GitHub starter project also includes an example solution in the end-state branch.

Do all of your work on the Broker 1 server. You can access the Kafka cluster from that server at localhost:9092.


```java

package com.linuxacademy.ccdak.kafkaSimpleConsumer;

import org.apache.kafka.clients.consumer.*;
import java.util.Properties;
import java.util.Arrays;
import java.time.Duration;

public class Main {

    public static void main(String[] args) {
        Properties props = new Properties();
        props.setProperty("bootstrap.servers", "localhost:9092");
        props.setProperty("group.id", "my-group");
        props.setProperty("enable.auto.commit", "true");
        props.setProperty("auto.commit.interval.ms", "1000");
        props.setProperty("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.setProperty("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Arrays.asList("inventory_purchases"));
        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
            for (ConsumerRecord<String, String> record : records) {
                System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
            }
        }
    }

}


```

