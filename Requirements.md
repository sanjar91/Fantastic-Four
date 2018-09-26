###### Fantastic Four
###### CYBR 8420
###### Project Requirements

# Requirements

### Backstory
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

#### 3. Creating Order Log

**Use Case**

Jake, the broker admin, creates and publishes an order log every morning. The order log contains the list of delivery orders and schedules. Additionally, Bob can update the log by publishing or un-publishing orders. Other actors such as, Bradley (delivery driver from Use Case 2) and Henry (delivery driver from Use Case 5) also have access to the order log. The delivery drivers can view, complete, or cancel orders; and the order log is update based on the completed and/or canceled orders.


**MisUse Case**

LaZer, the hacker, is bored and has nothing better to do with his life so he decided to sabotage the fleet management system. After his preliminary research, LaZer realizes that the order log is an important entity in this company, because all the other actors and actions rely on the order log. LaZer decides to perpetrate the order log with a Denial of Service (DoS) attack. LaZer overwhelms the order log with illegitimate superfluous requests which causes an overload and prevents the legitimate requests from being fulfilled. This attack will disrupt or possibly shutdown the company’s delivery operations. 

[![data_flow_3](https://github.com/sanjar91/Fantastic-Four/blob/master/images/UseCase-3-dataflow.png)

#### 4. Creating User Credentials

**Use Case**

Jake, the System Admin, needs to authenticate the devices and users that are connecting to the broker, and block connection attempts that don't have valid credentials. In order to complete this, Jake first creates a password file that contains usernames and passwords for accessing the broker. Jake then uploads the credential file to a folder the broker can access and updates the config to enable user authentication.

**MisUse Case**

Blaze, the hacker, needs to authenticate himself with the broker so that he can monitor published messages. One attack vector Blaze could use is to rename the credential file uploaded for the broker to access, and upload a fake credential file with the same naming conventions as the original file with usernames and passwords that Blaze wrote for the broker to use for authentication.

Another attack vector Blaze could use is to change the file permissions of the credential file so that he can write to the file and add his own credentials to the file.

**Diagram**

[![data_flow_4](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Use_Case_4_Dataflow_small.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Use_Case_4_Dataflow_small.png)

**Security Requirement**

When using usernames and passwords for authentication, permissions need to be set within the broker for what level users can change the requirements and configuration of credential files. Changes to active credential files need to be logged as well, and the file should be in a location on the machine hosting the broker that can only be accessed by the broker and system administrators.

**Security Features of Mosquitto**

Security features offered by Mosquitto that address these requirements are located in the [mosquitto.conf documentation](https://mosquitto.org/man/mosquitto-conf-5.html) under the Authentication Headline.
User Authentication in Mosquitto is handled through traditional user credentials that can be defined either in credential files, or can be adapted through additional external modules and plugins. A few of the plugins [recommended by mosquitto](https://mosquitto.org/blog/2013/07/authentication-plugins/) include using authentication based on md5 hashes, and credential servers using postgresql and redis.

Mosquitto also supports Access Control Lists to control client access to topics on the broker. The ACL is set to only provide access to topics listed, which would prevent additonal topics from being created.

Mosquitto has two types of logging, System status logs and informational/debugging logs. System status logs are part of the MQTT protocol and are logged automatically to the $SYS topic that can be subscribed to on the broker. The informational/debugging logs 
are more specific to Mosquitto and can be set to be published to a topic, to print to the console, or to write to a file. At this time, admin changes to ACLs or credential files are not logged to the System Status log, and it is unknown whether they are logged to the informational/debugging log destination.

#### 5. Creating and Canceling Orders

**Context** 

Henry the Honest Delivery Driver checks his IoT device for the delivery information on his next destination.  Each time he makes a delivery, he publishes a message to the broker that the delivery was made, and then proceeds to fulfill the next delivery order.  Sometimes Henry is unable to deliver the package because no one is home, so he publishes a "canceled delivery" order, and then proceeds on to the next order.

**MisUse Case**

Judas the Disgruntled employee is very unhappy in his job and recently received a poor performance review.  Judas is also upset at Henry because Henry tends to make Judas look bad.  In order to get back and Henry and cause chaos at the workplace, Judas plans on sabbotaging Henry's deliveries by canceling a number of his orders, and Judas is confident that these actions will not be able to be traced back to him.  Each time Judas cancels an order, it publishes a message to the broker which is then sent out to Henry, telling him that his current order is canceled and should not be delivered. 

**Diagram**

[![data_flow_5](https://github.com/sanjar91/Fantastic-Four/blob/master/images/user_case_5_dataflow_small.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/user_case_5_dataflow_small.png)

**Security Requirement**

Delivery drivers should be authenticated in the system so that their identify and actions are known.  Also, authorization mechanisms should be in place so that it is controlled which topics delivery drivers are able to publish/subscribe to.  


**Relevant Advertised Security Features of Mosquitto**

Authentication is built into the system, though it requires configuration in the [mosquitto-conf file](https://mosquitto.org/man/mosquitto-conf-5.html).  Authorization is built into the system, and requires a system administrator to enter the authorization rules in an access control list file.    

### Observation of Security-Related Configuration and Installation Issues

All Mosquitto configurations are located in the [mosquitto.conf](https://mosquitto.org/man/mosquitto-conf-5.html) file. The location of this critical file varies depending on the operating system.  For a mac it is located in the **/usr/local/opt/mosquitto/** directory.  The entire contents of the mosquitto.conf file are commented out, though the comments do specify all the commands associated to the default configurations.  

The documentation makes clear that the majority of the security features are disabled out of the box. A couple security-related configurations that stand out, which are disabled by default, are the following:

* No maximum publish payload size is configured.  
* The connections for logged in users never expire.  
* There are no maximum number of client connections specified.  
* SSL/TLS is turned off
* Authentication by publishers/subscribers is optional
* No authorization rules exist for authenticated or unauthenticated users
* All users have full access to publish as many topics as desired
* All users have full access to subscribe to all topics
* All users have full access to meta data related about the broker

Due to the lack of default security configurations, the creators of Mosquitto are placing much of the security-related responsibility on the system administrator. It is also up to the system administrator to setup SSL/TLS if they hope to encrypt communications.

