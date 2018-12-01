# Code Analysis 

After engaging in the security-related activities of misuse cases, assurance cases, and threat modeling, we identified the following security features to be high priority:
- authentication
- authorization 
- requiring strong passwords
- DOS prevention (limiting number of connections and denying dupliacate requests from the same connection)
- Logging of the broker and username/password creations

## Code Review Strategy

Our code review strategy involved the following activities:
* Run static analysis tools against the codebase
* Analyze the static analysis results
  * Filter the static analysis results to security related areas of concern
  * Identify any patterns and files of interest from the report.  Identify repeated CVEs
* Conduct manual code reviews of high level flagged areas of concern in relation to identified CVEs
* Conduct manual code review of files of interested identified in our previous security-related activities
  * scope manual code review to CVE's identified in static analysis 

## Code Review Results

### Automated Code Reviews:

### Codacy

**Overview**
* Codacy Report (*Click on the following badge for Codacy Report*) [![Codacy Badge](https://api.codacy.com/project/badge/Grade/54e5a5c7877d4c6a948a249c66a856ef)](https://www.codacy.com/app/sanjar91/mosquitto?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=ppeters0502/mosquitto&amp;utm_campaign=Badge_Grade)
* Total issues:	543
* Categories:
  * Security: 228
  * Error Prone:	73
  * Code Style:	228
  * Unused Code:	14
* Overall Grade: A

**Findings of Security Related issues**

* CWE-126 (Buffer Over-read): 205
  * 205 “strlen()” function calls do not handle strings that are not, “\0”, null terminated. Possible over-read may occur if the pointer or its index is incremented beyond the bounds of the provided buffer
* CWE-120 (Buffer overflow): 8
  * 8 “strncopy()” function calls do no verify that the size of input buffer is less that the size of output buffer before copying input buffer to output buffer causing a potential buffer overflow
* CWE-20 (Improper input validation): 3
  * Input validation that can affect the control flow or data flow of Mosquitto. There are three recursive/loops that are provided with not validated inputs in the following files:
   - lib/loop.c: line 159
   - lib/net_mosq.c: line 694
   - test/qos.c: line 102
* Input Validation: 5
  * 5 “subprocess()”calls in the following python files do not validate external input. However, python possesses many mechanisms to invoke an external executable and use of a command shell is not vulnerable to shell injection attacks, but care should still be taken to ensure validity of input:
   - test/broker/ptest.py: lines 114, 125, 137
   - test/mosq_test.py: lines 32, 55
* Importing sub processes: 3
  * Low level risk, but checks must be put in place to consider possible security implications associated with sub-process modules in the following python files:
   - test/broker/03-publish-qos-queued-bytes.py: line 7
   - test/broker/ptest.py: line 3
   - test/mosq_test.py: line 4
* Using the “random()” function: 2
  * The use of standard pseudo-random generators is not suitable for security/cryptographic purposes. Check the following file:
   - test/broker/03-publish-qos1-queued-bytes.py: lines: 129, 144
* CWE-732 (Incorrect Permission Assignment for Critical Resource): 2
  * The author uses and extra 0 before the permission. In the following file the author uses “0077” while better programming practice and CWE-732 argues to only use three digits so instead of using “0077” author could’ve used “077” in the following file:
   - lib/util_mosq.c: line 448

#### Flawfinder

**Overview**
* Flawfinder Report: https://github.com/sanjar91/Fantastic-Four/blob/master/static_analysis_output/flawfinder_output.txt 
* Total issues: 493
* Hits by Risk Level
  - [1] 261 
  - [2] 175 
  - [3]  12 
  - [4]  45 
  - [5]   0

**Findings of Security Related issues**

* CWE 126 (Buffer Over-read):  126 
  * Does not handle strings that are not \0-terminated; if given one it may perform an over-read

* CWE 120 (Buffer overflow): 31
  * (buffer) char: Statically-sized arrays can be improperly restricted, leading to potential overflows or other issues. Perform bounds checking, use functions that limit length, or ensure that the size is larger than the maximum possible length.
   - lib/cpp/08-ssl-connect-cert-auth-enc.cpp:8

* CWE 134 (Use of Externally-Controlled Format String): 12
  * (format) snprintf: The software uses a function that accepts a format string as an argument, but the format string originates from an external source. If format strings can be influenced by an attacker, they can be exploited, and do not always \0-terminate. Check the following files:
   - client/pub_client.c: line 29
   - config.h: line 24
   
* CWE 20 (Improper input validation): 3 
  * Data from all potentially untrusted sources should be subject to input validation. There are two recursive/loops that are not checking buffer boundaries located in these files:
   - lib/net_mosq.c: line 694
   - test/qos.c: line 102

* CWE 190 (Integer Overflow or Wraparound): 67
  * Low level risk, but if source is untrusted, check both minimum and maximum, even if the input had no minus sign. Consider saving to an unsigned value if that is intended to avoid large numbers from rolling over into negative numbers.

* CWE 362 (Shared Resource with Improper Synchronization): 31
  * (race) access: This usually indicates a security flaw. If an attacker can change anything along the path between the call to access() and the file's actual use (by moving files), the attacker can exploit the race condition. Set up the correct permissions using setuid().
   - test/broker/c/auth_plugin_msg_params.c:33
   - src/mosquitto_broker_internal.h: line 622
   - src/security.c: line 430

* CWE 327 (Use of a Broken or Risky Cryptographic Algorithm): 5
  * (random) srand: This function is not sufficiently random for security-related functions such as key and nonce creation. Use a more secure technique for acquiring random values. Check the following file:
   - lib/mosquitto.c: line 53


### Manual Code Review

**Inspection of Flagged Lines of Code**

**Scoped Manual Code Review of Previously Identified Files of Interest**
##### Logging of Admin Functions
After completing the Assurance Cases and Data Flow Diagrams for this project, a major flaw was discovered that had to do with event logging, and what functions within the OSS were in the scope of that event logging. This major flaw specifically dealt with a utility that is separate from the internal broker, the mosquitto_passwd utility. This utility creates new users, deletes users, resets passwords, and supports batch commands to create and delete large groups of users. 

This utility by default is only accessible from the host system that the broker runs on, so many of the vulnerabilities the externally facing files encounter are not applicable. Since no logging is set in place for this utility though, common exploits such as trying to change the password of a user with a higher level of access, or batch creating more users for use in a Denial of Service style attack, would not be tracked by this broker and could go unnoticed.

To enable this sort of feature, the mosquitto_passwd.c file would have to reference the logging.c file and config.h, in order to pull down what type of logging and what logging location is being used, and to use the log\_\_printf() function to successfully log user admin events. 

##### Authentication
Since Authentication was one of our security requirements, we doveinto it's implementation in the code.  Specifically, we focused on the username and password creation process.  Derived from our previous security activities, this made the mosquitto_passwd.c file of interest.  This is a self-contained utility that generates passwords.  Overall we noticed that buffer overflows are consistently negated by making use of the MAX_BUFFER_LEN macro whenever user input is saved into a data structure. The following methods stood out to us:
 
get_password: get's the password input from the user. Confirms the user properly entered the password by having them enter the password twice

Findings: Though this method does ensure that something is entered for the password (empty passwords are not accepted), it does not enforce strong passwords. The project could implement a strong password check via a regular expression check on line 310.  

ouput_new_password: hashes the password.  We were pleased to notice that hashing passwords are the default configuration which implments the "secure defaults" security principle.  The hashing process is utilizing the well known openssl library which implments the vetted design principle.

delete_pwuser:  Deletes the user/password.  We observed that the user is not required to enter the password to delete a specific user.  The user simply enters the user, and the utility finds the line where the user exists, and it delete that line of code, which contains both the user and the hashed password. 


**Issue with Codebase**
