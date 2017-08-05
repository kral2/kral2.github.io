---
ID: 151
post_title: 'I.Use[Markdown] to write formatted plain text &#8230; and TheJournalist debugging'
author: kral2
post_excerpt: ""
layout: post
permalink: >
  http://www.kral2.fr/using-markdown-to-write-plain-text-such-a-briliant-idea/
published: true
post_date: 2014-01-02 21:51:24
---
<h3><a href="http://www.kral2.fr/vkral/wp-content/uploads/2014/01/markdown_mark.png"><img class="alignleft size-full wp-image-150" src="http://www.kral2.fr/vkral/wp-content/uploads/2014/01/markdown_mark.png" alt="markdown_mark.png" width="208" height="128" /></a>TL;DR</h3>

Using markdown to write plain text ... such a brilliant idea! The main advantage of markdown is powerful text formatting while being free of application container lock-in : portability!

So when you choose an editor for writing in markdown, be carefull to not lock yourself with this shiny new text editor … That was exactly what happened to me with "The Journalist" app. This blog post is both a markdown introduction (why switching?) and a step-by-step guide to rescue your files after an application crash.

<hr />

<h3>Context : what is Markdown?</h3>

I am currently exploring some new tools to optimize my workflow, and this led me among other things, to <a href="http://en.wikipedia.org/wiki/Markdown">markdown</a>. I am really not on the bleeding edge, this language is out there since 2004. You can find plenty of articles explaining why markdown is awesome, and how it has revolutionized the workflow of everyone using it.

Shortly, Markdown is a plain text formatting syntax that help you to:

<ul>
    <li>write in plain text with your favorite text editor,</li>
    <li>keep focus on writing, and being less tempted to procrastinating,</li>
    <li>It's really easy, and simpler than HTML ou WIKI syntax.</li>
</ul>

You are not bound on any file format (docx, odt, etc), it's just plain text. It will hardly break or become corrupted ... if you stick with plain a text editor (see below on this part).

Then, when the file is processed by a "markdown aware" viewer, the magic works a you get a nicely formatted text.

<h3>My first enhanced Markdown Editor : The Journalist, Crash &amp; Restore ...</h3>

So I started taking notes with textwrangler, and soon I felt the need for :

<ul>
    <li>A preview of the text rendered, as I type,</li>
    <li>an easy way to manage all theses files.</li>
</ul>

As I was just starting to explore Markdown, I looked for a free app to start with. A quick search on the Mac App Store led me to the application "The Journalist".

The application looks pretty and my two former requirements seemed to be fulfilled. But after <strong>a day of notes</strong>, The Journalist crashed badly and my content was gone! It seems like something got finally corrupted...

Useless to explain how I was scared ... I started digging to find where the application actually stored his data, without success... An email to the application developer, and a couple of hours later I got a kind answer! The folder to look for was the following ( and I would never guess it alone ...) :

<pre><code>~/Library/Containers/com.mimietpaul.journalistmac
</code></pre>

<h4>Rescue the data!</h4>

Basically, The Journalist store data in a sqlite database. I figured it trying to open files with a basic text editor. So all I need, is to open this database file and query it!

Here are the steps to follow in order to recover your <strong><em>"precious"</em></strong> content:

<ol>
    <li>copy the sqlite file:
<pre><code>~/Library/Containers/com.mimietpaul.journalistmac/Data/Library/Application Support/TheJournalist
</code></pre>
</li>
    <li>rename thejournalist.data to .sqlite extension,</li>
    <li>Open the database using a sqlite editor (1)</li>
    <li>query the table "<em>ZJNOTE</em>" to list all the notes
<pre><code>SELECT * FROM ZJONOTE
</code></pre>
</li>
    <li>query on "<em>ZBODY</em>" field obtain to actual content : your notes!</li>
    <li>copy/paste in your favorite text editor,</li>
    <li>you are free!</li>
</ol>

(1) The Firefox extension <a href="https://addons.mozilla.org/fr/firefox/addon/sqlite-manager/">SQLite Manager</a> is a perfect fit for this simple need.

<h3>Conclusion</h3>

This misadventure has allowed me to learn a little more about the specifications for my note-taking tool:

<ul>
    <li>The potential markdown application <strong>must</strong> generate raw text files! (Natively or via export function),</li>
    <li>If the application is enhancing the writing experience (adding metadata, pictures, etc ...) : metadata must be kept separately from files.</li>
</ul>

Be sure that I will check twice before storing my data into an application ...

<h3>Ressources</h3>

<h4>Links</h4>

Here is some useful references and stories about Markdown:

<ul>
    <li><a href="http://daringfireball.net/projects/markdown/syntax">Markdown Syntax</a>, by John Gruber on Daring Fireball, creator of Markdown,</li>
    <li><a href="http://fletcherpenney.net/multimarkdown/">Multimarkdown</a>, an interesting extension to markdown by Fletcher Penney,</li>
    <li><a href="http://hiltmon.com/blog/2012/02/20/the-markdown-mindset/">The markdown mindset</a>,</li>
    <li>A switcher's <a href="http://shayashi.net/tech/why-i-turned-to-markdown/">story to markdown</a>.</li>
</ul>

<h4>Apps and Tools</h4>

Some useful tools for Markdown on OSX

<ul>
    <li><a href="https://itunes.apple.com/fr/app/textwrangler/id404010395?mt=12">Textwrangler</a>, the free edition of the great BBEdit. It recognize markdown syntax (*.md extension),</li>
    <li><a href="https://github.com/toland/qlmarkdown/downloads">QLMarkdown</a> is a OSX plugin for Quicklook. It enable the rendering of markdown files (*.md extension) right in the finder and <a href="http://tidbits.com/article/13499">A how-to guide</a> to assist you installing it.</li>
</ul>

I am currently evaluating <a href="https://itunes.apple.com/fr/app/ulysses-iii/id623795237?mt=12">Ulysse III</a> as my main editor and text file repository. Later on this as I continue digging about it.