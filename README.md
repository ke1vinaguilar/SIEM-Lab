<h1>SIEM Lab</h1>


<h2>Description</h2>
In this lab I will setup Azure Sentinel (SIEM) and connect it to a live virtual machine acting as a honey pot. We will observe live attacks (RDP Brute Force) from all around the world. I will use a custom PowerShell script to look up the attackers Geolocation information and plot it on an Azure Sentinel Map

The Powershell script in this repository is responsible for parsing out Windows Event Log information for failed RDP attacks and using a third party API to collect geographic information about the attackers location.
<br />


<h2>Languages and Used</h2>

- <b>PowerShell:</b> Extract RDP failed logon logs from Windows Event Viewer

<h2>Utilities Used </h2>

- <b>ipgeolocation.io:</b> IP Address to Geolocation API

<h2>Program walk-through:</h2>

<p align="center">
Create your Azure account: <br />
<a href="https://azure.microsoft.com/en-us/free/">Azure</a>
<br />
<br />
Go to https://portal.azure.com/: <br />
<img src="https://i.imgur.com/CxArIyU.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
To create our VM type in “Virtual Machine” in the search bar and under "Services" click on “Virtual Machine”: <br />
*The VM be exposed on the internet which will be attacked/ our honeypot* <br />
<img src="https://i.imgur.com/OAsyjyQ.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Create a new resource group “Honeypotlab”: <br />
<img src="https://i.imgur.com/69yr1Ng.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Name the VM “honeypot”: <br />
- For "Region" select “(US) West US 3” which is the data center it’s going to exist in <br />
<img src="https://i.imgur.com/c8q9zuz.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
For “Administrator account” create a username and password <br />
<img src="https://i.imgur.com/T715HZp.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Under licensing click the box that says “I confirm I have an eligible Windows 10 license with multi-tenant hosting rights.”:  <br />
<img src="https://i.imgur.com/kC6LRvU.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Select “Next” until you get to “Networking”
-In “NIC network security group” (which is like the Firewall) select “Advanced”:  <br />
<img src="https://i.imgur.com/ff0VSMx.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-Select “Create new” <br />
-Under “inbound rules” select “…” and “Remove” the default rules <br />
<br />
<br />
Select “Add inbound rule”:  <br />
<img src="https://i.imgur.com/S4Q8RPH.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-Under “Destination port ranges” type in “*” for anything <br />
-Under “Protocol” select “Anything” <br />
-Under “Action” select “Allow” <br />
-Under “Priority” type in “100” <br />
-Name the inbound security rule “DANGER_ANY_IN” and then select “Add” <br />
*this is going to allow all traffic from the internet into our virtual machine* <br />
<img src="https://i.imgur.com/9Ggf6IG.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Select “OK” and then select “Review + Create”:  <br />
<img src="https://i.imgur.com/b7ibtDr.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<img src="https://i.imgur.com/TXf116i.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Type in “Log Analytics workspaces” in the search bar and select the service:  <br />
<img src="https://i.imgur.com/n76cDwg.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
*Purpose is to ingest logs from the Virtual Machine, we’re going to ingest the windows event logs and we’re going to create our own custom log that contains geographic information so we can discover where the attackers are coming from* <br />
<br />
<br />
Click on “Create log analytics workspace” *where the logs will be stored*:  <br />
<img src="https://i.imgur.com/tc2hR7W.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-Select the resource group “Honeypotlab” <br/>
-Under “Instance details” for the “Name” type in “law-honeypot1” <br />
-For the "Region" select “West US 3” and select “Review + Create” <br />
-Click “Create” <br/>
<img src="https://i.imgur.com/fmUcDmV.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Type in “Security” in the search bar and select “Microsoft Defender for Cloud”:  <br />
<img src="https://i.imgur.com/MmlsOz2.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Under “Management” select “Environment Settings” and click on “law-honeypot1”: <br />
- Turn “Azure Defender on” <br />
- Turn “SQL servers on machines” off <br />
-Select “Save” <br/>
<img src="https://i.imgur.com/6UHfjR8.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Select “Data collection” and select “All Events” then “Save”:  <br />
<img src="https://i.imgur.com/JbtTUpS.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Go back to “Log Analytics workspaces”: <br />
-Select “law-honeypot” <br />
<img src="https://i.imgur.com/Dr7FUWE.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Under “Workspace Data Sources” select on “Virtual machines”: <br />
- click on the “honeypot-vm” and select “Connect” <br />
<img src="https://i.imgur.com/P3mAsEi.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Open a new tab and type in https://www.portal.azure.com/:  <br />
<img src="https://i.imgur.com/Vkew2i6.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Type in “sentinel” in the search bar and select “Microsoft Sentinel” under “Services”:  <br />
<img src="https://i.imgur.com/9KYU4iW.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “Create Azure Sentinel”:  <br />
<img src="https://i.imgur.com/JNOtw7r.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Select the “law-honeypot1” log analytics workspace and select “Add”:  <br />
<img src="https://i.imgur.com/r2ibICK.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Go back to the previous tab and type in “virtual machine” in the search bar and select the “Virtual Machine” service:  <br />
<img src="https://i.imgur.com/P7KQODG.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “honeypot-vm” and “copy to clipboard” the Public IP address:  <br />
<img src="https://i.imgur.com/UAe2IFW.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click the “Start” menu on the computer and search “Remote Desktop Connection”:  <br />
<img src="https://i.imgur.com/wdTxo6e.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Paste the Public IP address and select “Connect”:  <br />
<img src="https://i.imgur.com/lZYV7fQ.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Type in the following credentials: <br />
-Username: nameadmin <br />
-Password: xxxxxxx <br />
<br />
<br />
Click on the “Don’t ask me again for connections to this computer” box: <br />
-Click on “Yes” <br />
<br />
<br />
Select “No” to all privacy settings:  <br />
<img src="https://i.imgur.com/pRb3lBA.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
In the Windows search bar type in “Event Viewer” and select it:  <br />
<img src="https://i.imgur.com/NhdhpvU.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “Windows Logs” and click on “Security” and all the security events will populate: <br />
<img src="https://i.imgur.com/usKFQA1.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
*we’re going to be focusing on the Event ID “4625”* <br />
<br />
<br />
Select the log labeled “Audit Failure” and observe the IP address:  <br/>
<img src="https://i.imgur.com/s2U5rMF.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
*We’re going to programmatically take the IP address with PowerShell and then use an IP Geolocation API. Essentially, we’re going to post the IP address to the API and then the API is going to return the attackers location* <br />
*We’re going to turn off the firewall so its susceptible to it so it can respond to ICMP Echo requests so people can discover it on the internet faster. <br />
<br />
<br />
Minimize the VM and on the personal computer open the “Command Line Prompt” and type in “ping PublicIPAddress -t”:  <br />
<img src="https://i.imgur.com/hCK5m5U.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
*it shouldn't timeout* <br />
<br />
<br />
Go back to the VM select the “Start Menu” and type in “wf.msc”:  <br/>
<img src="https://i.imgur.com/2gA0r94.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on Windows “Defender Firewall Properties” and select “Domain Profile” and turn off “Firewall state”:  <br />
<img src="https://i.imgur.com/yGLpeJu.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-Select “Private Profile” and turn off “Firewall state” <br />
-Select “Public Profile” and turn off “Firewall state” <br />
<img src="https://i.imgur.com/OIaACv4.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-Select “Apply” and then select “OK” <br />
*The personal computer should be able to ping the VM since echo requests are allowed* <br />
<br />
<br />
Go back to the Virtual Machine and open up the web browser: <br />
-Type in https://github.com/joshmadakor1/Sentinel-Lab  <br/>
<br />
<br />
Click on “Custom_Security_Log_Exporter.ps1” and copy the code:  <br/>
<img src="https://i.imgur.com/IIIC0Yt.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
 <img src="https://i.imgur.com/Z3HGTbW.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Open “PowerShell ISE” on the VM:  <br/>
