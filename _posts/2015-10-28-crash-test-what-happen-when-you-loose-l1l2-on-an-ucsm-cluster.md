---
ID: 415
post_title: 'Crash Test &#8211; What happen when you loose L1&#038;L2 on an UCSM Cluster?'
author: kral2
post_excerpt: ""
layout: post
permalink: http://bit.ly/1WQ8sMX
published: true
post_date: 2015-10-28 00:52:22
---
<h2><a href="http://www.kral2.fr/vkral/wp-content/uploads/2015/10/Nexus-L1L2.png"><img class="alignleft size-medium wp-image-437" src="http://www.kral2.fr/vkral/wp-content/uploads/2015/10/Nexus-L1L2-300x93.png" alt="Nexus-L1L2" width="300" height="93" /></a>L1 &amp; L2, What's it?</h2>
<p style="text-align: justify;">The L1 &amp; L2 links are dedicated physical ports (GbE) on UCS Fabric Interconnect platform, responsible of carrying the heartbeat traffic for the UCSM cluster. To be clear, both links are crucial to the survival of the cluster!</p>
For the record, the Fabric Interconnects (or FI), are based on a Nexus hardware platform, tweaked for the job :
<ul style="text-align: justify;">
	<li>FI Gen 1 (6100XP) are based on Nexus 5000 physical platforms,</li>
	<li>FI Gen 2 (6200UP) are based on Nexus 5500 physical platforms.</li>
</ul>
<p style="text-align: justify;">If you are wondering "What are theses L1 &amp; L2 things for, on my brand new and shiny Nexus?", here is the answer : nothing (on a Nexus). They are here from day one on Nexus, because it was planned from the start to decline the same hardware base and reuse it for Fabric Interconnects.</p>
<p style="text-align: justify;">It's just about rationalization of the production line : it's cheaper to let the 4 GbE ports on all cases than handling several products.</p>
<p style="text-align: justify;">Yes, the company that produced the Nexus line of product (<a href="http://newsroom.cisco.com/feature-content?type=webcontent&amp;articleId=4294861">Nuova Systems, acquired by Cisco on 2008</a>) had UCS on mind from the beginning. If you look closer at the résumé of the co-founders of Nuova, all pieces comes together. They are the three musketeers as I tend to call them (even if "band" is mainly from Italy) : Mario Mazzola, Prem Jaim &amp; Luca Cafiero. It's not their first shot at creating a startup. But I will keep this story for another day <em>- for another blog post, why not</em>.</p>
<p style="text-align: justify;">Talking back about L1 &amp; L2 ports, if we keep on the analogy to the Nexus platform, we can say that L1 &amp; L2 could play the same role of VPC Peer-Links on a VPC,  minus some details. Explained that way, I am sure it is more clear for everybody :-)</p>
<p style="text-align: justify;">Note : L1 &amp; L2 ports connection are done this way :</p>

<ul>
	<li style="text-align: justify;">FI-A L1 &lt;-&gt; FI-B L1,</li>
	<li style="text-align: justify;">FI-A L2 &lt;-&gt; FI-B L2.</li>
</ul>
Key point, no switch between L1 &amp; L2!!! They are channel bonded, and expect another Fabric Interconnect at the other edge. Don't try to workaround, and connect both Fabric Interconnects through a switch : this is explicitly not supported.
<h2>The heartbeat traffic</h2>
<p style="text-align: justify;">UCSM is the management software of the UCS platform. It's embedded on each Fabric Interconnect and run in an Active/Stand-by way. The motivation and the revelence behind this choice, rather than opting for an installed of VM hosted application could feed an entire tribe of Trolls.</p>
<p style="text-align: justify;"><em>- We do love Trolls around here, but they will be fed another time on another blog post (again!) -</em></p>
<p style="text-align: justify;">Like any clustered application, signaling link is required to keep up a good knowledge of each member's health : it's usually the role of a redundant heartbeat link, in other words a messaging bus.</p>
<p style="text-align: justify;">So we are dealing here with a two member cluster, and it is not a detail.</p>

<h2>Loosing a member and Split-Brain</h2>
<strong>The cluster use the heartbeat link to monitor the health status of each member, and failover the services on the healthy member when disaster occur.</strong> A member is deemed healthy, if a quorum can be reached among cluster members. When the cluster consist of only two members, it's the word of one against the other ... So we need a referee (called "Tie-Breaker", "Failover Monitor" or "Witness" depending on the technology), in order to avoid split-brain. <strong>On a UCSM cluster, it's the default gateway that play this referee job when one member can't join the other.</strong>

Note : UCSM cluster do some other testing between FI and chassis. The FI check if the chassis's SEEPROM is reachable. The following article on UCSGuru dive a bit more about the tests and the particular case of C-Series servers, which don't have SEEPROM : <a href="http://ucsguru.com/2012/11/07/ha-with-ucsm-integrated-rack-mounts/">HA with UCSM Integrated Rack Mounts</a>.
<h2></h2>
<h2>What's happening when L1&amp;L2 link is down on a UCSM Cluster.</h2>
<p style="text-align: justify;">Ok, that was a pretty long introduction, to finally come the first question. What's the observed behaviour from an operator/administrator point of view?</p>
<p style="text-align: justify;">When one of the links become down, a "major" alert is raised on UCSM for each FI : <em>"HA Cluster Interconnect Link Failure".</em></p>

<ul style="text-align: justify;" type="disc">
	<li>Error "immediately" visible,</li>
	<li>No impact on data path.</li>
</ul>
<p style="text-align: justify;">When both links are down (L1 &amp; L2), after a timeout period (about minutes) :</p>

<ul>
	<li style="text-align: justify;">A new error is raised, this one is "Critical" : "HA Cluster Interconnect Total link failure",
<ul>
	<li style="text-align: justify;">Subordinate FI is deemed unreachable,</li>
	<li style="text-align: justify;">The B side is shut (assuming that B was the subordinate), <strong><span style="text-decoration: underline;">DATA PATH included</span></strong> :
<ul>
	<li style="text-align: justify;">FEX,</li>
	<li style="text-align: justify;">LAN &amp; SAN uplinks.</li>
</ul>
</li>
</ul>
</li>
</ul>
So keep an eye on your L1 &amp; L2 links, and be sure that at least one of them is always up.
<p style="text-align: justify;">Unfortunately I don't know the exact duration of the timeout. At least, I can say we are talking about minutes.</p>
<p style="text-align: justify;"><em>To keep comparing with Nexus and VPC, we can see a similar behaviour when VPC Peer-Link is down : the secondary is sacrificed on the split-brain altar.</em></p>

<h2>Back to a nominal situation</h2>
<p style="text-align: justify;">As soon as at least one link is back alive :</p>

<ul type="disc">
	<li style="text-align: justify;">Resync of subordinate FI on the primary,</li>
	<li style="text-align: justify;">Data links reactivation,</li>
	<li style="text-align: justify;">UCSM cluster back alive.</li>
</ul>
<h2>Some best practices about L1&amp;L2</h2>
<ul>
	<li>L1 &amp; L2 links should be direct-connected, no switch between them,</li>
	<li>During a migration or a physical move, it may happen to have only L1 active for a period of time, but keep it as short as possible and bring him back his L2 buddy as soon as possible,</li>
	<li>It is about Ethernet : keep in mind distance limitations, don't stretch more than 100 meters!</li>
</ul>