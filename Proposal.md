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


