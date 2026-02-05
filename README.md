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

***index=* sourcetype=WinEventLog:Security**

It should  bring out events that displays either eventcode4624 or eventcode4625

<img width="960" height="504" alt="image" src="https://github.com/user-attachments/assets/a3d10a94-e75c-4506-b1ac-930b7fb4567f" />

After confirming that my splunk server is recieving both failed and successful logs, i moved to simulating my windows OS on compromise brute force. 

As we've said earlier, brute force simply means having a multiple 5-10 failed logons, then one successful.

Attacker that want to login without your knowings will first tries many attempt of failed passwords and by chance he/she might guess one successful. That act is that is been refair to COMPROMISE BRUTE FORCE ATTACK. And this kind of attack is very dangerious and need urgent escalation and attention.


**HOW I SIMULATE MY WINDOW OS FOR A COMPROMISE BRUTE FORCE ATTACK.**

**1** I switched off my window OS. 

**2** I switched it on/ start my window OS

**3** I attempt a successful username with with wrong passwords 7 times 

**4** And then a correct password once to open my window

**After a successful login on my window OS, I quickly pickup my Ubuntu VM where my splunk is installed and running and did the below steps:**

**1** I opened and login into my splunk server

**2** I clicked on search and reporting 

**3** Inside the search box, i search for the below quaries:

***index=* sourcetype=WinEventLog:Security (EventCode=4625 OR EventCode=4624)
| eval action=if(EventCode=4625,"Failed login","Successful login")
| eval user=Account_Name
| where user="USER"
| table _time host user action
| sort _time**


<img width="617" height="220" alt="image" src="https://github.com/user-attachments/assets/891e128f-3ffd-4510-a0e9-6bdfa3bafe6c" />

**BRIEF ANALYSIS**

From the above image, you can see clearly with very close time interval how **7** failed logon was detected and **1** logon was successful.

This is a dangerous brute force attack which will definitly need urgent escalation and attendtion. 

You can begin to click on each alert to investigate its severity, either false negative, falt positive or true negative, tur positive. 

To learn or know how to do deep investigation/analysis you can check my previous repo or copy and paste the below link

