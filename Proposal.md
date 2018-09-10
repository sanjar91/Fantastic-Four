Fantastic Four
CYBR 8420
Project Proposal

1. What are the security needs of users from this software in its intended threat environment (e.g., home, office, enterprise, bank, government, etc.)? If there are none or very few, then re-evaluate your selection.
2. Develop a list of security features in the software. Again, if there are none or very few, then re-evaluate your choice.
3. Motivation for selecting this project
4. Open source project description (What is it?, Contributors, Activity, Use, Popularity, Languages used, platform, documentation sources, etc.)
5. Discuss License, procedures for making contributions, and contributor agreements
6. Summary of security-related history (E.g., known vulnerabilities, security-related engineering decisions, security feature additions/removal, etc. )
7. Link to your team GitHub repository that shows your internal project task assignments and collaborations to finish this task.


### 1. Security needs of users in the intended threat environment

#### Publisher Security Needs
* The "publisher" user-type of this software needs to be able to securely transmit a message that will simultaneously reach multiple users.
* The publisher needs to be able to verify the authenticity of the broker/topic they are publishing to, and the authenticity of the users receiving the messages the publisher transmits
* The publisher needs to be able to send messages to the broker to be published without the message being intercepted by a 3rd party
* The publisher needs to receive a status from the broker that their message was successfully published

#### Subscriber Security Needs
* The "subscriber" user-type of this software needs to be able to be able to request to subscribe or unsubscribe to a topic or channel. They should then receive confirmation that they are subscribed or unsubsribed to said topic.
* The subscriber needs to be able to verify the authenticity of the messages they receive from the topic and publisher they are subscribed to.
* The subscriber should be able to verify their connection to the message broker, and if the connection is lost or changed, a log of that connection issue should be kept on the subscriber's side.
* Requests from the subscriber (whether subscription requests, publishing requests, or any other type of request) should in no way impact the status of the publisher or the broker.

#### Broker Security Needs
* The broker should support connections to itself and the publishers/subscribers over secure transport encryptions such as SSL/TLS.
* The broker should support the ability for encryption of the payload sent in messages along with encryption of the transport for the payload.
* The broker needs to be able to authenticate at the user level (publisher/subscriber) and at the device level (IoT device, phone, desktop, etc).
* Permission levels need to be set on the broker to limit what a subscriber has the ability to do versus what a publisher has the ability to do. This includes instances where a publisher of one topic is a subscriber of a different topic.
* The broker should have the ability to limit the amount of requests that are sent from a single device or user to prevent possible network attacks from bringing the broker down.
* Requests from publishers and subscribers should in no way impact the status of the broker.

### 3. Motivation for selecting this project
Mosquitto is a lightweight broker and Client that uses the MQTT protocol for messaging. MQTT is a very popular protocol for the Internet of Things movement, and enables the ability to message thousands of machines at the same time in a publication/subscription format.
While this makes it very easy to enable machine to machine messaging and other core pieces of IoT, it also brings a greater threat impact and wider threat target to keeping IoT secure. With [DDOS attacks using IoT](https://www.corero.com/blog/870-the-rise-of-iot-botnet-threats-and-ddos-attacks.html) on the rise, the movement
is starting to attract a very negative attitude in the cybersecurity world, which was part of our motivation for selecting this project.

Mosquitto is also used in several large scale open source projects that deal specifically with personal location tracking. The [OwnTracks project](https://owntracks.org/booklet/), a project that provides applications for reporting your phone's location to your own private servers,
  uses Mosquitto exclusively in their applications and lists Mosquitto as their recommended broker in their [documentation](https://owntracks.org/booklet/guide/broker/). Another IoT project, [ThingsBoard](https://thingsboard.io/), also lists Mosquitto as a recommended client and broker for pushing data through [MQTT published messages](https://thingsboard.io/docs/getting-started-guides/helloworld/).
Due to the sensitivity of the information transmitted from projects such as OwnTracks and ThingsBoard, transmitting secure messages to thousands of devices is imperative, and Mosquitto makes for a great starting point in assuring that data can be transmitted securely. 


