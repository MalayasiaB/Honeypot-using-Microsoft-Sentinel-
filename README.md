<h1>Honeypot Using Microsoft Sentinel</h1>


<h2>Description</h2>
Project consists of a virtual Windows machine that is connected to a virtual network with the cloud firewall and Windows internal disabled to attract malicious traffic from the internet. The machine is configured to forward logs to a Log Analytics Workspace, where failed attack attempts are collected in a central repository. That repository is connected to Microsoft Sentinel, a cloud-native SIEM. The environment allows users to query for failed login attempts, generate custom alerts, and trigger automated playbooks in response to specific events.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>KQL</b>

<h2>Environments Used </h2>

- <b>Windows 10</b>
- <b>Windows 11</b> (24H2)

<h2>Program walk-through:</h2>

<p align="center">
Create Resource Group, Honeypotvirtuallab: <br/>
<img src="https://github.com/user-attachments/assets/a054b1db-e681-427e-8b82-b9d7c5d31123" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Navigated to Virtual Network and created, named Soclab:  <br/>
<img src="https://github.com/user-attachments/assets/53349f2a-be38-45cc-ae72-61698967103f" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Navigated to Virtual Machines and created CORP-NET-EAST1: <br/>
<img src="https://github.com/user-attachments/assets/67c2e758-b6a2-40e3-81e3-597508fdf7d5" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Editing the Network Security Group by removing RDP inbound rule:  <br/>
<img src="https://github.com/user-attachments/assets/7686dd54-409b-4d75-ab40-cdb9f86be395" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Creating the new "Danger_AllowAny" Rule and using "*" in the Destination port range to allow any traffic:  <br/>
<img src="https://github.com/user-attachments/assets/7c0083c6-06f8-4a00-aeb7-e8f7ddf2b64e" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Using the public IP assigned to my vm and remoting in:  <br/>
<img src="https://github.com/user-attachments/assets/bf1a9c22-b773-4f79-8f5f-a87fe97b0ed5" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Once inside naviagated to wf.msc for windows firewall and disabled the internal windows firewall:  <br/>
<img src="https://github.com/user-attachments/assets/506767a5-4149-40ba-b9b0-929f39210d08" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Pinged vm from local computer to ensure I could reach it over the public internet. This step ensured that if i could, hackers could also:  <br/>
<img src="https://github.com/user-attachments/assets/514a4c21-4015-45a4-b25d-75ff6962cd3c" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Logged out of my vm and intentionally failed logging in to create log data:  <br/>
<img src="https://github.com/user-attachments/assets/716c3e08-6fc1-4ea6-bf60-bdc12eeb1b15" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
More failed logins with a non-existent user:  <br/>
<img src="https://github.com/user-attachments/assets/1266e22e-0b48-44c6-b101-8907a2313aeb" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Opened windows Event Viewer and filtered for Event ID 4625 to display my failed logins:  <br/>
<img src="https://github.com/user-attachments/assets/42cecd66-680e-4daf-bbd1-d4d17e9042ad" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Back in Azure I created a Log Analytics Workspace that would serve as my log repository:  <br/>
<img src="https://github.com/user-attachments/assets/0c4d0226-1f31-4492-9de3-e2c88efc7d06" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Created Microsoft Sentinel, added my log analytics workspace to it, clicked content hub and installed Windows Security Events:  <br/>
<img src="https://github.com/user-attachments/assets/5e307125-a81b-42dd-9909-3a698e5dc908" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
After installation I configured it. This configuration provides connection from my VM to my log analytics workspace via the Windows Security Azure Monitoring Agent:  <br/>
<img src="https://github.com/user-attachments/assets/2fd1eafb-1148-4371-89dc-4e5eb544cbb7" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Created a data collection rule via the AMA:  <br/>
<img src="https://github.com/user-attachments/assets/d17ad1b2-7a51-4eb7-a9b0-606fed7d06d2" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Now with AMA configured, logs can be sent to our Log Analytics Workspace which is also connected to Micorsoft Sentinel; where the logs are accessed. As seen here:  <br/>
<img src="https://github.com/user-attachments/assets/41f57016-4c5a-45a6-941f-fb826915ce40" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Using KQL I queried the windows logs and you can see a difference from the above photo in log attempts. The above photo just displayed my failed logons from earlier. I allowed 30 mins to pass and you can see there are 839 results at the bottom right:  <br/>
<img src="https://github.com/user-attachments/assets/6ed75b5f-21c2-4c97-88a4-dec25f059436" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
From here I created the following query for Brute Force Detection. Failed logons grouped by IP and account, alerting if the same IP has more than 1 failed login within 30 mins, and sorting in descending order :  <br/>
<img src="https://github.com/user-attachments/assets/c191b638-5aca-4ea8-b1a9-fd0591d6d853" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
I used this query to create an alert,mapped it to MITRE T1110 for Brute force and set it to email me when triggered:  <br/>
<img src="https://github.com/user-attachments/assets/77b075e9-75a6-4fdf-8251-f1aef769952c" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
From here I created a playbook to email and post a message via a communication app:  <br/>
<img src="https://github.com/user-attachments/assets/63dc27f6-dda1-496e-ba20-1a560a909c62" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Running the playbook:  <br/>
<img src="https://github.com/user-attachments/assets/d4d27104-13c2-47cf-ade7-d4efbbf4f897" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Email correspondence from the configurations set:  <br/>
<img src="https://github.com/user-attachments/assets/f642e8de-aa63-4291-a9b9-17728b57c294" height="80%" width="80%" alt="Honeypot"/>
</p>
  
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
