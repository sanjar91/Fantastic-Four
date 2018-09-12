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

### 2. Security Features

#### Encrypted network connection and authentication ####
Authentication is disabled by default but two configurable encryption options are available:
- Certificate based SSL/TLS support
- Pre Shared Key encryption

#### Authorization ####
Configurable options are available to setup access control lists (ACLs) which confirm whether or not the clientis authorized to subscript/publish to various topics. 

#### Quality of Service ####
The Broker/Client can specific how much effort will take place to ensure a message is delivered.  Those levels are as follows:

0: the broker/client will deliver the message once, with no confirmation (default)

1: The broker/client will deliver the message at least once, with confirmation required

2: The broker/client will deliver the message exactly once by using a four step handshake

#### Wills ####
A client may setup a “Will” which is a message that the broker will send out to all other connected client’s of the selected topic in the event that the client disconnects unexpectedly. 

#### Retained Messages ####
Configurable options are available in order for the Broker to save/persist messages on topics.

#### Clean Sessions / Durable Connections ####
Configurations are availble for the broker to keep track of a client’s desired QoS and subscriptions.  in this way, those configurations will still be available if the client disconnects and later reconnects.

### 3. Motivation for selecting this project
Mosquitto is a lightweight broker and Client that uses the MQTT protocol for messaging. MQTT is a very popular protocol for the Internet of Things movement, and enables the ability to message thousands of machines at the same time in a publication/subscription format.
While this makes it very easy to enable machine to machine messaging and other core pieces of IoT, it also brings a greater threat impact and wider threat target to keeping IoT secure. With [DDOS attacks using IoT](https://www.corero.com/blog/870-the-rise-of-iot-botnet-threats-and-ddos-attacks.html) on the rise, the movement
is starting to attract a very negative attitude in the cybersecurity world, which was part of our motivation for selecting this project.

Mosquitto is also used in several large scale open source projects that deal specifically with personal location tracking. The [OwnTracks project](https://owntracks.org/booklet/), a project that provides applications for reporting your phone's location to your own private servers,
  uses Mosquitto exclusively in their applications and lists Mosquitto as their recommended broker in their [documentation](https://owntracks.org/booklet/guide/broker/). Another IoT project, [ThingsBoard](https://thingsboard.io/), also lists Mosquitto as a recommended client and broker for pushing data through [MQTT published messages](https://thingsboard.io/docs/getting-started-guides/helloworld/).
Due to the sensitivity of the information transmitted from projects such as OwnTracks and ThingsBoard, transmitting secure messages to thousands of devices is imperative, and Mosquitto makes for a great starting point in assuring that data can be transmitted securely. 

### 4. Open source project description (What is it?, Contributors, Activity, Use, Popularity, Languages used, platform, documentation sources, etc.)

Eclipse Mosquitto, part of the Eclipse Foundation, is an open source lightweight message broker that uses Message Queue Telemeter Transport (MQTT) protocol. Mosquitto is multiplatform and can be used on all devices regardless the computing power of the machine. MQTT protocol uses a publisher/subscriber module. Multiple subscribers can connect to a publisher and subscribe to the topics they are interested in. Mosquitto uses MQTT protocol due to MQTT’s lightweight pub/sub model. 

Mosquitto has 22 releases, and 53 contributors. Currently there are 196 open issues that are being worked on by different contributors. Mosquitto is mainly based around C#, but it uses Python, C++, HTML, and other languages as well. The documentation for subscriber, publisher/broker, and client library or APIs can be found in the man pages on http://mosquitto.org/man/. 

Mosquitto works on multiple platforms such as Windows, Mac, Linux, CentOS, Raspberry Pi, et cetera., the full list of platforms with installation guides for each can be found at the following link https://mosquitto.org/download/. 


### License
* The Mosquitto project is dual licensed under the Eclipse Public License 1.0 and the Eclipse Distribution License 1.0 as described in the epl-v10 and edl-v10 files.

1. Grant of Rights: Contributors grant recipients a non-exclusive, worldwide, royalty-free copyright license. Recipients have no assurances from contributors that the program does not infringe the patent or other intellectual property rights of any other entity. 
2. Requirements: A Contributor may choose to distribute the Program in object code form under
its own license agreement, provided that it complies with the terms and conditions of this Agreement; and excludes all contributors for damages as a result of the Program.
3. Commercial Distribution: Contributors who include the program within a commercial product must do so without creating potential liabilities for other contributors, and if a court requires any other Contributor to pay any damages as a result, the Commercial Contributor must pay those damages.
4. No Warranty: The open source software is available "as is" and each recipient is resposible for determining the appropriateness of using the program and assumes all risk associated with its use. 
5. Disclaimer of Liability: Recipients nor Contributors will have liability for any direct or indirect damages caused from code changes. 

### Making Contributions
In order for contributions to be accepted, they must comply with the Eclipse Foundation IP policy at http://wiki.eclipse.org/Development_Resources/Handling_Git_Contributions

#### Contributor Agreement
1. Create an account for an Eclipse Foundation User ID at https://accounts.eclipse.org/user/register to gain access to Gerrit, Bugzilla, and other Eclipse Foundation web resources. Be sure to use the same email address when you register for the account that you intend to use on Git commit records. 
2. Sign the Eclipse Contributor Agreement (Certificate of Origin) at http://www.eclipse.org/legal/ECA.php. 

By signing the ECA committers are agreeing to the following:
* Work submitted with modifications must be original work by the developer(s) and is expected to be covered by the appropriate open source license.
* All contributions and the personal information submitted with those contributions will be made public record.

#### Getting Started
* Clone the forked repository: git clone https://github.com/<your username>/mosquitto.git
* If you are adding a new feature, a new branch should be created from the latest develop branch with git checkout -b YOUR_BRANCH_NAME origin/develop. 
* If you are fixing a bug, a new branch should be created from the latest fixes branch with git checkout -b YOUR_BRANCH_NAME origin/fixes.
* Make sure that your commit message is meaningful and describes your changes correctly. Go to https://github.com/eclipse/mosquitto and create a pull request from your "YOUR_BRANCH_NAME" branch to the develop or fixes branch as appropriate to request review and merge of the commits in your pushed branch.
  
#### Commit Records
Minimally, your Git commit record must have the following:
* Your credentials (email address) captured in the "Author" field;
* A single line summary in the message field, followed by a more detailed descriptive paragraph;
* A "Signed-off-by" entry with matching credentials in the message footer; and
* Specify additional authors using one or more "Also-by" entries in the message footer. 

If applicable, a link to the Bug that is addressed by the commit should be included in the message footer. The id (bug number) of the bug should also be included in the message summary.

In order to set up sign-off-by formatting, go to account settings at https://accounts.eclipse.org/user#open_tab_accountsettings and add your GitHub username to your account. Make sure that you sign-off your Git commits in the following format: Signed-off-by: John Smith <johnsmith@nowhere.com> This is usually displayed at the bottom of the commit message. Additionally, ensure that the email address that you make your commits with is the same one you used to sign up to the Eclipse Foundation website with.

Command to sign off on a commit: git commit -s -m "Auto share multiple projects in single job"

### 6. Security History

This project is actively maintained. At the time of this writing, the most recent commit occurred less than a month ago, and responses to submitted issues tend to occur within days.  
This project has been available since 2014, and as of the time of this writing: a total of four security vulnerabilities have been posted.   These vulnerabilities where published on Common Vulnerabilities and Exposures (CVE).  The most recent of these was dated 2/27/2018, and it details a DDoS vulnerability.  A fix was implemented in version 1.4.15.  A particularly nasty vulnerability was also discovered where if a SIGHUP is sent to the broker when there are no more file descriptors, then this can in effect cause security settings to get reset back to their defaults, thus potentially removing critical security mechanisms such as authorization and authentication (because the default settings have these features turned off).This vulnerability was also fixed in version 1.4.15, but it is worth noting that the fix did not involve changing the default settings.  
