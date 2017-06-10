Spring AMQP
===========

[http://docs.spring.io/spring-amqp/reference/htmlsingle](http://docs.spring.io/spring-amqp/reference/htmlsingle)

Uses a "template" as high-level abstraction for sending / receiving messages.

Configuration
-------------

See RabbitProperties [https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/amqp/RabbitProperties.html](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/amqp/RabbitProperties.html)

*NOTE*: ConfigurationProperties prefix of spring.rabbitmq, so in application.properties define spring.rabbitmq.xxx i.e. host.

Connections
-----------

Requires the following elements to connect to an Exchange.

-	ConnectionFactory
	-	can use RabbitConnectionFactoryBean to configure SSL
-	AmqpAdmin
	-	TopicExchange
	-	Listener
	-	Container
-	Queue
-	RabbitTemplate (this is a specialization of AmqpTemplate)

Uses the ConnectionFactory to build a Connection. This pools Channels. There is a SingleConnectionFactory available for UnitTests.

*NOTE*: in a clustered environment use the 'addresses' property.

Messages
--------

Represented by byte array and properties. Modelled by the Message class.

Boundary of sending/receiving message is RabbitTemplate.

*NOTE*: RabbitMessagingTemplate provides an integration with Spring Framework messaging.

### Sending

The template is configured with defaults, but can be overriden at runtime by the client (i.e. exchange / routing key etc)

Can use MessageBuilder / MessagePropertiesBuilder fluent-api to build messages.

?? To send a message, bind to a connection, send then unbind.

### Receiving

To receive a message either poll, or register a listener.

For polling, use `AmqpTemplate.receive()`.

For listening implement the MessageListener / ChannelAwareMessageListener interface. A Container is used to bridge AMQP queue to a MessageListener instance. This is a managed component (has a lifecycle).

Can use annotations to listen for messages.

```
@RabbitListener(queues = "myQueue")
public void processOrder(String data) {
    ...
}
```

*NOTE*: This will require @EnableRabbit on a configuration class to support @RabbitListener.

This will automatically create a MessageListenerContainer.

If there is a RabbitAdmin within the ApplicationContext, then queues can be declared and bound automaticaly with @RabbitListener.

Listener endpoints can have more than just String sent to them. Various parameters can be injected

-	the Message
-	the Channel
-	@Header specifying header to inject
-	@Headers
-	A non-annotated parameter that is not one of the above is considered to be the payload.

### Message Conversion

1.	a MessageConverter converts Spring AMQP message to spring-messaging Message. (By default uses SimpleMessageConverter)
2.	use a GenericMessageConverter to delegate conversion to an instance of DefaultFormattingConversionService.

Exchange
--------

Modelled by the Exchange interface. Depends on type (see ExchangeTypes enum)

An empty string ("") refers to the default exchange. Unless specified, all queues are bound to the default exchange.

Queue
-----

Modelled by the Queue class.

Bindings
--------

How Producer sends to an Exchange and Consumer receives from a Queue.

```
new Binding(someQueue, someDirectExchange, "foo.bar")
BindingBuilder.bind(someQueue).to(someTopicExchange).with("foo.*");
```

General
-------

Use AmqpAdmin implementation to manage Queues, Exchanges and Bindings to the broker.

Can use fluent api for building Queues / exchanges

```
@Bean
public Queue queue() {
    return QueueBuilder.nonDurable("foo")
        .autoDelete()
        .exclusive()
        .withArgument("foo", "bar")
        .build();
}

@Bean
public Exchange exchange() {
  return ExchangeBuilder.directExchange("foo")
      .autoDelete()
      .internal()
      .withArgument("foo", "bar")
      .build();
}
```
