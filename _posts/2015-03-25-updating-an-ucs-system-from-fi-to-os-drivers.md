---
ID: 355
post_title: 'Updating an UCS system &#8211; from FI to OS drivers'
author: kral2
post_excerpt: ""
layout: post
permalink: >
  http://www.kral2.fr/updating-an-ucs-system-from-fi-to-os-drivers/
published: true
post_date: 2015-03-25 16:23:48
---
<em><span style="text-decoration: underline;"><strong>Update October 27th 2015
</strong></span>At the time&nbsp;writing this article, I mentioned&nbsp;my experience on upgrading from version 2.2(3a), and the unexpected reboots of "some" blades. It turns&nbsp;this&nbsp;bug have been identified and&nbsp;is&nbsp;fixed&nbsp;since&nbsp;2.2(3g). I forgot to update this article as promised : thanks to Patrick for asking after in the comments. :-)</em>

<em>First to narrow the scope, the problem affected only UCS B200 M4. It was not obvious for me, as it was a greenfield deployment with only B200 M4 blades. It's logged under <a href="https://quickview.cloudapps.cisco.com/quickview/bug/CSCut61527">bug number&nbsp;<em>CSCut61</em>527</a>.</em>

<em>What is it about? The B200 M4's <a href="https://en.wikipedia.org/wiki/Intelligent_Platform_Management_Interface#IPMI_components">BMC</a> (Baseboard Management Card, a part of&nbsp;IPMI/CIMC system) sometimes return an invalid <a href="https://en.wikipedia.org/wiki/Field-replaceable_unit">FRU</a>&nbsp;and make the blade reboot ... Yeah you read it right : a management controller taking potentially down production workloads ...</em>

<em>This caveat is around since 2.2(3a) and 2.2(1b), and was fixed first on release 2.2(3g). Here is the link to t</em><em>he proper&nbsp;section of <a href="http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/release/notes/ucs_2_2_rn.html#11707">Release Notes for UCSM 2.2</a>. When you are there, just look for CSCut61527 at the 10th row&nbsp;in the table.</em>

<a href="http://www.kral2.fr/vkral/wp-content/uploads/2015/03/upgrade-ucsm-bug-cscut61527-amp-b200m4-mdash-envoy-eacute-s.png"><img class="alignnone wp-image-422 size-full" src="http://www.kral2.fr/vkral/wp-content/uploads/2015/03/upgrade-ucsm-bug-cscut61527-amp-b200m4-mdash-envoy-eacute-s.png" alt="upgrade-ucsm--bug-cscut61527--amp--b200m4--mdash--envoy-eacute-s" width="881" height="261"></a>

<strong><em>Lesson learned : always double-check your current UCSM version before adding B200 M4 blades if it's not a greenfield deployment!
</em></strong><em><strong>---</strong></em>

There is plenty of writing&nbsp;about "how to upgrade UCS" (from official Cisco documentation to independent blog posts) but I found none&nbsp;going from UCSM up to&nbsp;ESXi drivers (disclaimer : I looked after&nbsp;it less than 5min :-).

So here is my 2 cents on the matter.
<h2>What do I&nbsp;need to update my UCS system?</h2>
The detailed list of objects you need to upgrade are the following, from top-to-bottom :
<ol>
	<li>UCSM itself, which is a cluster management software running in Active/Passive mode on Fabric Interconnects,</li>
	<li>The Fabric Interconnects,</li>
	<li>The IO Modules, aka FEXs,</li>
	<li>Servers (either Blade or Rack format),&nbsp;whichcan be separated in three major section&nbsp;:
<ol>
	<li>BIOS,</li>
	<li>Controllers (SAS, CIMC),</li>
	<li>Adapters cards,</li>
</ol>
</li>
	<li>Drivers, specific to your Operating System.</li>
