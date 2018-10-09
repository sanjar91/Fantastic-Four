###### Fantastic Four
###### CYBR 8420

# Assurance Cases

### Assurance Case 1

[![data_flow_1](https://github.com/sanjar91/Fantastic-Four/blob/master/images/assurance_case1_thumbnail.png)](https://github.com/sanjar91/Fantastic-Four/blob/master/images/assurance_case1.png)

**Description:** 

The context of this assurance case is that the fleet management system (FMS) is using the Mosquitto broker.  The top level claim (C1) is that the logistical information in the FMS maintains integrity through delivery of each order.  That is: the delivery information, such as the delivery address, does not get modified unless it is supposed to.  

The two identified rebuttals for this claim only differ by who the attacker is. R1 counters C1 if the attacker is an *outside* attacker and is able to change the data on the deliveries.  R2 counters C1 if the attacker is an *inside* attacker that is able to change the delivery orders.  

To counter R1, C2 claims that the FMS only allows authenticated users to change delivery orders, and C3 claims that the FMS only allows authorized users (authenticated users with designated roles in the system) to be able to change delivery orders. The C3 claim also counters R2.

R3 counters C2 if the system is vulnerable to dictionary attacks.  R4 counters C3 if the attack is actually an individual with the proper authorization credentials. 

C4 and C5 are both required in order to counter dictionary attacks (R3).  The system should require strong passwords (C4), and the system should have controls in place in order to prevent an automated system from performing a dictionary attack (C6).  This two sub claims combined will make dictionary attacks unfeasible. C6 is a difficult attack to absolutely prevent because the malicious user is an individual who possesses the necessary roles to modify addresses.  The best form of defense against this type of attack is detection.  This can be implemented by maintaining logs of all address modifications.  If there is an issue, then there is a paper trail available for review and investigation purposes.  

Evidence for C4 would be a report that multiple tests were conducted for the task of creating passwords, and the system would not allow any of the weak passwords to persist.  Evidence for C5 would be a report that tests were conducted which perform a dictionary attack against the, system, and the report should show that that the authentication attempts were throttled. Lastly, evidence for C6 would be a report of tests which prove that every time an order is published, logs are created which track the user that published that order. 




### Assurance Case 2
### Assurance Case 3
### Assurance Case 4
### Assurance Case 5

