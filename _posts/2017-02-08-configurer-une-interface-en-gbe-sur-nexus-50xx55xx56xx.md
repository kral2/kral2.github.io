---
ID: 507
post_title: >
  Configurer une interface en GbE sur
  Nexus 50xx/55xx/56xx
author: kral2
post_excerpt: >
  Configurer des ports GbE sur des
  switches Nexus 5K
layout: post
permalink: http://bit.ly/2kIjl8P
published: true
post_date: 2017-02-08 18:32:19
---
Les switchs Nexus constituent la gamme Datacenter chez Cisco. Ils sont optimisés pour les connexions 10GbE et supérieur, et présentent un bien meilleur prix au port 10GbE par rapport à la gamme Catalyst. Il est cependant parfois nécessaire d’avoir une connexion Gigabit Ethernet, entre autre pour se connecter à l’architecture existant.

Pour cela les modules GLC-T sont nécessaires.
<em>Note : prolabs manufacture des Gbic compatibles, avec un prix intéressant.</em>

L’erreur typique rencontrée est « SFP Validation Failed », malgré un SFP compatible. Afin d’avoir un port fonctionnel, il faut configurer le port <strong>avant</strong> d’insérer le SFP.
<ol>
	<li>Définir la vitesse du port avant insertion du SFP</li>
</ol>
<pre><code>conf
int e1/1
speed 1000
</code></pre>
<ol>
	<li>Insérer le SFP dans un port compatible 1/10GbE :</li>
</ol>
- Sur les Nexus 5010 : ports 1 à 8,
- Sur les Nexus 5020 : ports 1 à 16,
- Sur les Nexus 5500/5600 : ports 1 à 32.

Note : sur les 5500/5600, un port configuré en <code>speed auto</code> basculera automatiquement la configuration entre 1GbE et 10GbE.
<h1>Références</h1>
<a href="http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5000/sw/configuration/guide/cli/CLIConfigurationGuide/BasicEthernet.html">http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5000/sw/configuration/guide/cli/CLIConfigurationGuide/BasicEthernet.html</a>
<em>A Cisco Nexus 5000 Series switch has a number of fixed 10-Gigabit ports, each equipped with SFP+ interface adapters. The Nexus 5010 switch has 20 fixed ports, the first eight of which are switchable 1-Gigabit/10-Gigabit ports. The Nexus 5020 switch has 40 fixed ports, the first 16 of which are switchable 1-Gigabit/10-Gigabit ports.</em>

<a href="http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5500/sw/interfaces/7x/b_5500_Interfaces_Config_Guide_Release_7x/b_5500_Interfaces_Config_Guide_Release_7x_chapter_01.html">http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus5500/sw/interfaces/7x/b_5500_Interfaces_Config_Guide_Release_7x/b_5500_Interfaces_Config_Guide_Release_7x_chapter_01.html</a>
<em>The 5596T switch has 48 base board ports and 3 GEM slots. The first 32 ports are 10GBase-T ports the last 16 ports are SFP+ ports. The 10GBase-T ports support a speed of 1-Gigabit, 10-Gigabit, or Auto. The Auto setting automatically negotiates with the link parser to select either 1-Gigabit or 10-Gigabit speed.</em>

<a href="http://serverfault.com/questions/339957/how-do-i-get-past-sfp-validation-failed-on-a-sfp-ge-t-transceiver-on-a-nexus-5">http://serverfault.com/questions/339957/how-do-i-get-past-sfp-validation-failed-on-a-sfp-ge-t-transceiver-on-a-nexus-5</a>
<a href="https://supportforums.cisco.com/discussion/11510966/nexus-5020-and-glc-t">https://supportforums.cisco.com/discussion/11510966/nexus-5020-and-glc-t</a>