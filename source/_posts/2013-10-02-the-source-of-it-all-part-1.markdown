---
layout: post
title: "The source of it all...! [Part 1]"
date: 2013-10-02 07:38
comments: true
categories: "source-code, javascript"
---

We finally made an attempt to go through the source and wow its big! One thing about OpenSource projects is how much you learn by understanding the source; not just getting to know it but learning and understanding the design concepts! [This wiki page][1] tells you what to do to get the source and how to build it. 

<!-- more -->

This post is aimed to give you an overview of some aspects of developing 0 A.D. Although these details are available in the wiki, they are not yet structured in an understandable way. Here, we gather many wiki articles and also provide some information on how the game is structured.

### What does the root directory contain?

Let's have an overview of it first.

{% codeblock Root Directory %}
(0ad)source@svn : /$ tree
.
|-- binaries/
|-- build/
|-- docs/
|-- libraries/
|-- source/
|-- license_dbghelp.txt
|-- license_gpl-2.0.txt
|-- license_lgpl-2.1.txt
|-- LICENSE.txt
`-- README.txt

5 directories, 5 files
{% endcodeblock %}
 
**binaries →** This contains all of the game's data, scripts that are executed during the game, mods and other executables (typically for windows). This is what goes to the end user.  
**build →** This has the necessary tools and programs to set up a workspace to build the game. More instructions on how to build the game can be seen [here][2].  
**docs →** All documentation goes here.  
**libraries →** Placeholder for third-party libraries used in the game. However, they may or may not be used.  
**source →** This contains the source of the game engine and the source of other tools that are associated with the game (Eg: The scenario editor).

#### Are there more details available? 
A more detailed information on what these directories (and their subdirectories) contain can be found [here][3]. 

#### What resources are available for a contributor to learn more about the game?
The first is the [trac wiki][4]. This has a lot of links for all kinds of contributors - artists, programmers, etc. The next place are the [game forums][5]. One needs to register for the forums and there are 5 categories of forums available; the description of which can be seen under the link to the particular forum. And lastly, the best place to communicate is the IRC. You can join the channels`#0ad` and `#0ad-dev` in `QuakeNet` and jump right into a live discussion over there!

#### What are the languages used to implement the game design?
The source of the game is written in C++ and JS. Interesting right?  
The convention is to have as much of the game's source in JS as possible (to keep it simple) and only write the core game engine and those components which need performance in C++. This enables the game to be heavily moddable through JS.

#### I would like to be a developer and was wondering how to start with the game-dev. Any tips?
As we said, the [trac wiki][4] is the entry point for getting a picture of the game's structure. The next step would be to start reading the source (which is discussed from the next post onwards). You'll also need to know the [coding guidelines and conventions][6] that are followed so you can understand the source better and also will get you prepared when you start with the development. You would also need to learn [how to build the game][1], which for a developer, is vital. You can then look at some [tickets available on trac][7] and try to solve a few easy ones. This actually kickstarts the development and helps you to learn more about the internal structure of the game. You can start off with a few [beginner-level tickets][8].

[1]: http://svn.wildfiregames.com/public/ps/trunk
[2]: http://trac.wildfiregames.com/wiki/BuildInstructions
[3]: http://trac.wildfiregames.com/wiki/Finding_Your_Way_Around
[4]: http://trac.wildfiregames.com/
[5]: http://www.wildfiregames.com/forum/index.php
[6]: http://trac.wildfiregames.com/wiki/Coding_Conventions
[7]: http://trac.wildfiregames.com/report
[8]: http://trac.wildfiregames.com/query?status=assigned&status=new&status=reopened&keywords=~simple&col=id&col=summary&col=status&col=type&col=priority&col=milestone&col=component&report=16&order=priority