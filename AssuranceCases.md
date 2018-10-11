###### Fantastic Four
###### CYBR 8420

# Assurance Cases

### Assurance Case 1

[![data_flow_1](https://github.com/sanjar91/Fantastic-Four/blob/master/images/assurance_case1.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/assurance_case1.png) 

**Evidence:** 

Evidence for C4 would be a report that multiple tests were conducted for the task of creating passwords, and the system would not allow any of the weak passwords to persist.  Evidence for C5 would be a report that tests were conducted which perform a dictionary attack against the, system, and the report should show that that the authentication attempts were throttled. Lastly, evidence for C6 would be a report of tests which prove that every time an order is published, logs are created which track the user that published that order. 

### Assurance Case 2
### Assurance Case 3
### Assurance Case 4

[![data_flow_4](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_4.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_4.png)

**Evidence:** 

Evidence for C3 would include code on lines 155-159 of the handle._.c file and the topics lengths code on lines 134-145. The topics length has the ability to restrict messages/files to a predetermined length and does not allow topics to exceed these requirements. The code evidence cited, collectively, provides security against buffer overflow attacks. Lastly, evidence for C4 would require an additional capability by Misquitto, in a future-state, to limit the data types allowed on the broker and offer an added layer of security against malware attacks. 

### Assurance Case 5

[![data_flow_5](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_5_update-20181009.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_5_update-20181009.png)

**Claims and Defeaters:**

The context of this assurance case is that the fleet management system implemented authentication through credential files stored on the system the mosquitto broker is running on. The top level claim is that, when authentication is implemented, no unauthorized users can publish their own messages to the FMS broker.

The two top level rebuttals are that this claim can be proven false if credentials are stolen (R1), or if the unauthorized user is publishing to a new topic that they are creating on the broker (R2).

To counteract the first of these top level rebuttals,  dual factor authentication can prevent stolen credentials from being used successfully (C2), supporting the top claim. To counteract the second of the top rebuttals, implementing access control lists to prevent unauthorized users from creating their own topics (C3) will also support the top level claim.

A second level of rebuttals is generated, with two rebuttals against C2 and one against C3. The first of these rebuttals (R3), states that if credential files are replaced or altered in a malevolent manner, dual factor authentication can be circumvented, and the top level claim could be proven false. The second rebuttal (R4) states that if packets are seized in a man in the middle style attack, the packets could be altered to publish the unauthorized user's request, again circumventing dual factor authentication and proving C1 false. The last rebuttal in this level (R5) states that if the mosquitto.conf config file is changed or reset to default, ACLs could be removed from implementation, circumventing C3 and putting C1 back to false.

Both R5 and R3 are prevented by the implementation of logging to the $SYS topic. C4 implements logging any changes to credential files, whereas C6 implements logging whenever there are changes to the mosquitto.conf file, or whenever the config file is reloaded to implement new changes through a SIGHUP command. To support these new claims, evidence must be gathred by examing the $SYS topic log during the changing of config files, reloading of config files, or the changing of credential files.

R4 is prevented through the use of SSL/TLS encryption of connections to publish or subscribe to any topics on the broker (C5). To support this claim, evidence must be gathered through the analysis of packets used in publish requests that are through a SSL/TLS connection. 

Claims C4 and C6 (and their supporting evidence) can be undermined if the $SYS topic messages are altered by an unauthorized user (U1). This is prevented by the claim that the $SYS topic is read-only for all users (C7). To support this claim, a report on the $SYS topic permissions must be generated and analyzed.

