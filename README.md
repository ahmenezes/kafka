# Kafka


## CCDAK Introduction

* Application Design

  * Building a Practice Cluster
   * To begin working with Kafka, I will proceed with the configuration of the infrastructure necessary to create a Kafka cluster. 
    We need 3 servers 
     - one larger, 2 small
     - distribution Ubuntu 18.04 Biobic Beaver LTS
       
       Since I'm using A Cloud Guru, I proceeded with the creation of 3 cloud servers with the suggested configurations. 
    
       We will use Confluent Community version. 
       Confluent is an entrerprise platform built on Apache Kafka. Essentially Confluent is Kafka with some enterprise features. Note: Kafka usually requires no more than **6Gb of JVM heapp space**.
       
       Confluent Manual Install  https://docs.confluent.io/platform/current/installation/installing_cp/zip-tar.html
      
      
      https://acloudguru-content-attachment-production.s3-accelerate.amazonaws.com/1597950605801-01_02_Building%20a%20Kafka%20Cluster.pdf
      
      
      ```console
      
           
      Solution
Begin by logging in to the lab servers using the credentials provided on the hands-on lab page:

ssh cloud_user@PUBLIC_IP_ADDRESS
Create a Kafka Topic for the Inventory Purchase Data

Create the topic using the kafka-topics command:
kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 3 --partitions 6 --topic inventory_purchases
Note: After the creation command is ran, we may encounter a warning indicating that we should simply avoid creating another topic with a '.' called inventory.purchases.

Test the Setup by Publishing and Consuming Some Data

Start a command line producer:
kafka-console-producer --broker-list localhost:9092 --topic inventory_purchases
Develop a few lines of data that can be used for testing purposes. Since we are working with merely test data, a specific format is not required. It could look like this:
product: apples, quantity: 5
product: lemons, quantity: 7
Once the test images are published, we can exit the producer.

Start up a command line consumer:

kafka-console-consumer --bootstrap-server localhost:9092 --topic inventory_purchases --from-beginning
Note: The --from-beginning flag is used because we want to target our test messages.

We should see the test messages that were published earlier:
product: apples, quantity: 5
product: lemons, quantity: 7


other helpful commands

to check status
sudo systemctl status confluent*

to start kafka
sudo systemctl start confluent-kafka

to check the topic creation
kafka-topics --bootstrap-server localhost:9092 --describe


	```

      
      
       
  * Kafka Architecture Basics
     Introduction and Everything you need to know about Kafka in 10 minutes https://kafka.apache.org/intro
     Main concepts https://kafka.apache.org/documentation/#intro_topics
     some of he terms more commonly used are:
      * **Topic**: A named data feed where data can be written to and read from 
      * **Log**: The data structure uded to store a topic's data. The log is a partitioned, immutable sequence of data records.
      * **Partition**: A section of a topic's log
      * **Offset**: The sequencial and unique ID of a data record within a partition
      * **Producer**: Something that writes data to a topic.
      * **Consumer**: Something that reads data from a topic.
      * **Consumer group**: A group of multiple consumers. Normally, multiple consumers can all consume the same record from a topic, but only one consumer in a consumer group will consume each record.

      * **Brokers**: The central component of Kafka architecture. Brokers are the servers that compose the Kafka cluster (one or more brokers). Producers and consumers communicate with brokers in order to publish and consume messages.
      * **zookeeper**: Kafka depends on zookeeper. Zookeper is a generalized cluster management tool. It manages the cluster and prvides a consistent, distributed place to store cluster configuration. Zookeper coordinates communication throughout the cluster, adds and removes bokers, and monitors the status of nodes in the cluster. It is often installed alongside Kafka, but can be maintained on a completely separate set of servers.
      * **Controler**: In a Kafka cluster, one broker is dynamically desinated as the Controler. The controler coordinates the process of assigning partitions and data replicas to nodes in the cluster. https://cwiki.apache.org/confluence/display/KAFKA/Kafka+Controller+Internals
      * **Replication**: Kafka is designed with fault tolerance in mind. As a result, it includes built-in support for replication. Replication means storing multiple copies of a given piece of data. In Kafka every topic is given a configurable replication factor. The replication factor is the number of replicas that will be kept on dofferent brokers for each partition in the topic.
     
     ```console
    
    cloud_user@5f72d1f4c61c:~$ kafka-topics --bootstrap-server localhost:9092 --create --topic my-topic --partitions 3 --replication-factor 2
    ```
    
     ```console
cloud_user@5f72d1f4c61c:~$ kafka-topics --bootstrap-server localhost:9092 --describe --topic my-topic
Topic:my-topic	PartitionCount:3	ReplicationFactor:2	Configs:segment.bytes=1073741824
	Topic: my-topic	Partition: 0	Leader: 3	Replicas: 3,1	Isr: 3,1
	Topic: my-topic	Partition: 1	Leader: 1	Replicas: 1,2	Isr: 1,2
	Topic: my-topic	Partition: 2	Leader: 2	Replicas: 2,3	Isr: 2,3
 
   	```
   
     note: you can notice on the output we have a new term, **Leader**
    
    * **Leaders**: In order to ensure that messages in a partition are kept in a consistent order across all replicas, Kafka chooses a leader for each partition. The leader handles all reads and writes for the partition. The leader is dynamically selected and if the leader goes down, the cluster attempts to choose a new leader througha process called **leader election**.
    * **In-Sync Replicas**: Kafka maintains a list of In-Sync Replicas (ISR) for each partition.
    ISRs are replicas that are up-to-date with the leader. If a leader dies, the new leader is elected from among the ISRs. By default, if there ane no remaining ISRs when a leader dies, Kafka waits until one becomes available. This means that producers will be on hold until a new leader can be elected. You can turn on **unclean leader election**, allowing the cluster to elect a non-in-sync replica in the scenario. In the shared output, we can see that my-topic partition 0, has replicas 3,1 and 3,1 are in-sync
   
     * The life of a message
      * **Producer** publishes a message to a **partition** within a **topic**.
      * The messager is added to the partition on the **leader**
      * The message **is copied to the replicas** of that partition on other brokers.
      * **Consumers** read the message and process it
      * When the **retention period** for the message is reached, the message is deleted (default 7 days).
   
   Lab exercise
   
   
   Solution
