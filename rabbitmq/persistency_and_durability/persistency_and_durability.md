# Persistency and durability

## Persistency

Persistent message is persisted to disk. When broker restarts, persisted messages are recovered.

## Demo

1. Start RabbitMQ with Docker

```shell
docker run --name rabbitmq -d -p 15672:15672 5672:5672 rabbitmq:3-management
```

2. Create durable exchange and queue. Bind the exchange to the queue

3. Publish persistent and transient messages

```java
ConnectionFactory factory = new ConnectionFactory();
factory.setUsername("guest");
factory.setPassword("guest");
factory.setHost("localhost");
factory.setPort(5672);
Connection conn = null;
Channel channel = null;
try {
  conn = factory.newConnection();
  channel = conn.createChannel();
  String exchangeName = "Testing_durable_exchange";
  String routingKey = "testing";
  channel.exchangeDeclare(exchangeName, "direct", true);
  
  byte[] msg1 = "This is persistent message".getBytes();
  channel.basicPublish(exchangeName, routingKey, MessageProperties.PERSISTENT_TEXT_PLAIN, msg1);
  
  byte[] msg2 = "This is not persistent message".getBytes();
  channel.basicPublish(exchangeName, routingKey, null, msg2);
} finally {
  if (null != channel) {
    channel.close();
  }
  if (null != conn) {
    conn.close();
  }
}
```

4. Both persistent and transient message are published

![Image](https://raw.githubusercontent.com/franzwong/til/96c08bcf2f462669be265c00b745d4ed50a533e8/rabbitmq/persistency_and_durability/persistent_demo_1.png)

5. Restart RabbitMQ

```shell
docker stop rabbitmq
docker start rabbitmq
```

6. Only persistent message is recovered

![Image](https://raw.githubusercontent.com/franzwong/til/96c08bcf2f462669be265c00b745d4ed50a533e8/rabbitmq/persistency_and_durability/persistent_demo_2.png)

## Durability

When broker restarts, durable exchanges and queues are recovered.

## Demo

1. Start RabbitMQ with Docker

```shell
docker run --name rabbitmq -d -p 15672:15672 5672:5672 rabbitmq:3-management
```

2. Create durable queue and transient queue

![Image](https://raw.githubusercontent.com/franzwong/til/96c08bcf2f462669be265c00b745d4ed50a533e8/rabbitmq/persistency_and_durability/durable_demo_1.png)

3. Restart RabbitMQ

```shell
docker stop rabbitmq
docker start rabbitmq
```

4. Only durable queue is recovered

![Image](https://raw.githubusercontent.com/franzwong/til/96c08bcf2f462669be265c00b745d4ed50a533e8/rabbitmq/persistency_and_durability/durable_demo_2.png)