</ol>
This document is old and some information may be outdated, but still describe quite well the "What" : <a href="http://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110512-ucs-firmware-versioning.html" target="_blank">Cisco UCS Firmware Versioning</a>.
<h2>Where should I look for the software pieces?</h2>
It is rare enough for Cisco products to be mentioned : <span style="text-decoration: underline;">you don't need to have a system linked to your CCOID to be able to download UCS related software</span> :-)

Fortunately, all pieces of software listed on the previous section are grouped by bundles and you don't have (anymore) to download each packages separately :
<ul>
	<li>Infrastructure Bundle : it contains UCSM, FI and FEX softwares/firmwares,</li>
	<li>B-Series or C-Series Bundle : it contains BIOS, Controllers and Adapter cards firmwares,</li>
	<li>An ISO with all C-Series or B-Series drivers.</li>
</ul>
Note : Instead of downloading 2GB of drivers, if you are looking for drivers on a particular Operating System, it may be better to look for Cisco UCS drivers on the OS editor's site. For example if you look for the lastest Cisco UCS enic and fnic drivers for VMware, you can find them on vmware.com. It's a 2MB download versus 2GB ...
<h2>Updating the UCS system</h2>
In this section, I will not go for a screen-by-screen explanation but will rather explain the key steps and possible warnings you need to be aware of before starting the upgrade.

First, the documentation you should definitely check :
<ul>
	<li><a href="http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/ucs-manager/GUI-User-Guides/Firmware-Mgmt/3-1/b_UCSM_GUI_Firmware_Management_Guide_3_1.html" target="_blank">Cisco UCS Manager Firmware Management Guide</a>&nbsp;<span style="color: #ff0000;">(link updated to UCSM 3.1)</span>,</li>
	<li><a href="http://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110475-ucs-firmware-management-00.html#maintask1" target="_blank">Common Tips in UCS Firmware Management</a>,</li>
</ul>
At the time of writing this article, with the current version being 2.2(3e), the recommend upgrade path is Top-to-Bottom, it's&nbsp;generally the way to go. Yet&nbsp;on some earlier versions (1.4 if I am correct), required&nbsp;Bottom-to-Top.

It's really&nbsp;unlikely that would change back again, but&nbsp;you should definitely check the documentation and the last release note update to know what's the current and supported method. Here is&nbsp;<a href="http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/sw/upgrading/from2-1/to2-2/b_UpgradingCiscoUCSFrom2-1To2-2.html#topic_6505D0253916438AB0D832BA8840A9FA" target="_blank">Upgrading Cisco UCS from Release 2.1 to Release 2.2</a>&nbsp;document.

This doodle illustrate the updated parts and the actual order to follow.

<img class="aligncenter wp-image-366 size-large" src="http://www.kral2.fr/vkral/wp-content/uploads/2015/03/Update-UCS-1024x671.png" alt="Update UCS" width="604" height="396">

