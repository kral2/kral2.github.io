---
ID: 603
post_title: 'How.too : Configure a GbE interface on Nexus 5xx/55xx/56xx'
author: kral2
post_excerpt: ""
layout: post
permalink: >
  http://www.kral2.fr/how-too-configure-a-gbe-interface-on-nexus-5xx55xx56xx/
published: true
post_date: 2017-02-08 18:32:19
---
When you insert a GLC-T module without any previous configuration, you are likely to get the error " SFP Validation Failed", even if using a compatible module. This is because not all ports are capable to "downgrade" their functional speed to 1GbE : **you have to insert the SFP in an eligible port and configure it**. Best practice is to configure the port's speed setting **before** inserting the SFP module.

GLC-T is the *generic* SKU for a Gigabit Ethernet SFP.

1. Set the port speed
```
conf
int e1/1
speed 1000
```
2. Insert the SFP in a 1/10GbE compatible port :
- for Nexus 5010 : ports 1 to 8,
- for Nexus 5020 : ports 1 to 16,
- for Nexus 5500/5600 : ports 1 to 32.

*Note : On 5500/5600, when a port is configured with `speed auto` it will automatically select the right speed based on the inserted module.*

## References

[http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5000/sw/configuration/guide/cli/CLIConfigurationGuide/BasicEthernet.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5000/sw/configuration/guide/cli/CLIConfigurationGuide/BasicEthernet.html)
*A Cisco Nexus 5000 Series switch has a number of fixed 10-Gigabit ports, each equipped with SFP+ interface adapters. The Nexus 5010 switch has 20 fixed ports, the first eight of which are switchable 1-Gigabit/10-Gigabit ports. The Nexus 5020 switch has 40 fixed ports, the first 16 of which are switchable 1-Gigabit/10-Gigabit ports.*

[http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5500/sw/interfaces/7x/b\_5500\_Interfaces\_Config\_Guide\_Release\_7x/b\_5500\_Interfaces\_Config\_Guide\_Release\_7x\_chapter\_01.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5500/sw/interfaces/7x/b_5500_Interfaces_Config_Guide_Release_7x/b_5500_Interfaces_Config_Guide_Release_7x_chapter_01.html)
*The 5596T switch has 48 base board ports and 3 GEM slots. The first 32 ports are 10GBase-T ports the last 16 ports are SFP+ ports. The 10GBase-T ports support a speed of 1-Gigabit, 10-Gigabit, or Auto. The Auto setting automatically negotiates with the link parser to select either 1-Gigabit or 10-Gigabit speed.*

[http://serverfault.com/questions/339957/how-do-i-get-past-sfp-validation-failed-on-a-sfp-ge-t-transceiver-on-a-nexus-5](http://serverfault.com/questions/339957/how-do-i-get-past-sfp-validation-failed-on-a-sfp-ge-t-transceiver-on-a-nexus-5)
[https://supportforums.cisco.com/discussion/11510966/nexus-5020-and-glc-t](https://supportforums.cisco.com/discussion/11510966/nexus-5020-and-glc-t)