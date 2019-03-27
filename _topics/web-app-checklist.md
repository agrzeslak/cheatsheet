---
layout: default
title: Web Application Checklist
tags:
    - 80
    - 443
---
# Web Application Checklist
### Mapping the Application
- Explore Visible Content
- Consult Public Resources
- Discover Hidden Content
- Discover Default Content
- Enumerate Identifier-Specified Functions
- Test for Debug Parameters

### Analyse the Application
- Identify Functionality
- Identify Data Entry Points
- Identify the Technologies Used
- Map the Attack Surface

### Test Client-side Controls
- Test Transmission of Data via the Client
- Test CLient-side Control Over user Input
- Test Thick-client Components

### Test Access Controls
- Understand the Access Control Requirements
- Testing with Multiple Accounts
- Testing with Limited Access
- Test for Insecure Access Control Methods

### Test for Logic Flaws
- Identify the Key Attack Surface
- Test Multistage Processes
- Test Handling of Incomplete Input
- Test Trust Boundaries
- Test Transaction Logic

### Test the Authentication Mechanism
- Understand the Mechanism
- Test Password Quality
- Test for Username Enumeration
- Test Resilience to Password Guessing
- Test Any Account Recovery Function
- Test Any Remember Me Function
- Test Any Impersonation Function
- Test Username Uniqueness
- Test Predictability of Auto-Generated Credentials
- Check for Unsafe Transmission of Credentials
- Test for Logic Flaws
- Exploit Any Vulnerabilities to Gain Unauthorised Access

### Test the Session Management Mechanism
- Understand the Mechanism
- Test tokens for Meaning
- Test tokens for Predictability
- Check for Insecure Transmission of Tokens
- Check for Disclosure of Tokens in Logs
- Check Mapping of Tokens to Sessions
- Test Session Termination
- Check for Session Fixation
- Check for XSRF
- Check Cookie Scope

### Test for Web Server Vulnerabilities
- Test for Default Credentials
- Test for Default Content
- Test for Dangerous HTTP Methods
- Test for Proxy Functionality
- Test for Virtual Hosting Misconfiguration
- Test for Web Server Software Bugs

### Test for Input-Based Vulnerabilities
- Fuzz All Request Parameters
- Test for SQL Injection
- Test for XSS and Other Response Injection
- Test for OS Command Injection
- Test for Path Traversal
- Test for Script Injection
- Test for File Inclusion

### Miscellaneous Checks
- Check for DOM-based Attacks
- Check for Frame Injection
- Check for Local Privacy Vulnerabilities
- Follow Up Any Information Leakage
- Check for Weak SSL Ciphers

### Useful links:
<http://mdsec.net/wahh/tasks.html>