Step 0 is about preparation.&nbsp;You need to upload the firmware packages to the Fabric Interconnect boot flash (the packages are copied to both fabric interconnects).
<ol>
	<li>Upgrade the UCSM Software. <span style="text-decoration: underline;"><strong>It's supposed</strong><strong>&nbsp;to be</strong></span> non-disruptive for data path and you should only relaunch the UCSM client.&nbsp;<em>My recent experience when upgrading from 2.2(3a) to 2.2(3d) was catastrophic : some blades rebooted randomly 4-5 times ...&nbsp;Not so "non-disruptive". I managed to reproduce the same behavior on another system and a SR is currently open. I may&nbsp;update this post later depending on the SR's issue.</em></li>
	<li>Stage the Firmware (~10-20min) on all FEXs ("Update Firmware" on Equipment&gt;Firmware Management&gt;Installed Firmware") and <strong><span style="text-decoration: underline;">set it to be active on next reboot</span></strong> ("Activate Firmware" without forgetting the related "active on next reboot" checkbox). This will save you a reboot, as the FEX will reboot anyway when the Fabric Interconnect will be upgraded,</li>
	<li>Upgrade the Fabric Interconnect which is holding the secondary role, wait for reboot (~15min) then change the cluster lead to get primary on the newly updated FI,</li>
	<li>Upgrade the remaining Fabric Interconnect and wait for reboot (~15min), then take back the cluster lead to the initial state (there is no automatic fail-back for UCSM),</li>
	<li>Update the blades : best way is through Maintenance and Firmware Policies,
<ol>
	<li>Be sure that your service profile is set to "User Ack" for maintenance Policy,</li>
	<li>For&nbsp;ESXi node, take it in maintenance mode first from your vSphere Client,</li>
	<li>Ack the reboot request on UCSM as your ESX nodes are in Maintenance mode.</li>
</ol>
</li>
</ol>
<em>Note : you can edit the default "Host Firmware Package" policy to use the right package version (for blade and rack), even without any service profile created. This way, any UCS server connected to the fabric will be automatically updated to the desired baseline. This will effectively prevent running different firmware due to different shipping/buying batches.</em>

<strong>Most upgrade guides stop here</strong>, right after updating the hardware. <strong>Let me say that it's golden path to #fail :-)</strong>. The next part is about updating your ESXi driver to the most current driver version, supported by your UCS firmware release.
<h2>&nbsp;Updating VMware ESXi drivers</h2>
At the end of the day, what matter is how your Operating System handle your hardware. That&nbsp;is the driver's job. If it's obsolete, either it works non-optimized and without "new features/enhancements" (that's the best option) or it may lead to&nbsp;some unpredictable behaviors ...

Bets are high you installed your ESXi on a UCS server using a Custom ISO, available at vmware.com.&nbsp;Bets are higher that as the VIC card's exposed vETH and vHBA are recognized, nobody has bothered to update them. If so, you run with a 2-3 years old driver ...

You can check your current enic (vETH) and fnic (vHBA) driver version on your ESXi host with the following commands :

#vmkload_mod -s enic
#vmkload_mod -s fnic

If you find&nbsp;Enic version 2.12.42 and&nbsp;Fnic version 1.6.0.5, you run the ISO's version and I would highly recommend to upgrade.

Download your drivers at vmware.com following this navigation path : vmware.com &gt; download &gt; vsphere &gt; Driver &amp; Tools.
<ol>
	<li>Select the relevant vSphere version (5.X, do not choice "update 01, 02" links),</li>
	<li>Download Drivers for Cisco enic and fnic.</li>
</ol>
<a href="http://www.kral2.fr/vkral/wp-content/uploads/2015/04/35a19d2f-76d2-4aa2-866b-154aa497514a1.png"><img class="aligncenter wp-image-406 size-full" src="http://www.kral2.fr/vkral/wp-content/uploads/2015/04/35a19d2f-76d2-4aa2-866b-154aa497514a1.png" alt="35a19d2f-76d2-4aa2-866b-154aa497514a.png" width="804" height="384"></a>

It's 1MB per download on vmware.com, compared to the UCS Drivers ISO on Cisco.com which contains all drivers for all systems, but worth 2GB ...

To apply the update, you have choice between&nbsp;esxcli on the esx shell, or via Update Manager.

No rocket science here, just follow this standard VMware KB and pick an option you are comfortable with : <a title="http://kb.vmware.com/kb/2005205 " href="http://kb.vmware.com/kb/2005205%20">http://kb.vmware.com/kb/2005205</a> or <a title="http://kb.vmware.com/kb/1032936" href="http://kb.vmware.com/kb/1032936">KB1032936</a> for vSphere 4.x.

<em>Reminder : you do NOT need a Windows based vCenter when using Update Manager. You just have to plan a Windows system to install VMware Update Manager utility, then you can enjoy using vCenter Appliance.
</em>

In addition, this Troubleshooting Technote go into all the details regarding how to check and update UCS Drivers for ESXi, Windows Server and Linux (Red Hat &amp; Suse).

<a href="http://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/116349-technote-product-00.html">http://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/116349-technote-product-00.html</a>

&nbsp;