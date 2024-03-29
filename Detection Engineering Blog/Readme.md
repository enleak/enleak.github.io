
# Introduction To Detection Engineering

![image](https://github.com/enleak/enleak.github.io/assets/55566953/76bfb6c2-ceb1-4b71-ac4b-e3311b0c7572)

 
Source: https://redcanary.com/blog/detection-engineering/ 


It is crucially important to be able to detect and monitor threats that may cause damage to the assets you are protecting. To clarify, even though it is important to have security appliances in place such as a network firewalls, security information and event management (SIEM), intrusion detection/prevention (IDS/IPS), endpoint detection and response (EDR) in your environment, you want to make sure that they are tuned correctly in order to detect threats and malicious activity instead of flooding your dashboards with false positive alerts. SOC's require expertise in understanding the tactics, techniques, and procedures employed by threat actors and translating that knowledge into effective detection strategies. When it comes to detection and monitoring, time is of the essence, so you want to make sure that your appliances can correctly and quickly be able to distinguish between what is actually a threat or malicious application vs what is a false positive. Detection engineering is about creating a culture, as well as a process of developing, evolving, and tuning detections to defend against current threats. To give an example, indicators of compromise (IOC's) such as hashes, IP addresses, and other artifacts are seen as clues to better understand a cybersecurity attack and to help clarify what happened during the attack. These indicators of compromise can be implemented into the security appliances in order to improve the detection logic in place and to identify an attack based on those characteristics. Overall, detection engineering can be thought of as dealing with designing, developing, testing, and maintaining threat detection logic which can can be a "rule, a pattern, or even a textual description”.


 ![image](https://github.com/enleak/enleak.github.io/assets/55566953/22962552-a3cf-4494-bf12-d8d783fb2c1f)

Source: https://twitter.com/kwm/status/1260599938590797824 


For this part of the blog, I will be covering the difference between threat hunting and detection engineering. First, threat hunting is a proactive approach to threat prevention where threat hunters look for anomalies that can potentially be cyber threats lurking undetected in your systems. Combined with threat intelligence, hunting enables organizations to
 
+	Better understand the attack surface
+	Expose cyber criminals as early as possible – before they compromise the system.
  

On the other hand, threat detection is the "practice of identifying any malicious activity that could compromise the network and then composing a proper response to mitigate or neutralize the threat before it can exploit any present vulnerabilities". Unlike threat hunting, threat detection is a reactive approach: threat mitigation mechanisms activate only when the organization’s security system receives alerts on potential security breaches. For example, once a threat is detected, security teams can further analyze them to find its impact on the organization and take necessary security measures to remove them. Threat hunting is the practice of proactively searching for cyber threats lurking undetected in a network, whereas detection engineering aims to identify threats before they can do significant damage.

 ![image](https://github.com/enleak/enleak.github.io/assets/55566953/e054e40a-094d-4500-91cc-f107099ffd60)

Source: https://kostas-ts.medium.com/threat-hunting-series-detection-engineering-vs-threat-hunting-f12f3a72185f 


We will now discuss how detection engineering works from a more workflow perspective. First, you need to understand the data sources used in your environment, for example, what do you use to log the events happening in your environment and what protocols do you use to make sure those events are being logged in the appropriate security mechanisms, such as a SIEM and Syslog. An understanding of what normal or benign traffic looks like so you can use that to compare against traffic or events happening in your environment that may be suspicious or malicious based upon what you consider to be baseline traffic which is crucially important to not only understand, but implement, so that way you can have a better understanding of what is happening in your environment and most importantly, reduce false positives to find the true positives that need to be attended to. For more information about syslog, this blog from Crowd strike does an excellent job of helping you better understand why I mentioned it and its purpose: https://www.crowdstrike.com/guides/syslog-logging/ . Additional sources of information that is really beneficial to understand in your environment are:

•	Process and process command line monitoring, often collected by Sysmon, Windows Event Logs, and many EDR platforms
•	File and registry monitoring, also often collected by Sysmon, Windows Event Logs, and many EDR platforms
•	Authentication logs, such as those collected from the domain controller via Windows Event Logs
•	Packet capture, especially east/west capture such as that collected between hosts and enclaves in your network by sensors such as Zeek

![image](https://github.com/enleak/enleak.github.io/assets/55566953/145d9a03-a01f-49f2-bc30-50774663494f)

 
Source: https://attack.mitre.org/ 
Next, we will focus on the suspicious and malicious traffic happening in your network, and a great resource that I definitely recommend becoming aware of and understanding is the MITRE ATTT&CK framework which is a globally accessible knowledge base of adversary tactics and techniques based on real-world observations. Furthermore, the ATT&CK knowledge base is used as a foundation for the development of specific threat models and methodologies in the private sector, in government, and in the cybersecurity product and service community. After we have established for you a foundational understanding of what the MITRE ATT&CK framework is, we can use what is called tactics, techniques, and procedures to help you better understand how an attack works and the overall flow of an attack to help you better understand the mindset of an attack such as how do they conduct an attack on your environment. To clarify, I have included an image below defining what is tactics, techniques, and procedures to help you visually understand what the difference is between them to provide clarification:
Source: https://www.balbix.com/insights/tactics-techniques-and-procedures-ttps-in-cyber-security/ 
	Now that we have clarified the importance of understanding the different data sources used in your environment, what is benign or normal activity in your environment and what would be considered suspicious or malicious traffic that might correlate to an attacker, we will now discuss about how to map the activity(TTPs) to create detection rules in your environment to improve detection engineering or implement it successfully. For example, Custom detection rules are rules which make it possible to proactively monitor various events and system states. Furthermore, Each detection rule can be configured to run at regular intervals and generated alerts and taking “automated” response actions when there is a match. To clarify, every environment is different in terms of sector in which it operates in such as healthcare, construction, industrial control systems, assets involved such as amount and type, and other factors, so it is important to understand what is considered suspicious and malicious activity that correlates to attacker’s TTPs that you can learn more about using the MITRE ATT&CK link I provided in the previous image. An example to implement this would be using Microsoft Defender for Endpoint which is a EDR solution that stands for endpoint detection and response, but also Microsoft Defender for Endpoint is an enterprise endpoint security platform designed to help enterprise networks prevent, detect, investigate, and respond to advanced threats and for more information about the platform it can be found using this link: https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint?view=o365-worldwide After that, you will first use the query language that the platform provides you to create your own detection rule, which is the Kusto Query Language(KQL) which is an open-source language created by Microsoft to query big data sets stored in the Azure cloud, but also is used in Log Analytics, Sentinel, Defender for Endpoint, Azure Data Explorer, and some more resources used in the Microsoft landscape. For examples, I have listed one down below to get a visually representation of how to implement them and the syntax associated with the query language:
Source: https://jeffreyappel.nl/microsoft-defender-for-endpoint-series-advanced-hunting-and-custom-detections-part8/ 
Moving on, you will then Create the KQL query in the query editor and select Create detection rule to specify all required information including the generic details and this is very important for custom detection rules and in general for detection engineering to make your rules more clear and concise which is to create a clear and short title with the most information, such as Don’t use alert titles such as “Custom detection 1”.
https://jeffreyappel.nl/microsoft-defender-for-endpoint-series-advanced-hunting-and-custom-detections-part8/ Third, like I previously mentioned, it is important to understand the normal activity happening in your environment and what is considered suspicious and malicious activity that may be associated with attackers, so with that being said, you will use the GUI to create the custom detection rule details that you want to include and go step by step in terms of completing the instructions because each tool is different in terms of custom detection and UI, but these are the steps that I would recommend when it comes to detection engineering:
 





Source: https://jeffreyappel.nl/microsoft-defender-for-endpoint-series-advanced-hunting-and-custom-detections-part8/
1.	Understand the different data sources used in your environment used to log events and track activity happening in your environment.
2.	Create a baseline of benign or normal traffic and establish that to be compared against activity happening in your environment in real-time.
3.	Use frameworks such as the MITRE ATT&CK Framework to get a better understanding of the mindset of an attacker from a TTP perspective such as tactics, techniques, and procedures. Another really great resource is from red canary that you can find using these link: Welcome to the Red Canary 2023 Threat Detection Report
4.	Use the security mechanisms you have in place when it comes to creating detection rules such as Microsoft Defender for Endpoint to create the custom queries to correlate to a specific custom detection rule.
5.	Make sure to continuously test and update the rule to get the best results because of the constantly evolving cybersecurity threat landscape and attacker’s tactics, techniques, and procedures.
6.	Automate these process and repeat each step and continuously improve existing  detection and custom detection rules  and create new detection rules for new alerts to be able to detect them.
7.	Most importantly, make sure to tune the rules so that way you don’t get alert fatigue from a plethora of false positives, so its imperative to make sure that your custom detection and detection rules are clear and concise and tested constantly and most importantly: consistently.
8.	Repeat
In conclusion of this section, if you want to learn more about how to implement custom detection rules using Microsoft Defender for endpoint that I used for how to implement detection engineering in your environment as an example, the information can be found using this link: https://jeffreyappel.nl/microsoft-defender-for-endpoint-series-advanced-hunting-and-custom-detections-part8/
