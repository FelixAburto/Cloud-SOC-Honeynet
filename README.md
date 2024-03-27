<!--# Azure-SOC-Honeynet-Project-->
# Building a SOC + Honeynet in Azure (Live Traffic)
![SOC Honeynet (1)](https://github.com/0xbythesecond/Azure-SOC-Honeynet-Project/assets/23303634/43177fa9-4746-4f8d-8774-f9aca74b891d)

## Introduction
For this project, I set up a small-scale HoneyNet on Azure and directed log data from various sources into a Log Analytics workspace. This data feeds into Microsoft Sentinel, enabling the creation of attack maps, the triggering of alerts, and the generation of incidents. I first tracked security metrics in this not-yet-secured environment for 24 hours. Then, after applying security enhancements to make it more robust, I monitored the same metrics for another 24 hours to compare the results. The findings are presented below.


## Azure Resources Deployed, Technologies, and Regulations used:

- A virtual networking service in Azure (VNet)
- Security group for network-level security in Azure (NSG)
- Two Windows 10 Pro virtual machines and one Linux server virtual machine
- Workspace for log analytics using Kusto Query Language (KQL)
- Secure secrets management using Azure Key Vault
- Data storage using an Azure Storage Account
- Security information and event management with Microsoft Sentinel
- Protection of cloud resources via Microsoft Defender for Cloud
- Remote desktop service for accessing Windows remotely
- System management through Command Line Interface (CLI)
- Automation and configuration management with PowerShell
- Applying security controls based on NIST SP 800-53 Rev 4
- Guidance for incident handling according to NIST SP 800-61 Rev 2
 
## Course of Action
- ***Setting Up the Honeynet:*** Initially, I constructed the honeynet using Virtual Machines. This involved turning off the internal firewall and setting the Network Security Group (NSG) to accept all incoming ports and traffic.

- ***Observation and Analysis:*** I configured the Azure setup to efficiently collect logs from various sources into a specific log analytics workspace. Using Microsoft Sentinel's advanced features, I created detailed attack maps, triggering precise alerts and generating thorough incidents based on the thoroughly analyzed log data.

- ***Monitoring and Assessing Security Metrics:*** Over a full day, I kept an eye on the unprotected environment, recording key security metrics. This data provided a baseline for later comparison after enhancing security.

- ***Resolving Security Issues:*** After addressing the incidents and pinpointing vulnerabilities, I strengthened the environment by applying security best practices and Azure's recommended strategies.

- ***Post-Remediation Analysis:*** I spent another 24 hours closely reexamining the environment to assess the security metrics again. The new data was carefully compared to the initial baseline, allowing for a detailed comparative study.

We'll be presenting the following metrics:

- SecurityEvent for tracking Windows Event Logs
- Syslog to capture Linux Event Logs
- SecurityAlert, which encompasses alerts triggered in Log Analytics
- SecurityIncident for incidents identified by Sentinel
- AzureNetworkAnalytics_CL, focusing on malicious flows permitted into our honeynet


## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/gBvHJo4.gif)
During the initial "BEFORE" assessment phase, we noticed that all resources had been set up with direct exposure to the internet. The Virtual Machines had Network Security Groups configured to be open and their built-in firewalls set to allow most traffic. Additionally, other resources were launched with endpoints that were publicly accessible, making the use of Private Endpoints redundant.

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/oQtbais.gif)
During the "AFTER" evaluation phase, we strengthened the Network Security Groups to block all traffic except that from my administrative workstation. Furthermore, we enhanced the security of other resources by utilizing their built-in firewalls and introducing Private Endpoint functionality.

## Attack Maps Before Hardening / Security Controls
The graphic shown below offers a snapshot of attack attempts aimed at a publicly exposed Microsoft SQL server over a 24-hour period. The data points marked on the map indicate the exact locations from which these attacks or login attempts originated.
![MSSQL Allowed Access](https://i.imgur.com/UHVHIGM.png) <br />

The attack map displayed illustrates numerous authentication failures recorded in the syslog of the Linux server I set up. It highlights the existence of unauthorized attempts to access the server from external sources outside the local network. This underscores the critical importance of securing Linux servers with strong authentication methods and meticulously monitoring system logs to identify and prevent possible intrusions.
![Linux Syslog Auth Failures](https://i.imgur.com/8QbjEwL.png) <br />

The attack map shown captures a series of failed attempts using RDP (Remote Desktop Protocol) and SMB (Server Message Block), clearly demonstrating the persistent efforts of potential attackers targeting these protocols. The visual representation highlights the crucial need to strengthen remote access and file-sharing services, serving as a defensive measure against unauthorized access and reducing the risk of potential cyber threats.
![Windows RDP/SMB Auth Failures](https://i.imgur.com/ALHFE3u.png) <br />

The attack map displayed is a striking demonstration of the consequences of leaving the Network Security Group (NSG) open, which allows malicious network traffic to enter unobstructed. This visual aids in underlining the importance of implementing strong security measures, such as enforcing strict NSG rules, as a way to prevent unauthorized access and reduce the risks associated with potential threats.
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/W2iCXmv.png)


## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
<br />
`Start Time:` 2024-02-18T18:12:23 <br/>
`Stop Time:` 2024-02-19T18:12:23

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 48632
| Syslog                   | 3220
| SecurityAlert            | 6
| SecurityIncident         | 237
| AzureNetworkAnalytics_CL | 516

## Attack Maps After Hardening / Security Controls

  >**Note**: All map queries actually returned no results due to no instances of malicious activity for the 24-hour period after hardening.

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we applied security controls:
<br />
`Start Time:` 2024-02-19T18:12:23 <br/>
`Stop Time:` 2024-02-20T18:12:23

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 865
| Syslog                   | 19
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Change after Securing Environment
| Metric                                          | Percent
| ----------------------------------------------- | -----
| Security Event (Windows VM)                     |  -98.04%
| Syslog (Linux VM)                               |  -99.33%
| Security Alert (Microsoft Defender for Cloud)   |  -100.00%
| Security Incident (Sentinel Incidents)          |  -100.00%
| NSG Inbound Malicious Flows Allowed             |  -100.00%


## Reflection
Creating this lab and analyzing real-world traffic with attack maps and KQL data has been both challenging and gratifying. It was amazing to see it all come together, illustrating both an insecure and a secure environment. Before implementing security controls, the environment was flooded with malicious traffic. While the resources were left vulnerable, I observed various IP addresses and usernames that bad actors attempted to use to access my virtual machines. However, after completing the hardening process and waiting 24 hours, it was remarkable to see no signs of allowed traffic from these bad actors on the public internet – a truly satisfying outcome.

## Conclusion

In this project, we set up a small-scale honeynet on the Microsoft Azure platform and effectively integrated various log sources into a specific Log Analytics workspace. A key component of this setup was Microsoft Sentinel, which actively produced alerts and initiated incidents based on the ingested logs. We carefully measured detailed metrics in the vulnerable environment before applying any security controls and then conducted a follow-up evaluation after strengthening the infrastructure. The standout result was a marked decrease in the number of security events and incidents, clearly demonstrating the effectiveness of the security measures we put in place.

I should recognize that if the network's resources had been in active use by regular users, it's likely that a higher volume of security events and alerts might have been generated in the 24-hour period following the implementation of the security controls.
