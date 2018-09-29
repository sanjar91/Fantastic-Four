###### Fantastic Four
###### CYBR 8420
###### Project Requirements

# Requirements

### Backstory
Mosquitto is a message broker that uses the MQTT protocol for publication/subscribtion style messaging. This protocol and format of messaging was designed for connecting to a wide array of devices that might have weak signals or can't store messages that are in a larger format, such as IoT devices, embedded systems, or GPS devices.

One of the more common scenarios where mosquitto is implemented is in package delivery and fleet management. An open source Software As A Service Provider, [Thingsboard](https://thingsboard.io/fleet-tracking/) uses the MQTT protocol and recommends the mosquitto MQTT clients for it's fleet tracking and fleet management software. using the pub/sub style messaging, delivery trucks in the fleet can publish their location, mileage, and subscribe to updates on the orders they are driving to complete. The scenario we used to generate our use cases, data flows and misuse cases is for a package delivery company that uses mosquitto to broadcast messages to the drivers and track the driver's status as they complete orders.

The stakeholders in this scenario are primarily the drivers, who want to stay up to date as to the location and condition of the orders they are filling. They also wish to be able to notify their management headquarters when they either complete a delivery, or if there is a problem and they need assistance. The stakeholders are also the management team who need to notify the drivers of any state changes to their orders, and who need to monitor the activity of the individual members of their fleet. Depending on the package delivery system, and it's customer base, you could argue that the consumers who's items are delivered through this delivery system are also stakeholders, in that they wish to check the status of their package being delivered, and need to have the ability to possibly change the delivery location or cancel the order.

### Data Flows: Use Cases & Misuse Cases

#### 1.1 

**Use Case** 

Bob the delivery office admin, needs to communicate to the delivery truck driver in real-time where the delivery driver's next delivery location will be.  The first task required in this process is that Bob needs to create a delivery order in the system.  Each order should include the logistical information needed for the delivery driver to successfully make the delivery of that order.  Bob would create a delivery order in the system.  One of the actions that occurs when Bob creates this delivery order in the system is that it "publishes" the delivery order to the broker so that the broker is then able to push out that delivery order to the delivery driver.

Likewise, sometimes the delivery driver needs to be informed in real-time that his current delivery order is canceled so he knows to no longer deliver the current order.  Bob the delivery office admin would cancel an order in the system.  One of the actions that occurs when Bob cancels a delivery order is that it "publishes" the delivery order to the broker, so that the broker is then able to push out the "canceled order" message to the delivery driver.  
  
**MisUse Case**

It is important to note that the two main commands in Mosquitto is that you either "publish" messages, or you "subscribe" to messages.  Both the "create orders" and "cancel orders" would ultimately be sending publish commands.

Blaze the Hacker wants to steal a delivery package.  One way he can do this is to cancel a legitimate order, and then create the same order but with a different address.

**Prevention**

To prevent this misuse case, authentication should be present in mosquitto.  That is: only authenticated users should be able to create and cancel orders.

**MisUse Case Evolved**

Blaze the Hacker would need to get around the authentication, and one common attack again authenication is dictionary attacks.

**Prevention Evolved**

To mitigate a dictionary attack, a common strategy is duel factor authentication. That is: confirming the user's identify by more than one method.  This way: an attacker would have to spoof both forms of authentication.

**Diagram**

[![data_flow_1](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case1_small.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case_1.png)

**Relevant Advertised Security Features of Mosquitto**

There are various methods available to authenticate the user, and more than one method can be required.  This is described in the  [mosquitto.conf man page](https://mosquitto.org/man/mosquitto-conf-5.html) in the **Authentication** section. 


#### 1.2

**Use Case** 

This use case is closely related to the use case described in #1, but it is from the perspective of the delivery driver.  Henry the honest delivery driver "subscribes" to the delivery orders that Bob the Office Admin "publishes."  In this way, Bob has the delivery information needed to make deliveries, and will also be informed when deliveries are canceled. 

**Misuse Case**

The intent of the attacker is different than in #1, but the attack is the same.  In this scenario, the attacker is a disgruntled driver, and for the sole purpose of sabotaging operations: Judas the disgruntled delivery driver publishes messages that say deliveries are canceled when they truly are not. 

**Prevention**

The prevention is the same as in #1: authentication.

**Misuse Case Evolved** 

The misuse case evolved is the same as in #1: perform a dictionary attack.

**Prevention Evolved**

#1 described duel factor authentication as a mitigation technique.  Another technique that could be used that would mitigate a dictionary attack is for mosquitto to require strong passwords based off of well defined criteria. 

**Diagram**

[![data_flow_2](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case2_small.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case_2.png)

**Relevant Advertised Security Features of Mosquitto**

There exists the utility [mosquitto_passwd](https://mosquitto.org/man/mosquitto_passwd-1.html) which assists with managing password files. One is able to create a user/password combination to be added into the password file, but no available options exist which require the password to be a strong password.  


#### 2.

**Use Case / Misuse Case**

This data flow can be described as a combination of the first and second dataflows described above.  In this scenario, Judas the disgruntled employee's goal is not to cause chaos for the business, but instead his goal is to steal delivery packages.  One way he could do this is to steal packages located on a delivery truck that is not assigned to him, and in the system mark the delivery of those packages as complete.

**Prevention**
 
 Prevention of this attack is to incorporate authorization into the system (which assumes that authentication is already incorporated in the system from data flows #1 and #2).  This way, system administrators will be able to tell who marked the delivery of a package as complete, so if a customer complains that they never received their package, the logs will be able to show that Judas the disgruntled employee is the one that marked it as completed and not Henry the honest delivery driver.

**Misuse Case Evolved**

Judas might be able to find a way around this if he discovers that the authorization file was not configured correctly, or if he discovers a loophole in the rules defined in the authorization.

**Prevention Case Evolved**

The system should have a procedure in place which prevents errors when setting up or modifying the authorization policies.  This could come in the form of a utility, much like mosquitto's mosquitto_passwd utility. 

**Diagram**

[![data_flow_3](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case3_small.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/use_case_3.png)

**Relevant Advertised Security Features of Mosquitto**

In the [mosquitto.conf man page](https://mosquitto.org/man/mosquitto-conf-5.html) in the **Authentication** section, it explains the necessary configuration options for an access control list (ACL) file if it is to be used.  Unfortunately: policies must be typed directly into the ACL file.  This leaves room for errors.  Unlike the mosquitto_passwd utility, there is no existing utility that would provide a convenient interface to the user for them to modify the ACL file.  

#### 3. 

**Use Case**

Jake, the broker admin, creates and publishes a scheduling order log every morning. The order log contains the list of delivery orders and schedules. Other actors such as, Bob, the delivery office admin, can publish or cancel orders in the order log, Henry, the honest delivery driver, subscribes to the order log. Jake can view, complete, or cancel orders; and the order log is update based on the completed and/or canceled orders.

**MisUse Case**

LaZer, the hacker, is bored and has nothing better to do with his life so he decides to sabotage the fleet management system. After his preliminary research, LaZer realizes that the order log is an important entity in this company, because all the other actors and actions rely on the order log. LaZer decides to perpetrate the order log with a Denial of Service (DoS) attack. LaZer overwhelms the order log with illegitimate superfluous connection requests which causes an overload and prevents the legitimate requests from being fulfilled. This attack will disrupt or possibly shutdown the company’s delivery operations. In case the fleet management system has contingencies put in place to mitigate system overload due to numberless connection requests; LaZer has a backup DoS attack which will overload the system by sending extensive ping requests from a single connection. 


**Diagram**

[![data_flow_3](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Updated%20DoS.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Updated%20DoS.png)

**Security Requirement**

Since the Order Log is a crucial entity in the fleet management system, the company must take all necessary actions to prevent or mitigate all possible attacks. In this case, LaZer is sending large amount of illegitimate requests that seem legitimate and pass the firewall. This attack can be prevented by adding a rule to the firewall settings which would limit the number of connection requests. And to prevent extensive amount of requests from a single connection, a firewall rule must be added to set a threshold to deny large amount of traffic coming from the same IP address within a short period of time.  

**Relevant Advertised Security Features of Mosquitto**

The above described security requirements are addressed in [mosquitto-conf man page](https://mosquitto.org/man/mosquitto-conf-5.html), under the **General** section of the webpage. The following are two features listed in Mosquitto documentation that cover the firewall rule descried above.
“*allow_anonymous*” Boolean determines whether clients without credentials are allowed to connect. By setting this Boolean value to false, we can stop the incoming traffic from unauthenticated clients and prevent a possible **DoS** attack. 
“*allow_duplicate_messgae*” Boolean limits the number of duplicate messages to one if set to false. This option is useful when clients are subscribed to multiple subscriptions that overlap. In addition, this feature is useful to the security features described above in order to deny multiple illegitimate messages coming from one client. 


#### 4. 

**Use Case**

Jake, the System Admin, is responsible for the upkeep, configuration, and operation of the systems at Fleet Management company. The security of the systems is essential to protecting the information assets of the organization from exploits and to guarantee daily operations free of down time for the office manager and delivery staff out in the field. 

**MisUse Case**

Lazer, the hacker, is a System Administrator that was recently laid off from the parent company who owns Fleet Management and feels he has a score to settle. He looks for a smaller subsidiary with a significant reach in both billings and clients and that would cause the most disruption to the organization. Lazer creates a phishing campaign specifically to target Fleet Managements System Administrator. The campaign is for a fake a system update and when Jake, the unsuspecting system administrator, clicks the download option it places malware onto his computer. Once Lazer has Jake's login credentials, he plans to compromise the integrity of the Fleet Management systems and stop day-to-day operations. 

**Diagram**

[![data_flow_4](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Use_Case_4_Dataflow_e.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Use_Case_4_Dataflow_e.png)

**Security Requirement**

To mitigate a targeted phishing attack, a common strategy is duel factor authentication. This would be a multi identity design that could use RSA tag or a push that acts as a secondary credential in conjunction with the username and password. The inclusion of an Intrusion Prevention System would add further security and block malicious activity by recognizing bad patterns, such as Malware.  

**Relevant Advertised Security Features of Mosquitto**

There are various methods available to authenticate the user, and more than one method can be required. This is described in the [mosquitto.conf man page](https://mosquitto.org/man/mosquitto-conf-5.html) in the Authentication section.

#### 5. 

**Use Case**

This use case centers around a key responsibility of a Systems Administrator (or in this scenario, Broker administrator), monitoring the status of the broker.

Jake, the broker admin, needs to be able to monitor the status of the broker, and pull statistics from the broker to log externally. He accomplishes this by subscribing to the $SYS topic that is hosted on the broker.

It is important to note that there is an assumption within this use case about our broker's configuration. Part of the MQTT messaging protocol dictates that System level statistics and statuses are published on every MQTT broker in a topic labeled "$SYS". While we are staying out of the technical confines and definitions of our system and software while generating our use cases, and the actual definitions of what is published in this topic is still vague, this is a [well-defined requirement](https://github.com/mqtt/mqtt.github.io/wiki/SYS-Topics) of every MQTT broker instance, so it is assumed that the broker used in our scenario will have a $SYS topic where information pertaining to our broker will be published.

**Mis-Use Case**

Darrel, a hacker hired by a rival package delivery company, wishes to spy on the amount of orders in Jake's system. He attempts to accomplish this by trying to subscribe to the $SYS topic on the example broker, since it is again well known that every MQTT broker instance has a $SYS topic published. 

One additional assumption is made with this use case and misuse case for the sake of generating different security requirements. Since every additional use case published in our assignment has brought authentication as a mitigation step in some form, we are assuming that this broker is already implementing authentication, and that Darrel is attempting to subscribe to the $SYS topic with stolen credentials.

**Prevention**

Authorization is a feature that should be implemented in order to prevent this attack pattern from successfully happening. Only users with a specific type of access to the example broker should be able to subscribe to the $SYS topic since sensitive information pertaining to the system could be published to this topic.

**Mis-use case evolved**

In order to evolve past the use of authorization, Darrell instead attacks the use of authorization itself. He attempts this by trying to change the configuration of the broker through sending a SIGHUP (or signal update) command 
to the broker.

**Diagram**

[![data_flow_5](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Use_Case_4_Dataflow_small.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/user_case_5-updated_dataflow_small.png)

**Prevention Evolved**

A combination of increased use of authorization, along with Logging of SIGHUP commands and other commands of this nature should be implemented in order to prevent this command from successfully altering authorization of the broker.

**Relevant Advertised Security Features of Mosquitto**
  
Mosquitto handles authorization primarily through Access Control Lists (ACLs) which you can limit by topic.

From studying the documentation offered by Eclipse, it's unknown if SIGHUP commands have a similar ability to be limited through ACLs or any other form of authorization. It's also unknown if SIGHUP commands are logged in the $SYS topic or other logging locations (stdout or file output for example).


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

