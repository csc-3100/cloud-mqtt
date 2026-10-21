# UML Class Diagram

This diagram illustrates the structure of the MQTT application using the CSC 3100 UML conventions.

- **Yellow:** Application classes.
- **Blue:** Java SDK and external library classes and interfaces.
- **Solid arrows:** Associations.
- **Dashed lines with hollow triangles:** Interface realizations.

```mermaid
classDiagram
    direction LR

    class Handler {
        -String BROKER_URL
        -String[] TOPICS
        +main(String[] args)$ void
    }

    class Publisher {
        -String broker
        -String[] topics
        +Publisher(String brokerUrl, String[] topics)
        +run() void
    }

    class Subscriber {
        -String broker
        -String[] topics
        +Subscriber(String brokerUrl, String[] topics)
        +run() void
        +connectionLost(Throwable cause) void
        +messageArrived(String topic, MqttMessage message) void
        +deliveryComplete(IMqttDeliveryToken token) void
    }

    class Runnable {
        <<interface>>
        +run() void
    }

    class MqttCallback {
        <<interface>>
        +connectionLost(Throwable cause) void
        +messageArrived(String topic, MqttMessage message) void
        +deliveryComplete(IMqttDeliveryToken token) void
    }

    class MqttClient {
        +connect() void
        +setCallback(MqttCallback callback) void
        +subscribe(String topic, int qos) void
        +publish(String topic, MqttMessage message) void
        +disconnect() void
    }

    %% Associations

    Handler --> Publisher
    Handler --> Subscriber

    Publisher --> MqttClient
    Subscriber --> MqttClient

    %% Interface realizations

    Publisher ..|> Runnable
    Subscriber ..|> Runnable
    Subscriber ..|> MqttCallback

    %% CSC 3100 UML colors

    classDef application fill:#FFF2B3,stroke:#555555,color:#222222
    classDef library fill:#DCEEFF,stroke:#555555,color:#222222

    class Handler,Publisher,Subscriber application
    class Runnable,MqttCallback,MqttClient library
```

## Design Notes

The `Handler` class starts the publisher and subscriber threads.

Both `Publisher` and `Subscriber` implement the Java `Runnable` interface, allowing them to execute concurrently.

The `Subscriber` also implements the Eclipse Paho `MqttCallback` interface to receive notifications when messages arrive or connections are lost.

Both classes use `MqttClient` to communicate with the MQTT broker.

The publisher and subscriber do not communicate directly. MQTT messages are exchanged through the broker.
