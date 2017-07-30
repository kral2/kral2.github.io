---
ID: 429
post_title: 'Crash Test &#8211; Que se passe-t-il lorsque vous perdez L1&#038;L2 sur un cluster UCSM?'
author: kral2
post_excerpt: ""
layout: post
permalink: http://bit.ly/1jPOB4q
published: true
post_date: 2015-10-28 00:52:22
---
<h2><a href="http://www.kral2.fr/vkral/wp-content/uploads/2015/10/Nexus-L1L2.png"><img class="alignleft size-medium wp-image-437" src="http://www.kral2.fr/vkral/wp-content/uploads/2015/10/Nexus-L1L2-300x93.png" alt="Nexus-L1L2" width="300" height="93" /></a>L1 &amp; L2, c'est quoi?</h2>
<p style="text-align: justify;">Les liens L1 et L2 sont des ports physiques dédiés (GbE) sur les platefome UCS Fabric Interconnect, véhiculant le traffic de heartbeat de l'application en cluster UCSM. Autant dire qu'ils sont cruciaux à la survie du cluster!</p>
<p style="text-align: justify;">L'image du post est bien un Nexus, et ce n'est pas une erreur :-). Pour la petite histoire, les Fabric Interconnect (ou FI), sont basés sur une plateforme matérielle Nexus, adaptée pour la situation :</p>

<ul style="text-align: justify;">
	<li>Les FI Gen 1 (6100XP) sont basés sur les plateformes matérielles Nexus 5000,</li>
	<li>Les FI Gen 2 (6200UP) sont basés sur les plateformes matérielles Nexus 5500.</li>
</ul>
<p style="text-align: justify;">Si vous vous demandiez <em>"à quoi servent les ports L1 &amp; L2 sur mon joli Nexus tout neuf?"</em>, vous avez votre réponse : ils ne servent à rien (sur un Nexus). Ils sont présents depuis le début sur les Nexus, car la vision moyen terme prévoyait déjà une déclinaison du boîtier pour servir de base matérielle aux futur Fabric Interconnects.</p>
<p style="text-align: justify;">Il faut y voir une simple rationalisation des chaînes de production : il est plus rentable de laisser ces 4 ports GbE sur l'ensemble des boites plutôt que de gérer l'exception.</p>
<p style="text-align: justify;">Et oui, la société à la genèse de la gamme Nexus (<a href="http://newsroom.cisco.com/feature-content?type=webcontent&amp;articleId=4294861">Nuova Systems, qui a été racheté par Cisco en 2008</a>) pensait <del>aigle</del> UCS dès le départ. Si l'on se penche un peu sur le parcours des fondateurs de Nuova, le puzzle devient tout de suite plus clair. Ce sont trois anciens cerveaux de chez Cisco, Mario Mazzola, Prem Jaim &amp; Luca Cafiero, que j'aime appeler les trois mousquetaires :-) (même si le trio est à majorité italienne), et ils n'en sont pas à leur coup d'essai. Mais ceci est une autre histoire - <em>à destination d'un autre billet pourquoi pas.</em></p>
<p style="text-align: justify;">Pour revenir aux ports L1 &amp; L2, tout en restant dans l'analogie avec les plateformes Nexus, les liens L1 &amp; L2 pourrait être assimilés aux liens VPC Peer-Links, à quelques détails près. Dit comme ça, je suis certains que ça parle à tout le monde :-)</p>
<p style="text-align: justify;">Note : les connexions des ports L1 &amp; L2 sur chaque FI se fait de la manière suivante :</p>

<ul>
	<li style="text-align: justify;">FI-A L1 &lt;--&gt; FI-B L1,</li>
	<li style="text-align: justify;">FI-A L2 &lt;--&gt; FI-B L2.</li>
</ul>
<p style="text-align: justify;">Autre détail (de taille), pas de switch sur le chemin des connexions L1 &amp; L2!!! Ces ports sont configurés en bonding et attendent à l'autre bout de la connexion : un autre Fabric Interconnect. Ne pensez pas à configurer du port-channel côté switch, placer les bons vlans, etc : c'est une configuration explicitement hors support.</p>

<h2>Le traffic de heartbeat</h2>
<p style="text-align: justify;">UCSM est le logiciel de gestion de la plateforme UCS. Il est embarqué au sein de chaque Fabric Interconnect et fonctionne en mode Actif/Passif. La motivation et la pertinence de ce choix, plutôt que d'opter pour une application à installer ou exécuter au sein d'une VM, pourrait alimenter tout une tribu de troll.</p>
<p style="text-align: justify;"><em>- Ici on aime les trolls mais ils seront nourris une autre fois dans un autre billet (encore!) -</em></p>
<p style="text-align: justify;">Comme toute application en cluster, un lien de signalisation est nécessaire afin de pouvoir maintenir une bonne connaissance de l'état de santé de chaque membre : cela se fait généralement via un lien de heartbeat redondé, autrement dit un bus de communication.</p>
<p style="text-align: justify;">Nous sommes donc ici en présence d'un cluster à deux membres, et ce n'est pas un détail.</p>

