##### Alexis Bank kata:
https://github.com/apavlidi/Bank/tree/redis/src/main/java/infrastructure/redis

Alexis did the bank kata with Redis, Elastic and Kafka.

Looking through these implementations is useful for working on config for database setup. This was Alexis' Redis solution:
https://github.com/apavlidi/Bank/blob/redis/src/main/java/infrastructure/redis/RedisConnectionSingleton.java


#### This is the singleton class for redis connection:
```
package infrastructure.redis;

import config.RedisConstants;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class RedisConnectionSingleton {

  private static final Jedis jedis = establishConnection();

  private RedisConnectionSingleton() {
  }

  public static Jedis getInstance() {
    return jedis;
  }

  private static Jedis establishConnection() {
    JedisPoolConfig poolCfg = new JedisPoolConfig();
    JedisPool jedisPool = new JedisPool(poolCfg, RedisConstants.HOSTNAME, RedisConstants.PORT, RedisConstants.TIMEOUT,
        RedisConstants.PASSWORD, RedisConstants.SSL);
    return jedisPool.getResource();
  }

}
```

```
package infrastructure.redis;

public class RedisJsonSeriliazationException extends RuntimeException {

}
```
This is the repository using the Redis connection:
```

package infrastructure.redis;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import domain.Transaction;
import domain.TransactionFactory;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import repository.TransactionRepository;
import utils.Clock;

public class RedisTransactionRepository implements TransactionRepository {

  private Clock clock;
  private TransactionFactory transactionFactory;
  public static final String transactionKey = "transactions";

  public RedisTransactionRepository(Clock clock, TransactionFactory transactionFactory) {
    this.clock = clock;
    this.transactionFactory = transactionFactory;
  }

  @Override
  public void addDeposit(int amount) {
    Transaction transaction = transactionFactory.create(clock.getCurrentDate(), amount);
    try {
      RedisConnectionSingleton.getInstance().hset(transactionKey, String.valueOf(transaction.id),
          new ObjectMapper().writeValueAsString(transaction));
    } catch (JsonProcessingException e) {
      throw new RedisJsonSeriliazationException();
    }
  }

  @Override
  public void addWithdraw(int amount) {
    Transaction transaction = transactionFactory.create(clock.getCurrentDate(), -amount);
    try {
      RedisConnectionSingleton.getInstance().hset(transactionKey, String.valueOf(transaction.id),
          new ObjectMapper().writeValueAsString(transaction));
    } catch (JsonProcessingException e) {
      throw new RedisJsonSeriliazationException();
    }
  }

  @Override
  public List<Transaction> getAll() {
    ArrayList<Transaction> transactions = new ArrayList<>();
    Map<String, String> jsonTransactions = RedisConnectionSingleton.getInstance().hgetAll(transactionKey);

    for (String jsonTransaction : jsonTransactions.values()) {
      try {
        transactions.add(Transaction.createFromJson(jsonTransaction));
      } catch (IOException e) {
        e.printStackTrace();
      }
    }

    return transactions;
  }
}
```
Here we translate to JSON to translate to Redis.

This is the Consumer singleton for Kafka:
```
package infrastructure.kafka;

import config.KafkaConstants;
import java.util.Collections;
import java.util.Properties;
import org.apache.kafka.clients.consumer.Consumer;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.apache.kafka.common.serialization.StringSerializer;

public class ConsumerSingleton {

  private static final Consumer<Long, String> consumer = createProducer();

  private ConsumerSingleton(){
  }

  public static Consumer<Long, String> getInstance(){
    return consumer;
  }

  private static Consumer<Long, String> createProducer() {
    Properties props = new Properties();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, KafkaConstants.KAFKA_BROKERS);
    props.put(ConsumerConfig.GROUP_ID_CONFIG, KafkaConstants.GROUP_ID_CONFIG);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false");
    props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, KafkaConstants.MAX_POLL_RECORDS);
    Consumer<Long, String> consumer = new KafkaConsumer<>(props);
    consumer.subscribe(Collections.singletonList(KafkaConstants.TOPIC_NAME));
    return consumer;
  }
}
```
#### This is the KafkaTransaction repository:
```
package infrastructure.kafka;

import config.KafkaConstants;
import domain.Transaction;
import infrastructure.kafka.exceptions.KafkaSendException;
import java.time.Duration;
import java.time.temporal.ChronoUnit;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutionException;
import org.apache.kafka.clients.consumer.Consumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.producer.ProducerRecord;
import repository.TransactionRepository;
import utils.Clock;

public class KafkaTransactionRepository implements TransactionRepository {

  private static final int MAX_NO_MESSAGE_FOUND_COUNT = 100;

  private Clock clock;

  public KafkaTransactionRepository(Clock clock) {
    this.clock = clock;
  }

  @Override
  public void addDeposit(int amount) {
    ProducerRecord<String, String> record = new ProducerRecord<>(KafkaConstants.TOPIC_NAME,
        createTransactionEntry(amount));
    try {
      ProducerSingleton.getInstance().send(record).get();
    } catch (InterruptedException | ExecutionException e) {
      throw new KafkaSendException();
    }

  }

  @Override
  public void addWithdraw(int amount) {
    try {
      ProducerRecord<String, String> record = new ProducerRecord<>(KafkaConstants.TOPIC_NAME,
          createTransactionEntry(-amount));
      ProducerSingleton.getInstance().send(record).get();
    } catch (ExecutionException | InterruptedException e) {
      throw new KafkaSendException();
    }

  }

  @Override
  public List<Transaction> getAll() {
    List<Transaction> transactions = new ArrayList<>();
    int noMessageFound = 0;

    do {
      ConsumerRecords<Long, String> consumerRecords = ConsumerSingleton.getInstance().poll(Duration.of(10, ChronoUnit.MILLIS));
      if (consumerRecords.isEmpty()) {
        noMessageFound++;
      } else {
        appendTransaction(transactions, consumerRecords);
      }
    } while (noMessageFound <= MAX_NO_MESSAGE_FOUND_COUNT);

    ConsumerSingleton.getInstance().commitAsync();
    return transactions;
  }

  private void appendTransaction(List<Transaction> transactions, ConsumerRecords<Long, String> consumerRecords) {
    consumerRecords.forEach(record -> {
      String[] split = record.value().split(" \\|\\| ");
      transactions.add(new Transaction(split[0], Integer.parseInt(split[1])));
    });
  }

  private String createTransactionEntry(int amount) {
    return clock.getCurrentDate() + " || " + amount;
  }
}
```
#### This is the Producer singleton:
```
package infrastructure.kafka;

import config.KafkaConstants;
import java.util.Properties;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;

public class ProducerSingleton {

  private static final Producer<String, String> producer = createProducer();

  private ProducerSingleton(){
  }

  public static Producer<String, String> getInstance(){
    return producer;
  }

  private static Producer<String, String> createProducer() {
    Properties props = new Properties();
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, KafkaConstants.KAFKA_BROKERS);
    props.put(ProducerConfig.CLIENT_ID_CONFIG, KafkaConstants.CLIENT_ID);
    props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
    props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
    return new KafkaProducer<>(props);
  }
}
```

#### This is LocalDateTime example:
https://stackoverflow.com/questions/30374796/how-can-i-add-an-int-minutes-to-localdatetime-now
```
Do you mean to say the system's date and time? – Blip May 21 '15 at 13:13
6
LocalDateTime fiveMinutesLater = LocalDateTime.now().plusMinutes(5) ? Assuming that LocalDateTime is from the Joda framework. – Henrik Aasted Sørensen May 21 '15 at 13:14 
```