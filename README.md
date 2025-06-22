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
Create Resource Group: <br/>
<img src="https://i.imgur.com/tRm4NJg.png" height="80%" width="80%" alt="Honeypot Steps"/>
<br />
<br />
Navigate to Virtual Network and Create, named Corp-Net-East1:  <br/>
<img src="https://i.imgur.com/47MX9lj.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Navigate to Virtual Machines and Create: <br/>
<img src="https://i.imgur.com/jWwAmip.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Editing the Network Security Group by removing RDP inbound rule:  <br/>
<img src="https://i.imgur.com/Pkv8H5y.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Creating the new "Danger_AllowAny" Rule and using "*" in the Destination port range to allow any traffic:  <br/>
<img src="https://i.imgur.com/w55HAu6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Using the public IP assigned to my vm and remoting in :  <br/>
<img src="https://i.imgur.com/35tp9YL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Once inside naviagated to wf.msc for windows firewall and disabled the internal windows firewall:  <br/>
<img src="https://i.imgur.com/rUD5wwo.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Pinged vm from local computer to ensure I could reach it over the public internet. This step ensured that if i could, hackers could:  <br/>
<img src="https://i.imgur.com/gGu4cVJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Logged out of vm and logged back in with failed attempts to create log data:  <br/>
<img src="https://i.imgur.com/Ks7kAO3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
More failed logins with a non-existent user:  <br/>
<img src="https://i.imgur.com/ReT65QS.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Opened windows Event Viewer and filter for Event ID to 4625 to display my failed logins:  <br/>
<img src="https://i.imgur.com/6SyeThr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Created a Log Analytics Workspace that would serve as my log repository:  <br/>
<img src="https://i.imgur.com/T43QOlm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Created Microsoft Sentinel, added my log analytics workspace to it, clicked content hub and installed Windows Security Events:  <br/>
<img src="https://i.imgur.com/cItEHJ8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
After installation I configured it. Allowing connection from my VM to my log analytics workspace via the Windows Security Azure Monitoring Agent:  <br/>
<img src="https://i.imgur.com/KilkyW2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Created a data collection rule via the AMA:  <br/>
<img src="https://i.imgur.com/yUlUamX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Now with AMA configured, logs can be sent to our Log Analytics Workspace which is also connected to Micorsoft Sentinel; where the logs are accessed:  <br/>
<img src="https://i.imgur.com/UpJeE1z.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Using KQL I queried the windows logs and you can see a difference from the above photo in log attempts. The above photo just displayed my failed logons from earlier. I allowed 30 mins to pass and you can see there are 839 results at the bottom left:  <br/>
<img src="https://i.imgur.com/pAWaHcl.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
From here I created the following query for Brute Force Detection. Failed logons grouped by IP and account, alerting if the same IP has more than 1 failed login within 30 mins, and sorting in descending order :  <br/>
<img src="https://i.imgur.com/qTtEVPh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
I used this query to create an alert,mapped it to MITRE T110 for Brute force and set it to email me when triggered:  <br/>
<img src="https://i.imgur.com/mcJ6OeD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
From here I created a playbook to email and post a message via a communication app:  <br/>
<img src="https://i.imgur.com/Zrv3sm2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Running the playbook:  <br/>
<img src="https://i.imgur.com/yWeFral.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Some email correspondence for configurations set:  <br/>
<img src="https://i.imgur.com/BaevIyV.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
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
