# Graylog_Extractors_pfSense

## Overview
This a fork of [Hr46ph's repository](https://github.com/Hr46ph/Graylog_Extractors_pfSense_2.4.4), which is itself a fork of [Hobadee's repository](https://github.com/Hobadee/Graylog_Extractors_pfSense). My changes are as follows:
* Changed the column names to all start with *pf*, which makes them easier to find on the *Search* screen
* Drop any searches for IPv6, since my pfSense instance only sends IPv4 logs

## Explanation
In pfSense 2.2 and above, the format of the actual log message is broken down as follows:
* Common fields (this is in all messages)
* Then IPv4 or IPv6 specific data
* Then IP data
* Then protocol-specific data (e.g., TCP, UDP, or ICMP)

This leaves us with a total of 13 combinations (below). For my purposes, I'm only using IPv4 and the ICMP responses.
* IPv4 TCP
* IPv4 UDP
* ~~IPv6 TCP~~
* ~~IPv6 UDP~~
* Nine different responses for ICMP
  * ICMP Echo
  * ICMP Unreachable protocol
  * ICMP Unreachable port
  * ICMP Uncreachable other
  * ICMP Need Frag
  * ICMP TStamp
  * ICMP TStamp Reply
  * ICMP Default
  * ~~ICMP IPv6~~


## pfSense configuration
Go to *Status*, then *System Logs*, then *Settings*, and scroll down to *Remote Logging Options*.

Enable *Send log messages to remote syslog server*, input the Graylog server name and port in the box (you can send logs to up to three remote log servers), and select *Firewall Events*.  
![dashboard](img/20190701_001.png)

You may also want to change some options under *General Logging Options*. For example, I make sure to enable *Log packets matched from the default block rules in the ruleset* so that I can see things blocked from coming into my network.

Click *Save* at the bottom of the page when you are done.

## Graylog configuration
Go to *System/Inputs*, then *Inputs*. From the dropdown, select an input of type *Syslog UDP* and click *Launch new input*. From the *Node* dropdown, select your node. Under *Port*, set the port you specified in pfSense on the SG-1100 (e.g., 8515). 

Click *Save* at the bottom of the page when you are done.

Then, click *Manage extractors*, then click *Actions*, then *Import extractors*.  
![dashboard](img/20190701_002.png)

Copy/paste the raw JSON [here](https://raw.githubusercontent.com/loganmarchione/Graylog_Extractors_pfSense/master/pfSense%20Extractors.json) into the box and click *Add extractors to input*.
