###### Fantastic Four
###### CYBR 8420
###### Project Requirements

### 0. Make sure you have a back story. Where is this system being used? What is its criticality? Who are the stakeholders?
Mosquitto is a message broker that uses the MQTT protocol for publication/subscribtion style messaging. This protocol and format of messaging was designed for connecting to a wide array of devices that might have weak signals or can't store messages that are in a larger format, such as IoT devices, embedded systems, or GPS devices.

One of the more common scenarios where mosquitto is implemented is in package delivery and fleet management. An open source Software As A Service Provider, [Thingsboard](https://thingsboard.io/fleet-tracking/) uses the MQTT protocol and recommends the mosquitto MQTT clients for it's fleet tracking and fleet management software. using the pub/sub style messaging, delivery trucks in the fleet can publish their location, mileage, and subscribe to updates on the orders they are driving to complete. The scenario we used to generate our use cases, data flows and misuse cases is for a package delivery company that uses mosquitto to broadcast messages to the drivers and track the driver's status as they complete orders.

The stakeholders in this scenario are primarily the drivers, who want to stay up to date as to the location and condition of the orders they are filling. They also wish to be able to notify their management headquarters when they either complete a delivery, or if there is a problem and they need assistance. The stakeholders are also the management team who need to notify the drivers of any state changes to their orders, and who need to monitor the activity of the individual members of their fleet. Depending on the package delivery system, and it's customer base, you could argue that the consumers who's items are delivered through this delivery system are also stakeholders, in that they wish to check the status of their package being delivered, and need to have the ability to possibly change the delivery location or cancel the order.

### Data Flows: Use Cases & Misuse Cases

#### 1. Creating and Canceling Orders

**Context** 

Bob the delivery office admin, needs to communicate to the delivery truck driver in real-time where the delivery driver's next delivery location will be.  The first task required in this process is that Bob needs to create a delivery order in the system.  Each order should include the logistical information needed for the delivery driver to successfully make the delivery of that order.  Bob would create a delivery order in the system.  One of the actions that occurs when Bob creates this delivery order in the system is that it "publishes" the delivery order to the broker so that the broker is then able to push out that delivery order to the delivery driver.

Likewise, sometimes the delivery driver needs to be informed in real-time that his current delivery order is canceled so he knows to no longer deliver the current order.  Bob the delivery office admin would cancel an order in the system.  One of the actions that occurs when Bob cancels a delivery order is that it "publishes" the delivery order to the broker, so that the broker is then able to push out the "canceled order" message to the delivery driver.

**MisUse Case**

Blaze the Hacker wants to steal a delivery package.  One attack vector Blaze could use is to intercept the order data as it is in transit from Bob the delivery office admin's system to the broker.  Bob's system would not be publishing a message to the broker because Blaze is intercepting the published message.  Instead, Blaze the hacker would alter the logistical information in that message, and then submit the altered version of the order to the broker.  Ultimately, Blaze would be the one that is actually publishing a message to the broker and not Bob.

**Diagram**

[![data_flow_1](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case_1_Dataflow_small.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case_1_Dataflow_small.png)

**Security Requirement**

When a message is published to the broker, such as a delivery order to the delivery driver, the broker should confirm that the message received from Bob the delivery office admin is truly a message from Bob, and that Bob's message has not been altered in any way.

**Relevant Advertised Security Features of Mosquitto**

The relevant security feature methods associated to this security requirement are located in the [libmosquitto man page](https://mosquitto.org/man/libmosquitto-3.html). Additionally, configuration is required and described in the [mosquitto-conf man page](https://mosquitto.org/man/mosquitto-conf-5.html), and the [mosquitto_passwd](https://mosquitto.org/man/mosquitto_passwd-1.html) tool is available for setting up usernames and passwords. For the broker to authenticate that the message received is unaltered and from Bob, a combination of authentication and network-based encryption options must be specified in the mosquitto.conf file. For encryption, it is important to note that encryption is turned off by default. SSL/TLS options must be specified in the configuration file to make use of encryption.  Username and password authentication are a part of the protocol for mosquitto.  It is important to use network-based encryption if the user is defining the username and passwords over a network so that this critical data is not intercepted.