<h2>Perte d'un membre et Split-Brain</h2>
<p style="text-align: justify;"><strong>Le cluster surveille l'état de santé de chaque membre via le lien de heartbeat, et bascule les services sur un membre réputé sein en cas de panne</strong>. Sans entrer dans les détails et transformer cet article en un essai sur les clusters (c'est bien prétentieux :-)), un membre est réputé sein si le quorum (<a href="https://fr.wikipedia.org/wiki/Quorum">voir la définition stricte</a>, et ne pas faire d'amalgame avec du jargon Microsoft) est atteint au sein des membres. Pour un cluster à deux membres, c'est la parole d'un bandit contre un autre ... Il faut donc faire recours à un arbitre (appelé suivant les technologies : "Tie-Breaker", "Failover Monitor", etc ...), afin d'éviter le phénomène de <a href="https://fr.wikipedia.org/wiki/Split-brain">Split-brain</a>.  <strong>Dans un cluster UCSM, c'est la passerelle par défaut qui fait office d'arbitre lorsqu'un membre n'arrive pas à joindre l'autre</strong>.</p>
<p style="text-align: justify;">Note : le cluster UCSM effectue un test supplémentaire, visant à vérifier le lien entre le FI et le/les châssis. Il s'appuie pour cela sur la SEEPROM (Serial EEPROM) : espace de stockage en mémoire non-volatile, accessible par chaque FI. L'article suivant chez UCSGuru explique un peu plus en détail les différents tests menés, ainsi que le cas particulier de serveurs C-Series (format rack), qui ne disposent pas de SEEPROM : <a href="http://ucsguru.com/2012/11/07/ha-with-ucsm-integrated-rack-mounts/">HA with UCSM Integrated Rack Mounts</a>.</p>

<h2>Que se passe-t-il lorsque l'on perd les liens L1&amp;L2 sur un cluster UCSM?</h2>
<p style="text-align: justify;">Toute cette introduction pour arriver à la question initiale. Quel est l'effet concret constaté, d'un point de vue opérateur/administrateur?</p>
<p style="text-align: justify;">Lorsque un des deux liens n'est plus disponible, une alerte de type "majeur" est remontée dans UCSM pour chaque FI : <em>"HA Cluster interconnect Link Failure"</em></p>

<ul style="text-align: justify;" type="disc">
	<li>Erreur visible "immédiatement",</li>
	<li>Pas d'impact sur le data path.</li>
</ul>
<p style="text-align: justify;">Lorsque les deux liens ne sont plus disponibles (L1 &amp; L2), après une période de timeout (En minutes) :</p>

<ul style="text-align: justify;" type="disc">
	<li>Une nouvelle erreur de type "Critique" est levée : "HAClusterInterconnectTotallinkfailure",
<ul type="circle">
	<li>Le FI subordinate est déclaré "injoignable",</li>
	<li>Toutes la partie B est "shut" (en admettant que c'était la partie passive (subordinate) du cluster UCSM) <span style="text-decoration: underline;"><strong>DATA PATH compris</strong></span> :
<ul type="disc">
	<li>FEX,</li>
	<li>Uplinks LAN &amp; SAN.</li>
</ul>
</li>
</ul>
</li>
</ul>
<p style="text-align: justify;">Attention donc à vos liens L1 &amp; L2, il faut les choyer et assurer la présence d'au-moins un lien sur deux, sinon effets surprise-party garanti!</p>
<p style="text-align: justify;">Je n'ai malheureusement pas connaissance de la durée exacte du timeout avant coupure complète de la partie "passive". L'ordre de grandeur est en minutes, j'essayerai de reproduire le test à l'occasion, équipé d'un super chronographe mécanique :-D</p>
<p style="text-align: justify;"><em>Toujours pour rester dans l'analogie avec les Nexus et le mécanisme VPC, c'est un comportement similaire qui est observé lorsque l'on perd le sacro-saint VPC Peer-Link : le secondary est sacrifié sur l'autel du split-brain.</em></p>

<h2>Retour à la normale</h2>
<p style="text-align: justify;">Lorsqu'au moins un des liens est de nouveau présent :</p>

<ul type="disc">
	<li style="text-align: justify;">Resync du FI subordinate sur le primary,</li>
	<li style="text-align: justify;">Réactivation des liens,</li>
	<li style="text-align: justify;">Rétablissement du cluster UCSM.</li>
</ul>
<h2>Notes et recommandations sur L1&amp;L2</h2>
<ul>
	<li>Les connexions L1-L1 et L2-L2 doivent être en DIRECT, pas de switchs sur le chemin,</li>
	<li>En cas de migration/déménagement, on peut être amené à fonctionner avec seulement L1, mais essayez de garder cette période aussi courte que possible et rendez lui au plus vite son copain L2,</li>
	<li>Média Ethernet : les limitations usuelles s'appliquent, ne dépassez pas les 100m!</li>
</ul>