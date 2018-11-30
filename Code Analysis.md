# Code Analysis 

After engaging in security-related activities such as misuse cases, assurance cases, and threat models, we identified that the following security features were high priority:
- authentication
- authorization 
- requiring strong passwords
- DOS prevention (limiting number of connections and denying dupliacate requests from the same connection)
- Logging of the broker and username/password creations

**Code Review Strategy**

Our code review strategy involved numerous activities which we list below:
- Examine the documentation for mentions of these security features
- Identify any unpatched CVE's
 - Examine past issues and the question/response cycle between users and maintainers of mosquitto to develop an idea of the project's current health of maintainability
- Run static analysis tools.  Filter through the results for any results that might be of concern
- Identify major source code files of interest that are involved in our high priority features.  Conduct a systematic manual code review of these files


**Code Review Results**
