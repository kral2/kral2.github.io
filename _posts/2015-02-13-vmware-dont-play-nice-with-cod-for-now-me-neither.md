---
ID: 336
post_title: 'VMware don&#8217;t play nice with CoD. For now. Me neither &#8230;'
author: kral2
post_excerpt: ""
layout: post
permalink: http://bit.ly/1FHR4Tz
published: true
post_date: 2015-02-13 02:21:33
---
No, I am not going to talk about Call of Duty. At least not today, not on this blog post, even maybe not during this life ... :-)

So what's CoD and what the hell this have to do with VMware? CoD stands for <strong>Cluster-on-Die</strong>, a new NUMA setting found on some shiny Intel Haswell processors, aka E5-v3.

It's active by default.

<span style="text-decoration: underline;"><em>Side note on this Feature</em></span>
<em>With Haswell, Intel have gone wild and each processor can hold up to 18 cores! That's a lot, but nothing new here. The bottom line is that with so many cores on a single socket, it may lead to more latency toward memory access with some workloads.
So there is a bunch of new features, to <del>get you confused</del> have choice and find the best suiting option for your context.</em>

Here is a link to a <a href="http://www.cisco.com/c/en/us/products/collateral/servers-unified-computing/ucs-c-series-rack-servers/whitepaper_C11-733130.html" target="_blank">Cisco white paper</a>, talking about the UCS B200 M4 and BIOS tuning for performance. You could find there plenty of details on each new BIOS settings. The following is an abstract for the "Cluster on Die Snoop" functionality
<p class="pSubhead2CMT" style="padding-left: 30px;"><em><span style="text-decoration: underline;">Cluster on Die Snoop</span>
</em><em>Cluster on Die (CoD) snoop is available on Intel Xeon processor E5-2600 v3 CPUs that have 10 or more cores. Note that some software packages do not support this setting: for example, at the time of writing, VMware vSphere does not support this setting. CoD snoop is the best setting to use when NUMA is enabled and the system is running a well-behaved NUMA application. A well-behaved NUMA application is one that generally accesses only memory attached to the local CPU. CoD snoop provides the best overall latency and bandwidth performance for memory access to the local CPU. For access to remote CPUs, however, this setting results in higher latency and lower bandwidth. This snoop mode is not advised when NUMA is disabled.</em></p>
<strong>Problem</strong> : VMware doesn't support this new option. It's outlined on the <a title="KB2087032" href="http://kb.vmware.com/kb/2087032" target="_blank">KB2087032</a>, for vSphere 5.1 in the front line, but you can read that this problem persist for vSphere 5.5.
I ran on this problem with a brand new UCS M4, the result (that I am aware of) is pretty fun : vSphere host see 4 sockets instead of 2 sockets ... Licensing guys @VMware will be happy :-)

<strong>Workaround</strong> : This is a well-known issue today for vSphere, and no doubt it will be fixed soon by VMware. In the mean time, the recommended action is to "disable" this setting. Actually, you cannot "disable" it, you just have to choose another CPU snoop mode. The best balanced mode seems to be "Home Snoop" mode.
<p class="pSubhead2CMT"><span style="text-decoration: underline;"><em>Home Snoop</em></span>
<em>Home Snoop (HS) is also available on all Intel Xeon processor E5-2600 v3 CPUs and is excellent for NUMA applications that need to reach a remote CPU on a regular basis. Of the snoop modes, HS provides the best remote CPU bandwidth and latency, but with the penalty of slightly higher local latency, and is the best choice for memory and bandwidth-intensive applications: such as, certain decision support system (DSS) database workloads.</em></p>
The Third available mode for CPU Snooping defined by Intel on E5-2600 v3 is "Early Snoop", which is best suited for low latency environments.

So to configure that parameter on a UCS system, <a href="http://markhazell.info/ucs-and-cluster-on-die-2/" target="_blank">the best option is to define a custom BIOS policy through UCSM</a>, tweaking the <strong>QPI snoop mode</strong> and choose "Home-Snoop".

Changing the BIOS policy for this value take effect immediately, without needing to reboot. vSphere see that change immediately and report the correct socket number.

Just remember <span style="text-decoration: underline;">to be at least in firmware version 2.2(3c)</span> to get that feature available, but my advice would be to run at least UCSM 2.2(3d), <a href="http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/release/notes/ucs_2_2_rn.html#pgfId-226104" target="_blank">that's the current recommended firmware version for B200 M4</a> as writing.

<strong>UPDATE March 25th : "Home Snoop" is the default setting for B200 M4 BIOS, when your system is shipping with UCSM &gt; 2.2.3(c).</strong>