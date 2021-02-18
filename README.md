# Graylog_Extractors_pfSense

## Overview
Graylog extractors for the following pfSense services:
* Firewall (I do not have IPv6 extractors)
* Suricata

## Explanation
### pfSense firewall logs
In pfSense versions up to 2.5.0, pfSense used logs in the RFC 3164 format. The format of the actual log message is [broken down as follows](https://docs.netgate.com/pfsense/en/latest/monitoring/logs/raw-filter-format.html) (hostname is not included in syslog data sent to remote log hosts):  
```<Timestamp> <Hostname> filterlog: <CSV data>```  

The CSV data consists of the following:
* Common fields (this is in all messages)
* Then IPv4 or IPv6 specific data
* Then IP data
* Then protocol-specific data (e.g., TCP, UDP, or ICMP)

This leaves a total of 13 combinations (below). For my purposes, I'm only using IPv4 and the ICMP responses (10 combinations).
* IPv4 TCP
* IPv4 UDP
* ~~IPv6 TCP~~
* ~~IPv6 UDP~~
* Eight different responses for ICMP
  * ICMP Echo
  * ICMP Unreachable protocol
  * ICMP Unreachable port
  * ICMP Uncreachable other
  * ICMP Need Frag
  * ICMP TStamp
  * ICMP TStamp Reply
  * ICMP Default
* ~~CARP/VRRP~~

In pfSense versions 2.5.0 and up, pfSense [offers the options](https://docs.netgate.com/pfsense/en/latest/monitoring/logs/settings.html) of either RFC 3164 (legacy) or RFC 5424 (modern). Their RFC 3164 format slightly changed from 2.4.5-p1, so new extractors are needed.

### pfSense Suricata logs
I'm assuming you have Suricata installed, your lists configured, and interfaces setup. If so, go to *Services*, then *Suricata*, and then click *Edit* on your interface. Enable *Send Alerts to System Log* and configure *Log Facility* and *Log Priority* (I left mine at the default).
![screenshot](img/20201216_003.png)

## pfSense remote logging configuration
Go to *Status*, then *System Logs*, then *Settings*, and scroll down to *Remote Logging Options*.

You may want to change some settings under *General Logging Options*. For example, I make sure to enable *Log packets matched from the default block rules in the ruleset* so that I can see things blocked from coming into my network.
![screenshot](img/20201216_002.png)

Enable *Send log messages to remote syslog server*, input the Graylog server name and port in the box (you can send logs to up to three remote log servers), then select *System Events* and *Firewall Events*.
![screenshot](img/20201216_001.png)

Click *Save* at the bottom of the page when you are done.

## Graylog configuration
Go to *System/Inputs*, then *Inputs*. From the dropdown, select an input of type *Syslog UDP* and click *Launch new input*. From the *Node* dropdown, select your node. Under *Port*, set the port you specified in pfSense (e.g., 514). 

Click *Save* at the bottom of the page when you are done.

Then, click *Manage extractors*, then click *Actions*, then *Import extractors*.  
![screenshot](img/20190701_002.png)

Copy/paste the raw JSON file below into the box and click *Add extractors to input*. Repeat for each file you want to import.  

**pfSense versions up to and including 2.4.5-p1 (RFC 3164)**
 - [Firewall](https://raw.githubusercontent.com/loganmarchione/Graylog_Extractors_pfSense/master/RFC_3164/pfSense_Extractors_Firewall.json)  
 - [Suricata](https://raw.githubusercontent.com/loganmarchione/Graylog_Extractors_pfSense/master/RFC_3164/pfSense_Extractors_Suricata.json)  

**pfSense version 2.5.0 (RFC 3164 - Netgate slightly changed the format)**
  - [Firewall](https://raw.githubusercontent.com/loganmarchione/Graylog_Extractors_pfSense/master/RFC_3164/pfSense_Extractors_Firewall_2.5.0.json)
