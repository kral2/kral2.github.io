---
ID: 281
post_title: 'Integrating scoop.it with wordpress &#8230; as much as I can, with a free account&#8230;'
author: kral2
post_excerpt: ""
layout: post
permalink: http://bit.ly/1DdPQ1e
published: true
post_date: 2014-10-16 08:17:41
---
<b>Update 2017/04/02 : Scoop.it apparently changed something in their publishing scheme. I don't have the time to debug/update/nor I use this service anymore. I will simply deactivate the page on this blog from now on.</b>

I am using scoop.it as a content curation platform, mainly when I don't have the time or think that the news is not enough to publish a full blog post.

I don't want to blend my "curated content" with my "written content" in an indistinct way, so the obvious choice was to integrate the entries on a separate WordPress page. To do that on a tightly integrated way, you need to get a paid account (worth several hundred bucks), or if you don't mind tinkering a bit you can :
<ul>
	<li>use the RSS feed, my choice,</li>
	<li>use the API, but this one is out of my reach. I am busy enough to not scatter more than I already do:-)</li>
</ul>
So to "integrate" your scoop.it entries within a WordPress page, you need :
<ul>
	<li>your RSS feed address (you can get it on the apps section, choosing  the "Scoop.it Topic Widget",</li>
	<li>a WordPress plugin able to warp a Feed in PHP or shortcode.</li>
</ul>
I use <a title="RSSImport" href="https://wordpress.org/plugins/rss-import/">RSSImport</a>, and the Shortcode parameters below give an output like what you can see <a title="Quick Inights" href="http://www.kral2.fr/quick-insights/">here</a>.
<ul>
	<li>html=true</li>
	<li>before_date="&lt;p&gt;&lt;small&gt;Scooped on "</li>
	<li>date=true</li>
	<li>before_creator=" by "</li>
	<li>creator=true</li>
	<li>after_creator="&lt;/small&gt; "</li>
	<li>display=10</li>
	<li>feedurl="http://www.scoop.it/your-rss-feed/rss.xml"</li>
	<li>displaydescriptions="true"</li>
	<li>use_simplepie="true"</li>
	<li>truncatedescchar=350</li>
	<li>truncatedescstring=" ..."</li>
	<li>after_desc=" &lt;a href='%href%' target='_blank'&gt;show insight&lt;/a&gt; &lt;p&gt;"]</li>
</ul>
The plugin's page is full of description and samples, about how to use it.

It's not perfect, nor the first way I imagined it, but it's there ... Ideally, I would like to extract my insight from the feed entry and print it right below each bullet.

Any sugestion or help from a more talented <del>coder</del> tinkerer than me (no so hard ...) is welcome!