<img src="https://i.imgur.com/nfanyw5.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “New” and paste the code:  <br/>
<img src="https://i.imgur.com/TPbJYob.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Save on the Desktop as “Log_Exporter:  <br/>
<img src="https://i.imgur.com/BGBDBCF.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Get Geolocation.io API Key to insert into code: <br />
- Go to https://ipgeolocation.io/ <br />
- Click on “Sign Up” and fill in the form <br />
<img src="https://i.imgur.com/pDnRVoc.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Log In to https://ipgeolocation.io/:  <br/>
<img src="https://i.imgur.com/pLeqsou.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Obtain your API key:  <br/>
<img src="https://i.imgur.com/3OCl8Xc.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Insert it into the code:  <br/>
<img src="https://i.imgur.com/fy5IlKJ.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Run the script:  <br/>
<img src="https://i.imgur.com/yiRbLJ6.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Go back to Azure and create a custom by typing “Log Analytics Workspace” in the search bar: <br />
Click on the workspace, “law-honeypot1” <br />
<img src="https://i.imgur.com/ER0QVft.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
In the drop-down list go under “Settings” and click on “Custom logs”:  <br/>
<img src="https://i.imgur.com/ewb2xpn.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “Add custom log”:  <br/>
<img src="https://i.imgur.com/G2uSkfX.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Go back to the VM and copy the log: <br />
-Press Windows key + R and type in “C:\ProgramData\” <br />
<img src="https://i.imgur.com/BYRTKH3.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Open up the “failed_rdp” text document and copy the contents in it:  <br/>
<img src="https://i.imgur.com/jjMLSyY.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Open up “NotePad” in the personal desktop and paste the log in:  <br/>
<img src="https://i.imgur.com/qUNXOWy.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Save on the Desktop as “failed_rdp.log”:  <br />
<img src="https://i.imgur.com/B2G4VuR.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
In “Sample log” browse the file and select “failed_rdp.log” as the sample log to train log analytics what to look for:  <br />
<img src="https://i.imgur.com/Z0fsyZP.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<img src="https://i.imgur.com/hAvl2Z5.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Click “Next” to see the preview then select “Next”:  <br/>
<img src="https://i.imgur.com/szSIi4P.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
In “collection paths” under “Type” select “Windows” and under “Path” type “C:\programdata\failed_rdp.log”. Press “Next”:  <br/>
<img src="https://i.imgur.com/Svn3Xn1.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Under “Details” for “Custom log name *” type in “FAILED_RDP_WITH_GEO” and select “Next”:  <br/>
<img src="https://i.imgur.com/BAfaZfs.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Press “Create”:  <br/>
<img src="https://i.imgur.com/sF3b4q6.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
*Under Logs we can see the custom entries* *”Security Event” is the windows log event that we can use immediately” <br />
<br />
<br />
Click "Run" for logs to come through:  <br/>
*Wait 10-15 minutes for the logs to start populating* <br />
<img src="https://i.imgur.com/zoOF3nE.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Extract certain fields from the raw data:  <br />
-Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’” <br />
<img src="https://i.imgur.com/CGrdPEp.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
- For Latitude, Highlight the field, in “Field Title” type in “latitude”, in “Field Type” select “Numeric”, and click on “Extract”, then click on “Save Extraction” <br />
<img src="https://i.imgur.com/Nb7dY3h.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<img src="https://i.imgur.com/cuudDwM.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/BElaYAr.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For Longitude, Highlight the field, in “Field Title” type in “longitude”, in “Field Type” select “Numeric”, and click on “Extract”, then click on “Save Extraction” <br />
<img src="https://i.imgur.com/sXwzsZ0.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
*Sometimes it selects the wrong field so you have to edit it to train the extract algorithm and highlight the correct field* <br />
<img src="https://i.imgur.com/jEAiyTs.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/qshWNeL.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For Destination Host, Highlight the field, in “Field Title” type in “destinationhost”, in Field Type” select “Text” and click on “Extract”, then click on “Save Extraction” <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/px5j42Q.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For username, Highlight the field, in “Field Title” type in “username”, in Field Type” select “Text” and click on “Extract”, then click on “Save Extraction” <br />
<img src="https://i.imgur.com/FuXiiXM.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/zcXgwKQ.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For Source Host (IP address), Highlight the field, in “Field Title” type in “sourcehost”, in Field Type” select “Text” and click on “Extract”, then click on “Save Extraction” <br />
<img src="https://i.imgur.com/EkaQ1oI.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/QF3gsls.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For State, Highlight the field, in “Field Title” type in “state”, in Field Type” select “Text” and click on “Extract”, then click on “Save Extraction” <br />
<img src="https://i.imgur.com/N7BipcE.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/EVckjdV.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For Country, Highlight the field, in “Field Title” type in “country”, in Field Type” select “Text” and click on “Extract”, then click on “Save Extraction” <br />
<img src="https://i.imgur.com/0OywapV.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/AQfLQUj.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For Label (to label stuff on the map [country – IP address]), Highlight the field, in “Field Title” type in “label”, in Field Type” select “Text” and click on “Extract”, then click on “Save Extraction” <br />
<br />
<br />
Right-click the date (ex. 3/13/2023, 11:27:47.079 PM) and click on “Extract fields from ‘FAILED_RDP_WITH_GEO_CL’”:  <br/>
<img src="https://i.imgur.com/CqdFCI9.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-For Timestamp, Highlight the field, in “Field Title” type in “timestamp”, in Field Type” select “Date/Time (ISO 8601 Format)” and click on “Extract”, then click on “Save Extraction” <br />
<img src="https://i.imgur.com/XiSUE1d.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Run the Log Analytics, the custom fields should populate but no data will be shown *data will populate on the subsequent logs*:  <br/>
<br />
<br />
Go back to actual computer for testing and log into our VM via “Remote Desktop Connection” and insert our VM IP address and fail the login to make sure that the logs are coming in and getting parsed out correctly *Should populate in PowerShell ISE and then in Azure Log Analytics in 5-10 minutes*:  <br/>
<br />
<br />
Wait 5-10 minutes then run the Log Analytics, our failed login should populate with the custom fields:  <br/>
<br />
<br />
Open a new tab and go to https://portal.azure.com/ , in the search bar type in “Sentinel” and click on it:  <br/>
<img src="https://i.imgur.com/UYExIOS.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “law-honeypot1” and select “Overview”:  <br/>
<img src="https://i.imgur.com/7bPpVlH.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Under “Threat management” select on “Workbooks”:  <br/>
<img src="https://i.imgur.com/x6ce5Xy.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “Add workbook”:  <br/>
<img src="https://i.imgur.com/BLjQauT.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Click on “Edit”:  <br/>
<img src="https://i.imgur.com/UxpZV64.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Remove the 2 default widgets: <br />
-Click on the 3 dots (…) and select “Remove” and Select “Yes”  <br />
<img src="https://i.imgur.com/61VNGJ1.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<img src="https://i.imgur.com/8m6ocIp.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Click on “Add” and select “Add query”:  <br/>
<img src="https://i.imgur.com/vg2aOU5.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
-Paste the query <br/>
FAILED_RDP_WITH_GEO_CL | summarize event_count=count() by sourcehost_CF, latitude_CF, <br/> longitude_CF, country_CF, label_CF, destinationhost_CF <br />
| where destinationhost_CF != "samplehost" <br />
| where sourcehost_CF != "" <br />
<img src="https://i.imgur.com/nlR1m15.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
- Press “Run Query” <br />
<img src="https://i.imgur.com/W67oEhD.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Under “Visualization” select “Map”: <br />
-Under “Size” select “Full” <br />
-Under “Latitude” select “latitude_CF” <br />
-Under “Longitude” select “longitude_CF” <br />
-Under “Metric Label” select “label_CF” <br />
-Under “Metric Value” select “event_count” <br />
-Press “Apply” <br />
-Press “Save and Close” <br />
<img src="https://i.imgur.com/fj3qX8g.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<img src="https://i.imgur.com/DB8SElj.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
<br />
<br />
Save the map and in “Title” input “Failed RDP World Map”: <br />
-For “Location” select “(US) West US 2” <br />
-Select “Save” <br />
-Select “Done Editing” <br />
<img src="https://i.imgur.com/v24Pcnj.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Turn on Auto refresh for “5 minutes”:  <br/>
<img src="https://i.imgur.com/7QXZcwR.png" height="80%" width="80%" alt="SIEM Lab Steps"/> <br />
*Make sure the PowerShell script in the VM is running” <br />
<br />
<br />
Fix map plot sizes by clicking on “Map Settings” <br />
-Under “Size by” select “event_count” <br />
-Press “Apply” <br />
-Press “Save and Close” <br />
-Select the Save Icon and select “Overwrite their changes” <br />
-Select “Done Editing” <br />
<img src="https://i.imgur.com/fgVnAye.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
Observe the Map:  <br/>
<img src="https://i.imgur.com/ebRWFAv.png" height="80%" width="80%" alt="SIEM Lab Steps"/>
<br />
<br />
 
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
