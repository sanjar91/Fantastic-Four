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
* Analysis the static analysis results
  * Filter the static analysis results to security related areas of concern
  * Identify any patterns and files of interest from the report.  Identify repeated CVEs
* Conduct manual code reviews of high level flagged areas of concern in relation to identified CVEs
* Conduct manual code review of files of interested identified in our previous security-related activities
  * scope manual code review to CVE's identified in static analysis 

## Code Review Results

### Codacy

**Overview**
- Codacy Report [![Codacy Badge](https://api.codacy.com/project/badge/Grade/54e5a5c7877d4c6a948a249c66a856ef)](https://www.codacy.com/app/sanjar91/mosquitto?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=ppeters0502/mosquitto&amp;utm_campaign=Badge_Grade)


**Filtering Process**

**Patterns, CVEs, Files of Interest**

### Flawfinder

**Overview**
- Flawfinder Report: https://github.com/sanjar91/Fantastic-Four/blob/master/static_analysis_output/flawfinder_output.txt 

**Filtering Process**

**Patterns, CVEs, Files of Interest**

### Manual Code Review

**Inspection of Flagged Lines of Code**

**Scoped Manual Code Review of Previously Identified Files of Interest**

**Issue with Codebase**
