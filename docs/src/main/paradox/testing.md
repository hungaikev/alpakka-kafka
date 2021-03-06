# Testing

To simplify testing of streaming integrations with Alpakka Kafka, it provides the **Alpakka Kafka testkit**.

@@dependency [Maven,sbt,Gradle] {
  group=com.typesafe.akka
  artifact=akka-stream-kafka-testkit_$scala.binary.version$
  version=$version$
}

Note that Akka testkits do not promise binary compatibility. The API might be changed even between minor versions.


## Mocking the Consumer or Producer

The testkit contains factories to create the messages emitted by Consumer sources in `akka.kafka.testkit.ConsumerResultFactory` and Producer flows in `akka.kafka.testkit.ProducerResultFactory`.

To create the materialized value of Consumer sources, @scala[`akka.kafka.testkit.scaladsl.ConsumerControlFactory`]@java[`akka.kafka.testkit.javadsl.ConsumerControlFactory`] offers a wrapped `KillSwitch`.

Scala
: @@snip [snip](/tests/src/test/scala/docs/scaladsl/TestkitSamplesSpec.scala) { #factories }

Java
: @@snip [snip](/tests/src/test/java/docs/javadsl/TestkitSamplesTest.java) { #factories }


## Testing with an embedded Kafka server

To test the Alpakka Kafka connector the [Embedded Kafka library](https://github.com/manub/scalatest-embedded-kafka) is an important tool as it helps to easily start and stop Kafka brokers from test cases.

The testkit contains helper classes used by the tests in the Alpakka Kafka connector and may be used for other testing, as well.


### Testing from Java code

Test classes may extend `akka.kafka.testkit.javadsl.EmbeddedKafkaTest` to get access to 
* starting and stopping Kafka brokers (`startEmbeddedKafka(kafkaPort, replicationFactor)` and `stopEmbeddedKafka()`),
* preconfigured consumer settings (`ConsumerSettings<String, String> consumerDefaults`),
* preconfigured producer settings (`ProducerSettings<String, String> producerDefaults`), and
* unique topic creation (`createTopic(int number, int partitions, int replication)`).

The example below shows a skeleton test class for use with JUnit.

Java
: @@snip [snip](/tests/src/test/java/docs/javadsl/AssignmentTest.java) { #testkit }


### Testing from Scala code

The `KafkaSpec` class offers access to 
* preconfigured consumer settings (`ConsumerSettings<String, String> consumerDefaults`),
* preconfigured producer settings (`ProducerSettings<String, String> producerDefaults`),
* unique topic creation (`createTopic(number: Int = 0, partitions: Int = 1, replication: Int = 1)`),
* an implicit `LoggingAdapter` for use with the `log()` operator, and
* other goodies.

`EmbeddedKafkaLike` extends `KafkaSpec` to add automatic starting and stopping of the embedded Kafka broker.

Most Alpakka Kafka tests implemented in Scala use [Scalatest](http://www.scalatest.org/) with the mix-ins shown below.

Scala
: @@snip [snip](/tests/src/test/scala/akka/kafka/scaladsl/SpecBase.scala) { #testkit }


With this `SpecBase` class test classes can extend it to automatically start and stop a Kafka broker to test with.

Scala
: @@snip [snip](/tests/src/test/scala/docs/scaladsl/AssignmentSpec.scala) { #testkit }