Begin by logging in to the lab servers using the credentials provided on the hands-on lab page:

ssh cloud_user@PUBLIC_IP_ADDRESS
Create a Kafka Topic for the Inventory Purchase Data

Create the topic using the kafka-topics command:
kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 3 --partitions 6 --topic inventory_purchases
Note: After the creation command is ran, we may encounter a warning indicating that we should simply avoid creating another topic with a '.' called inventory.purchases.

Test the Setup by Publishing and Consuming Some Data

Start a command line producer:
kafka-console-producer --broker-list localhost:9092 --topic inventory_purchases
Develop a few lines of data that can be used for testing purposes. Since we are working with merely test data, a specific format is not required. It could look like this:
product: apples, quantity: 5
product: lemons, quantity: 7
Once the test images are published, we can exit the producer.

Start up a command line consumer:

kafka-console-consumer --bootstrap-server localhost:9092 --topic inventory_purchases --from-beginning
Note: The --from-beginning flag is used because we want to target our test messages.

We should see the test messages that were published earlier:
product: apples, quantity: 5
product: lemons, quantity: 7
Conclusion
Congratulations - you've completed this hands-on lab!
   
   
   useful commands
   
   sudo systemctl start confluent-kafka
cloud_user@5f72d1f4c61c:~$ sudo systemctl status confluent*
    kafka-topics --bootstrap-server localhost:9092 --describe
    
    
  * Kafka and Java
  * Kafka Streams
  * Advanced Application Design Concepts






## This is the visualised result
Confluent Certified Developer for Apache Kafka (CCDAK)
and
https://docs.confluent.io/platform/current/tutorials/examples/clickstream/docs/index.html#clickstream-data-analysis-pipeline-using-ksqldb
