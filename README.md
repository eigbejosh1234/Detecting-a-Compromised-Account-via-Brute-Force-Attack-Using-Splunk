# Detecting-a-Compromised-Account-via-Brute-Force-Attack-Using-Splunk
This repository demonstrates how to detect compromised accounts caused by brute force attacks using Splunk as a SIEM tool.

The project leverages authentication and security logs to identify suspicious login patterns, such as a high volume of failed authentication attempts followed by a successful login. Using Splunk Search Processing Language (SPL), the detection logic helps security analysts uncover potential credential compromise and respond before further malicious activity occurs.

**Key features:**

Brute force and compromised account detection using Splunk

* SPL queries for analyzing authentication failures and successes

* Correlation of failed and successful login events

* SOC-ready detection logic for blue team operations

Designed for cybersecurity labs, SOC analyst training, and defensive security research, this repository can be adapted for real-world Splunk alerting and monitoring environments.


As a SOC analysis, the first thing i do before viewing log/alerts is to confirm whether my splunk is recieving log/alerts, especially failed and success. (Eg EventCode 4625/4624)

To confirm whether my splunk is recieving alert from my window universal forwarder:

**1** I login into my spunk server

**2** I clicked on search and reporting 

**3** In the search box, i searched for the quary below:

index=* sourcetype=WinEventLog:Security

It should  bring out events that displays either eventcode4624 or eventcode4625

<img width="960" height="504" alt="image" src="https://github.com/user-attachments/assets/a3d10a94-e75c-4506-b1ac-930b7fb4567f" />

After confirming that my splunk server is recieving both failed and successful logs, i moved to simulating my windows OS on compromise brute force. 

As we've said earlier, brute force simply means having a multiple 5-10 or many failed logons, then one successful.

Attacker that want to login without your knowings will first try many attempts of failed passwords and by chance he/she might guess one successfuly. That act is been refair to as **COMPROMISE BRUTE FORCE ATTACK.** This kind of attack is very dangerious and needs urgent escalation and attention.


**HOW I SIMULATE MY WINDOW OS FOR A COMPROMISE BRUTE FORCE ATTACK.**

**1** I switched off my window OS. 

**2** I switched it on/ start my window OS

**3** I attempt a successful username with with wrong passwords 7 times 

**4** And then a correct password once to open my window

**After a successful login on my window OS, I quickly pickup my Ubuntu VM where my splunk is installed and did the below steps:**

**1** I opened and login into my splunk server

**2** I clicked on search and reporting 

**3** Inside the search box, i search for the below spl quaries:

index=* sourcetype=WinEventLog:Security (EventCode=4625 OR EventCode=4624)
| eval action=if(EventCode=4625,"Failed login","Successful login")
| eval user=Account_Name
| where user="USER"
| table _time host user action
| sort _time


<img width="617" height="220" alt="image" src="https://github.com/user-attachments/assets/891e128f-3ffd-4510-a0e9-6bdfa3bafe6c" />

**BRIEF ANALYSIS**

From the above image, you can see clearly with very close time interval how **7** failed logon was detected and **1** logon was successful.

This is a dangerous compromised brute force attack because after **7** failed logon, the attacker eventually initiated a successful login, and as a soc analyst, this will definitly need urgent escalation and attendtion. 

**To investigate a particular host that was compromised**

**How to check if a command is being run on your windows os using splunk:**

In your SPL search box, search:

index=* EventCode=4688

If you see nothing that means attacker run no command. But if you see either CMD, POWERSHELL etc commands where ruan on your host.


**Check if someone access/change file**

In your spl search run:

index=* EventCode=4663

This shows what file, who accessed it and when.

**Did the attacker try to stay permanent?**

In your spl search, run:

index=* EventCode=7045 or EventCode=4597

**Did the attacker create or change account?**

index=* EventCode IN (4720,4722,4728,4732)


 **INCIDENT SUMMARY REPORT**

 Host: MASTERJOETECH

 Date: 10/02/2026

 Incident Type: Multiple failed login/ Brute-force attempt

 Severity Level: Low

 **SUMMARY**

 Multiple failed login attempts (EventCode=4625) where detected on the host, followed by a single sucessful local login (EventCode=4624, Logon type 2). No remote access, malicious command execussion, file modification, or persistence activity was identified.

 **INVESTIGATION FINDINGS**

 * Failed login attempts detected.

 * Successful local login fro same host (127.0.0.1)

 * No RPD or remote access

 * No suspicious processes or command execussion

 * No malicious services, file or account changes

   **IMPACT**
   No evidence or system compromised. Activity likely due to repeated incorrect password attempts or local login brute force.


   **RECOMMENDATION**

   * Reset affected user password
  
   * Enable account lockout policy
  
   * Continue monitoring login events
  
     **INCIDENT STATUS**

     Status: CLOSED

     Escalation Required: NO
