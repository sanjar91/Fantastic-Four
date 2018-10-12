###### Fantastic Four
###### CYBR 8420

# Assurance Cases

### Assurance Case 1

[![data_flow_1](https://github.com/sanjar91/Fantastic-Four/blob/master/images/assurance_case1.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/assurance_case1.png) 

**Evidence:** 

The first evidence for C4 would be that there would be mention somewhere in the [mosquitto-conf documention](https://mosquitto.org/man/mosquitto-conf-5.html) that strong passwords are either implicitly required, or there is an available setting for the mosquitto-conf file that makes strong passwords a requirement.  Unfortunately, this feature does not exist in mosquitto.  

This application would benefit from strong passwords being required whenever authentication is used.  One way this feature could be implemented in the future is to include a [check on the password's strength via a regular expression](https://stackoverflow.com/questions/42965082/using-regex-in-c-program-to-check-password-strength).  

If a strong password feature was included in the future, then an undermining rebuttal might be that the regular expression has loopholes in it, thus not satisfying the strong password requirements.  To counter this, a report could be provided from automated test results of attempted creations of weak passwords.  For each strong password rule that the regular expression enforces (e.g. one upper case letter, one lower case letter, one number, etc), there would be an attempted password creation that violates that rule.   All tests should result in Mosquitto responding that the provided password is invalid.  These tests would confirm that the regular expression is enforcing all password requirements as intended.

There are many [dictionary attack tools and techniques](https://wiki.skullsecurity.org/Passwords) that often make password recovery possible.  Strong passwords via C4 make dictionary attacks much less successful.  However, additional security can make dictionary attacks even less successful by throttling the amount of password attempts.  Since dictionary attacks require an extremely high amount of guesses, most dictionary attacks are automated in order to make the most password guesses in the least amount of time.  Thus, dictionary attacks would appear very similar to a DoS attack to an intrusion detection system.  Assurance Case #3 details the required evidence to provide assurance that DoS attacks are handled, and this assurance will also apply to dictionary attacks.

Claim #6 is a difficult situation since it assumes the attacker is an insider who already possesses the necessary credentials to alter orders.  Detection via log files is the best tool available in these situations so that if any malicious activity is suspected, there is an available papertrail for investigation.  The [mosquitto.conf file] has three relevant settings: 
1. `allow_anonymous false` (set to false to make it so that authentication is mandatory. This way, all publish/subscribe commands are linked to a user.)
2. `log_dest <path_to_log_file>` (specify the file that the broker logs are saved to.  Default is that logs are not saved but instead sent to stdout.)
3. `log_type all` (provides metadata about all subscribe/publish requests.  Most notably: the user's name, and the unix timestamp)

The critical information that the broker log file can provide is a Unix timestamp, which serves as an identifier for each publish/subscribe event, as well as the user who initiated the event.  Unfortunately, the broker log file only contains the ability to capture metadata about the payload, such as the byte size of the payload, and does not provide any options to capture the message itself.  In order to further tie the user to the malicious message, an additional *subscription* log file should be maintained for each topic. It is worth noting that the subscription log file does not possess any options to display the user who sent the publish command.  This is where the Unix timestamp comes into play because the broker log file and subscription log file can be joined since they are both able to log the Unix timestamp:

[![log join file](https://github.com/sanjar91/Fantastic-Four/blob/master/images/logfile_join.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/logfile_join.png)

As specified in the [mosquitto.sub docs](https://mosquitto.org/man/mosquitto_sub-1.html), to include the Unix timestamp on the subscribe command, the `-F` flag should be set to specify a custom output, and `%U` specifies the Unix timestamp. 

The undermining rebuttal postulates that the logs may not properly be logging the above mentioned critical information as expected.  A simple report can be provided with the results of automated tests which prove that, given various inputs, the log outputs produce what is expected.   

### Assurance Case 2

The second assurance case was going to surround the requirement that only the delivery driver can mark deliveries as completed.  However, we quickly noticed that assurance case #1 and assurance case #5 already provides assurance surrounding this requirement.  In other words, the diagram would duplicate claims and rebuttals already covered elsewhere.  We are confident that as long as assurance case #1 and assurance case #5 are satisifed, then we can assume that the delivery driver’s action of completing orders will maintain integrity as well. 

We brainstormed ofter possible assurance cases but were unable to think of others.  This is mostly due to the fact that mosquitto is limited in it's functionality.  Our existing assurance cases already cover a lot of ground, including: dictionary attacks, DoS attacks, malware, authenication and authorization configurations.

### Assurance Case 3
[![data_flow_3](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance%20Case%203.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance%20Case%203.png)
### Assurance Case 4

[![data_flow_4](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_4_new2.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_4_new2.png)

**Evidence:** 

Evidence for (C3) would include code found on lines 155-159 of the handle_publish.c file, and code regarding topic length found on lines 134-145 of the same file. The Mosquitto configuration restricts messages/files to a predetermined length and prohibits files from exceeding these requirements. The combined code evidence cited above, collectively, protects the broker against buffer overflow attacks. Lastly, evidence for (C4) would require an additional capability by Mosquitto, in a future-state, that would limit the data file types allowed by the broker thereby offering an added layer of security against malware attacks. 

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

