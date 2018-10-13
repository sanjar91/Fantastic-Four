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

Claim #6 is a difficult situation since it assumes the attacker is an insider who already possesses the necessary credentials to alter orders.  Detection via log files is the best tool available in these situations so that if any malicious activity is suspected, there is an available papertrail for investigation.  The mosquitto.conf file has three relevant settings: 
1. `allow_anonymous false` (set to false to make it so that authentication is mandatory. This way, all publish/subscribe commands are linked to a user.)
2. `log_dest <path_to_log_file>` (specify the file that the broker logs are saved to.  Default is that logs are not saved but instead sent to stdout.)
3. `log_type all` (provides metadata about all subscribe/publish requests.  Most notably: the user's name and the Unix timestamp.)

The critical information that the broker log file can provide is both the user who initiated the event and the Unix timestamp.  The Unix timestamp serves as an identifier for each publish/subscribe event.  Unfortunately, the broker log file only contains the ability to capture metadata about the payload, such as the byte size of the payload, and does not provide any options to capture the message itself.  In order to further tie the user to the malicious message, an additional *subscription* log file should be maintained for each topic. It is worth noting that the subscription log file does not possess any options to display the user who sent the publish command.  This is where the Unix timestamp comes into play because the broker log file and subscription log file can be joined since they are both able to log the Unix timestamp:

[![log join file](https://github.com/sanjar91/Fantastic-Four/blob/master/images/logfile_join.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/logfile_join.png)

As specified in the [mosquitto.sub docs](https://mosquitto.org/man/mosquitto_sub-1.html), to include the Unix timestamp on the subscribe command, the `-F` flag should be set to specify a custom output, and `%U` specifies the Unix timestamp. 

The undermining rebuttal postulates that the logs may not properly be logging the above mentioned critical information as expected.  A simple report can be provided with the results of automated tests which prove that, given various inputs, the log outputs produce what is expected.   

### Assurance Case 2

The second assurance case was going to surround the requirement that only the delivery driver can mark deliveries as completed.  However, we quickly noticed that assurance case #1 and assurance case #5 already provide assurance surrounding this requirement.  In other words, the diagram would duplicate claims and rebuttals already covered elsewhere.  We are confident that as long as assurance case #1 and assurance case #5 are satisifed, then we can assume that only the delivery driver can complete his own orders, and the complete action will maintain integrity. 

We brainstormed for other possible assurance cases, but we were unable to think of others that would not become very similar to already existing assurance cases.  This is mostly due to the fact that mosquitto is limited in it's functionality.  Our existing assurance cases already cover a lot of ground, including: dictionary attacks, DoS attacks, man in the middle attacks, malware, as well as authentication and authorization configurations.

### Assurance Case 3
[![data_flow_3](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance%20Case3%20final%20final%20update.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance%20Case3%20final%20final%20update.png)

**Evidence:**

Evidence **E1** and **E4** are addressed in [mosquitto-conf man page](https://mosquitto.org/man/mosquitto-conf-5.html), under the **General Options** section of the webpage. The `allow_anonymous` boolean determines whether clients without credentials are allowed to connect. By setting this boolean value to `False`, we can stop the incoming traffic from unauthenticated clients and prevent a possible **DoS** attack.

Evidence **E2** is addressed in this [link](https://www.cox.com/business/security/ddos-mitigation.html). Internet Service Providers (ISPs) can prevent Distributed Denial of Service (DDoS) attacks by proactively monitoring networks. Cox communication, one of the widely used **ISPs**, describe their mitigation plan for **DDoS** attacks on their customer networks. Cox proactively monitors networks 24 hours a day, 7 days a week, and 365 days a year by scanning network resources, assessing threats, and managing a log of network activities so they can quickly mitigate **DDoS** threats.

Evidence **E3** and **E5** are addressed in [mosquitto-conf man page](https://mosquitto.org/man/mosquitto-conf-5.html). If set to `False`, the `allow_anonymous` boolean limits the number of duplicate messages to one. This option is useful when clients are subscribed to multiple subscriptions that overlap. In addition, this feature prevents **DoS** attacks by denying multiple illegitimate messages coming from one client.

### Assurance Case 4

[![data_flow_4](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_4_new2.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_4_new2.png)

**Evidence:** 

Evidence for (C3) would include code found on lines 155-159 of the [handle_publish.c](https://github.com/eclipse/mosquitto/blob/master/src/handle_publish.c) file, and code regarding topic length found on lines 134-145 of the same file. The Mosquitto configuration restricts messages/files to a predetermined length and prohibits files from exceeding these requirements. The combined code evidence cited above, collectively, protects the broker against buffer overflow attacks. Lastly, evidence for (C4) would require an additional capability by Mosquitto, in a future-state, that would limit the data file types allowed by the broker thereby offering an added layer of security against malware attacks. 

### Assurance Case 5

[![data_flow_5](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_5_update-20181012.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/Assurance_Case_5_update-20181012.png)

**Evidence:**

Evidence for C4 (E1) and C6 (E2) would include the logs that are generated depending on the log\_type (specified in mosquitto.conf on the [mosquitto.conf documentation under log_type](https://mosquitto.org/man/mosquitto-conf-5.html)). When the log\_type is set for either info or all, any reloading of the config files are logged to the logging destination (whether that is a file, the $SYS topic, or to the stdout stream). The evidence for this setup can be found on line 574 of [loop.c](https://github.com/eclipse/mosquitto/blob/master/src/loop.c) where an external boolean, flag_reload, is examined, and if found true, reloads the config and logs the change using the log\_\_printf function defined on line 254 of [logging.c](https://github.com/eclipse/mosquitto/blob/master/src/logging.c).

In order for access control lists or password files to be added, edited, or removed, the mosquitto.conf config file needs to be reloaded, so as long as the log\_type is set to all or info, any changes taking effect with the files (from the broker's perspective) would be logged when the config file is restarted.



