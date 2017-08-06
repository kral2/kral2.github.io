---
ID: 653
post_title: 'Working with Comware OS (HPE FlexFabric Switching) &#8211; Basics'
author: kral2
post_excerpt: ""
layout: post
permalink: >
  http://www.kral2.fr/working-with-comware-os-hpe-flexfabric-switching-basics/
published: true
post_date: 2017-08-06 15:37:45
---
## TL;DR
I recently got the opportunity to get my hands on a HPE 5700. This switch come from H3C assets and run the Comware OS.

It is a bit disturbing to work with it when you are used to Cisco's cli. This post is about basics on the first setup and configuring an HPE Switch running Comware OS 7.

*Unfortunately, having only one switch, I am not able to test advanced features like IRF[^1].*

## HPE 5700 48G physical configuration
The 5700 48G (model JG894A) is a datacenter switch composed of the following :
•	On the front panel :
◦	48 x fixed 1GbE base-T ports,
◦	4 x 10GbE SFP+ ports,
◦	2 x 40GbE QSFP+ ports.
•	On the back :
◦	1 x 1GbE base-T port, for OOBM (Out of Band Management) purpose,
◦	1 x console port.

Here is a link to the [quickspec of the HP5700](https://www.hpe.com/h20195/v2/getpdf.aspx/c04347352.pdf?ver=10 "quickspec of the HP5700") 

## Switching between view/config mode and saving the config
When you first log in, you are in a *display mode*, which means you can only display the current configuration but take no configuration action (The command prompt is a superior sign).
Typing the command « system-view » take you to the configuration mode and the command prompt become a hash.
```
hpswitch&gt;system-view
hpswitch#
```

Saving the running config is done via the *« save »* command. You can save the running config to startup config with the following command :
```
hpswitch# save force
```
The *« force »* parameter will avoid you confirmation & file naming prompts.

## Configuring _**remote**_ out of band management

This part is composed of five sub-tasks :
1. configure the oobm interface IP,
2. define the default gateway,
3. create a local user with admin rights,
4. activate a remote protocol (preferably ssh),
5. configure the vty (Virtual Teletype).

### 1. Configure management interface's IP
After entering *system-view* mode, you select the management interface then set IP address and network mask.
```
interface M-GigabitEthernet0/0/0
 ip address 192.168.1.1 24
```
 
### 2. Configure defaut route on the management interface
```
ip route-static 0.0.0.0 0 M-GigabitEthernet 0/0/0 192.168.1.254 permanent
```
 
### 3. Create a local user, grant "network-admin" rights and SSH connection
```
local-user admin
 password simple &lt;PASSWORD&gt;
 service-type ssh
 authorization-attribute user-role network-admin
```
 
### 4. Configure SSH
First enable SSH service :
```
ssh server enable
```
 
Then generate crypto keys :
```
Public-key local create rss
```
 
### 5. Configure first 15 vty with SSH access
```
line vty 0 15
 authentication-mode scheme
 protocol inbound ssh
```

## Some useful commands
### Configure an uplink in trunk mode and allow all VLANs
```
Interface GigabitEthernet 1/0/48
 port link-type trunk
 port trunk permit vlan all
```
 
### Create an SVI for a vlan
```
interface Vlan-interface &lt;vlan-id&gt;
 ip address 10.0.0.252 24
```
 
### Split 40GbE interfaces into 4 x 10GbE
The 40GbE interfaces can be split into 4 x 10GbE interfaces. You will need a break-out cable and the switche will have to reload.

Select a 40GbE interface in *system-view* mode and enter this command :
```
using tengige
```
 
You can see the 10GbE interfaces after the switche have rebooted. The break-out interfaces numbers are appended after a colon - Think sub-interface.

```
[HPE-5700]display interface brief
…
XGE1/0/53:1          UP   10G(a)  F(a)   A    1
XGE1/0/53:2          UP   10G(a)  F(a)   A    1
XGE1/0/53:3          UP   10G(a)  F(a)   A    1
XGE1/0/53:4          UP   10G(a)  F(a)   A    1
XGE1/0/54:1          UP   10G(a)  F(a)   A    1
XGE1/0/54:2          UP   10G(a)  F(a)   A    1
XGE1/0/54:3          UP   10G(a)  F(a)   A    1
XGE1/0/54:4          UP   10G(a)  F(a)   A    1
```
 
### Configure a native vlan on a trunk port
Native vlan is possible when the port is operating in hybrid mode (you will have to first put the port on access mode, then activate hybrid mode).

```
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port link-type hybrid
Please set the link type of the port to access first.
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port link-type access
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port link-type hybrid
```
 
#### Set native vlan
```
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port hybrid vlan 500 untagged
```
 
#### Add a vlan to a trunk port
```
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port hybrid vlan 502 tagged
```

#### summary commands for a trunk port with native vlan
```
port link-type access
port link-type hybrid
port hybrid vlan 500 untagged
port hybrid vlan 501 tagged
port hybrid vlan 502 tagged
```

[^1]:	IRF : Intelligent Resilient Framework, HPE mLAG technology (equivalent to Cisco's vPC).