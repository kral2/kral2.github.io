---
ID: 151
post_title: 'Using markdown to write plain text &#8230; such a briliant idea!'
author: kral2
post_excerpt: ""
layout: post
permalink: http://bit.ly/1a4AAaD
published: true
post_date: 2014-01-02 21:51:24
---
<p><a href="http://www.kral2.fr/vkral/wp-content/uploads/2014/01/markdown_mark.png"><img src="http://www.kral2.fr/vkral/wp-content/uploads/2014/01/markdown_mark.png" alt="markdown_mark.png" width="208" height="128" class="alignleft size-full wp-image-150" /></a></p>

<p>I am currently exploring some new tools to optimize my workflow, and this lead me among other things, to <a href="http://en.wikipedia.org/wiki/Markdown">markdown</a>. I am really not on the bleeding edge, this language is out there since 2004. You can find plenty of articles explaining why markdown is awesome, and how it has revolutionized the workflow of everyone using it.</p>

<p>Shortly, Markdown is a plain text formatting syntax that help you to:</p>

<ul>
<li>write in plain text with your favorite text editor,</li>
<li>keep focus on writing, and being less tempted to procrastinating,</li>
<li>It's really easy, and simpler than HTML ou WIKI syntax.</li>
</ul>

<p>You are not bound on any file format (docx, odt, etc), it's just plain text. It will hardly break or become corrupted ... if you stick with plain a text editor (see below on this part).</p>

<p>Then, when the file is processed by a "markdown aware" viewer, the magic works a you get a nicely formatted text.</p>

<h3>My first enhanced Markdown Editor : The Journalist, Crash &amp; Restore ...</h3>

<p>So I started taking notes with textwrangler, and soon I felt the need for :</p>

<ul>
<li>A preview of the text rendered, as I type,</li>
<li>an easy way to manage all theses files.</li>
</ul>

<p>As I was just starting to explore Markdown, I looked for a free app to start with. A quick search on the Mac App Store led me to the application "The Journalist".</p>

<p>The application looks pretty and my two former requirements seemed to be fulfilled. But after <strong>a day of notes</strong>, The Journalist crashed badly and my content was gone! It seems like something got finally corrupted...</p>

<p>Useless to explain how I was scared ... I started digging to find where the application actually stored his data, without success... An email to the application developer, and a couple of hours later I got a kind answer! The folder to look for was the following ( and I would never guess it alone ...) :</p>

<pre><code>~/Library/Containers/com.mimietpaul.journalistmac
</code></pre>

<h4>Rescue the data!</h4>

<p>Basically, The Journalist store data in a sqlite database. I figured it trying to open files with a basic text editor. So all I need, is to open this database file and query it!</p>

<p>Here are the steps to follow in order to recover your <strong><em>"precious"</em></strong> content:</p>

<ol>
<li><p>copy the sqlite file:</p>

<pre><code>~/Library/Containers/com.mimietpaul.journalistmac/Data/Library/Application Support/TheJournalist
</code></pre></li>
<li>rename thejournalist.data to .sqlite extension,</li>
<li>Open the database using a sqlite editor (1)</li>
<li><p>query the table "<em>ZJNOTE</em>" to list all the notes</p>

<pre><code>SELECT * FROM ZJONOTE
</code></pre></li>
<li>query on "<em>ZBODY</em>" field obtain to actual content : your notes!</li>
<li>copy/paste in your favorite text editor,</li>
<li>you are free!</li>
</ol>

<p>(1) The Firefox extension <a href="https://addons.mozilla.org/fr/firefox/addon/sqlite-manager/">SQLite Manager</a> is a perfect fit for this simple need.</p>

<h3>Conclusion</h3>

<p>This misadventure has allowed me to learn a little more about the specifications for my note-taking tool:</p>

<ul>
<li>The potential markdown application <strong>must</strong> generate raw text files!</li>
<li>If the application is enhancing the writing experience (adding metadata, pictures, etc ...) : metadata must be kept separately from files.</li>
</ul>

<p>Be sure that I will check twice before storing my data on an application ...</p>

<h3>Ressources</h3>

<h4>Links</h4>

<p>Here is some useful references and stories about Markdown:</p>

<ul>
<li><a href="http://daringfireball.net/projects/markdown/syntax">Markdown Syntax</a>, by John Gruber on Daring Fireball, creator of Markdown,</li>
<li><a href="http://fletcherpenney.net/multimarkdown/">Multimarkdown</a>, an interesting extension to markdown by Fletcher Penney,</li>
<li><a href="http://hiltmon.com/blog/2012/02/20/the-markdown-mindset/">The markdown mindset</a>,</li>
<li>A switcher's <a href="http://shayashi.net/tech/why-i-turned-to-markdown/">story to markdown</a>.</li>
</ul>

<h4>Apps and Tools</h4>

<p>Some useful tools for Markdown on OSX</p>

<ul>
<li><a href="https://itunes.apple.com/fr/app/textwrangler/id404010395?mt=12">Textwrangler</a>, the free edition of the great BBEdit. It recognize markdown syntax (*.md extension),</li>
<li><a href="https://github.com/toland/qlmarkdown/downloads">QLMarkdown</a> is a OSX plugin for Quicklook. It enable the rendering of markdown files (*.md extension) right in the finder and <a href="http://tidbits.com/article/13499">A how-to guide</a> to assist you installing it.</li>
</ul>

<p>I am currently evaluating <a href="https://itunes.apple.com/fr/app/ulysses-iii/id623795237?mt=12">Ulysse III</a> as my main editor and text file repository. Later on this as I continue digging about it.</p>