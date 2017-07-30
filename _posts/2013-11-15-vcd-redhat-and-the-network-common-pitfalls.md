---
ID: 61
post_title: 'vCD, Redhat, and the network : common pitfalls'
author: kral2
post_excerpt: ""
layout: post
permalink: http://bit.ly/1aa1TQl
published: true
post_date: 2013-11-15 00:47:59
---
<p>This post could have been named "the good, the bad and the ugly", you are free to choose which one you map with : vCD, Red Hat and the network ... :D</p>

<p>The purpose here, is to sum up common pitfalls when setting up a vCloud Director environment. As you will see, most of them are not related to vCloud, but rather to linux.</p>

<h2>What do you need for a vCloud cell?</h2>

<p>Before starting, let's do a quick refresh on what you need for a vCloud Cell:</p>

<ul>
<li>a VM with minimum hardware requirement and a supported guest OS (see installation guides),</li>
<li>a minimum of two nics : one for the web portal, one for VM consoles (VMRC proxy).</li>
</ul>

<p>According to the installation guides, for all versions prior to 5.5 you will need:</p>

<ul>
<li>2GB of RAM,</li>
<li>~1GB of disk space to install vCD binaries,</li>
<li>an RHEL 5.x or 6.x depending on the version of vCD (no CentOS support).</li>
</ul>

<p>Starting with vCD 5.5, you will need 4GB of RAM and a little more disk space (1350MB).
Note: version 5.5 officially support CentOS 6.4.</p>

<p>There is no recommandation/requirement explicitly stated by VMware for cpu count. However, any decent vCD design should include a dedicated management cluster, and the cpu ressource will not be a bottleneck on this cluster. So I tend to setup vCD cells with 2 vCPUs.</p>

<p>Here, I am digressing from the original matter of this post. Let the design advices for another post, and focus on configuration errors.</p>

<p>The more common problems are the following:</p>

<ol>
<li>Hang on reboot of the vCD cell,</li>
<li>Being unable to ping the 2nd nic,</li>
<li>Unable to access the vCloud web portal.</li>
</ol>

<h3>1. Hang on reboot of the vCD cell</h3>

<p>It's mainly related to vSphere 5.1 and RHEL/CentOS 6.x. The problem have been widely discussed on VMware community forums, here are some links:</p>

<ul>
<li><a href="http://www.chriscolotti.us/vmware/workaround-for-vsphere-5-1-guest-unable-to-collect-ipv4-routing-table/">An explanation from Chris Colotti's blog</a>,</li>
<li><a href="http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=1189">VMware KB 1189</a>
, pointed on Chris's blogpost,</li>
<li><a href="http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=2048572">VMware KB 2048572</a>, related to the error message you find during boot by pressing F5.</li>
</ul>

<p>With these informations, you should be able to get rid of this nasty reboot hang ...</p>

<h3>2. Unable to ping the 2nd nic</h3>

<p>If you choosed RHEL6 for your vCD cell guest OS and your nics are on the same vlan, you should have some problems to ping the second nic. This is due to RHEL's default setting regarding "reverse path filtering".</p>

<p>Basicly, RHEL drop packets when the route for outbound traffic differs from the route of incoming traffic. It's a new "default behavior" with RHEL 6.
More details could be found on the subject following <a href="https://access.redhat.com/site/solutions/53031">this link to the related Red Hat KB</a>.</p>

<h3>3. Unable to access the vCloud web portal</h3>

<p>Even if you have dodged all these traps, you could still be frustrated when trying to launch the vCloud web portal for first configuration. And it's mainly because of the default security parameters of RHEL: <strong>The iptable firewall is running</strong>.</p>

<p>Based on your security context, you could just disable the firewall or a better approach, configure it ...
By default RHEL is not listening on port 443, so you have no chance to execute the first install wizard.</p>

<p>Fast spoiler : add the following rule and restart your firewall</p>

<pre><code>iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
service iptables save
service iptables restart
</code></pre>

<p>Obviously you will need to open other ports to get a fully functional vCD, but we are here focussing on what you need to get access to the main portal.</p>

<p>Kendrick Coleman wrote an exhaustive article about <a href="http://www.kendrickcoleman.com/index.php/Tech-Blog/vcloud-director-rhel-firewall-settings.html">how to setup the RHEL firewall for the vCD cell</a>. I strongly encourage you to read it if you decide to not completely turn off iptables.</p>

<p>And finally, the yoda quote:<br />
"To be an <em>iptables jedi</em>, only one way there is luke : <a href="http://www.netfilter.org/documentation/index.html">rtfm of netfilter/iptables</a>"</p>

<p>More links to help you configure your vCD cell right, on the first try:</p>

<ul>
<li><a href="http://www.kendrickcoleman.com/index.php/Tech-Blog/how-to-install-vcloud-director-15-from-beginning-to-end.html">How to install vcloud director 1.5 from beginning to end</a>,</li>
<li><a href="http://kendrickcoleman.com/index.php/Tech-Blog/how-to-install-vcloud-director-on-rhel-62-no-gui.html">How to install cloud director on rhel 6.2 with no gui</a>,</li>
<li><a href="http://www.hypervizor.com/2011/07/publishing-the-vcloud-director-portal-on-the-internet/">publising the vcloud director portal on the internet</a>.</li>
</ul>