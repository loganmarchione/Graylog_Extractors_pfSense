# graylog-pfsense-sg1100

## Overview
Instead of having physically independent ethernet ports, the [SG-1100](https://www.netgate.com/solutions/pfsense/sg-1100.html) uses a [Marvell Link Street-88E6341](https://origin-www.marvell.com/switching/assets/LinkStreet_88E6341_Product_Brief.pdf) ethernet switch.
This switch can support six ethernet ports, with built-in support for four physical ethernet ports (the SG-1100 has [three physical ports](https://docs.netgate.com/pfsense/en/latest/solutions/sg-1100/io-ports.html#ethernet-ports)).
Because of this switching chip, pfSense only sees one physical interface, which is the switch itself. Those ports are then broken out into "interfaces" in pfSense via three distinct VLANs (that cannot be removed or changed).

| Interface name  | Port name |
| ------------- | ------------- |
| WAN  | mvneta0.4090 |
| LAN  | mvneta0.4091 |
| OPT  | mvneta0.4092 |

Because the "interfaces" are virtual, most patterns for pfSense will not work, since you need to specifically filter the pattern of *interface.vlan*, instead of just *interface*. 

## Usage
pfense

## pfSense configuration
Go to *Status*, then *System Logs*, then *Settings*, and scroll down to *Remote Logging Options*.

Enable *Send log messages to remote syslog server*, input the Graylog server name and port in the box (you can send logs to up to three remote log servers), and select *Firewall Events*.  
![dashboard](img/20190701_001.png)

You may also want to change some options under *General Logging Options*. For example, I make sure to enable *Log packets matched from the default block rules in the ruleset* so that I can see things blocked from coming into my network.

Click *Save* at the bottom of the page when you are done.

### Explanation
In pfSense 2.2 and above, the format of the actual log message is broken down as follows:
* Common fields (this is in all messages)
* Then IPv4 or IPv6 specific data
* Then IP data
* Then protocol-specific data (e.g., TCP, UDP, or ICMP)

This leaves us with a total of 12 combinations (below). For my purposes, I'm only using IPv4 and the ICMP responses.
* IPv4 TCP
* IPv4 UDP
* IPv6 TCP
* IPv6 UDP
* Eight different responses for ICMP
  * ICMP Echo
  * ICMP Unreachable protocol
  * ICMP Unreachable port
  * ICMP Uncreachable other
  * ICMP Need Frag
  * ICMP TStamp
  * ICMP TStamp Reply
  * ICMP Default

## Graylog configuration
Go to *System/Inputs*, then *Inputs*. From the dropdown, select an input of type *Syslog UDP* and click *Launch new input*. From the *Node* dropdown, select your node. Under *Port*, set the port you specified in pfSense on the SG-1100 (e.g., 8515). 

Click *Save* at the bottom of the page when you are done.

Then, click *Manage extractors*, then click *Actions*, then *Import extractors*.  
![dashboard](img/20190701_002.png)

Copy/paste the raw JSON [here](https://raw.githubusercontent.com/loganmarchione/graylog-pfsense-sg1100/master/sg1100.json) into the box and click *Add extractors to input*.
