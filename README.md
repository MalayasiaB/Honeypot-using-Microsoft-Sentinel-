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
<img src="./images/filename1.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Navigated to Virtual Network and created, named Soclab:  <br/>
<img src="./images/filename2.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Navigated to Virtual Machines and created CORP-NET-EAST1: <br/>
<img src="./images/filename3.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Editing the Network Security Group by removing RDP inbound rule:  <br/>
<img src="./images/filename4.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Creating the new "Danger_AllowAny" Rule and using "*" in the Destination port range to allow any traffic:  <br/>
<img src="./images/filename5.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Using the public IP assigned to my vm and remoting in:  <br/>
<img src="./images/filename6.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Once inside naviagated to wf.msc for windows firewall and disabled the internal windows firewall:  <br/>
<img src="./images/filename7.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Pinged vm from local computer to ensure I could reach it over the public internet. This step ensured that if i could, hackers could also:  <br/>
<img src="./images/filename8.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Logged out of my vm and intentionally failed logging in to create log data:  <br/>
<img src="./images/filename9.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
More failed logins with a non-existent user:  <br/>
<img src="./images/filename10.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Opened windows Event Viewer and filtered for Event ID 4625 to display my failed logins:  <br/>
<img src="./images/filename11.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Back in Azure I created a Log Analytics Workspace that would serve as my log repository:  <br/>
<img src="./images/filename12.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Created Microsoft Sentinel, added my log analytics workspace to it, clicked content hub and installed Windows Security Events:  <br/>
<img src="./images/filename13.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
After installation I configured it. This configuration provides connection from my VM to my log analytics workspace via the Windows Security Azure Monitoring Agent:  <br/>
<img src="./images/filename14.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Created a data collection rule via the AMA:  <br/>
<img src="./images/filename15.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Now with AMA configured, logs can be sent to our Log Analytics Workspace which is also connected to Micorsoft Sentinel; where the logs are accessed. As seen here:  <br/>
<img src="./images/filename16.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Using KQL I queried the windows logs and you can see a difference from the above photo in log attempts. The above photo just displayed my failed logons from earlier. I allowed 30 mins to pass and you can see there are 839 results at the bottom right:  <br/>
<img src="./images/filename17.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
From here I created the following query for Brute Force Detection. Failed logons grouped by IP and account, alerting if the same IP has more than 1 failed login within 30 mins, and sorting in descending order :  <br/>
<img src="./images/filename18.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
I used this query to create an alert,mapped it to MITRE T1110 for Brute force and set it to email me when triggered:  <br/>
<img src="./images/filename19.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
From here I created a playbook to email and post a message via a communication app:  <br/>
<img src="./images/filename20.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Running the playbook:  <br/>
<img src="./images/filename21.png" height="80%" width="80%" alt="Honeypot"/>
<br />
<br />
Email correspondence from the configurations set:  <br/>
<img src="./images/filename22.png" height="80%" width="80%" alt="Honeypot"/>
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
