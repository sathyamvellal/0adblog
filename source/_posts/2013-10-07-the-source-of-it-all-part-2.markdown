---
layout: post
title: "The source of it all...! [Part 2]"
date: 2013-10-07 13:14
comments: true
categories: 
---

In the last post we documented an overview of the game and also some vital resources that are to be used during the game-dev So lets start descending into the source. In this post we'll discuss how the game starts and runs.

Also, you can access the doxygen generated doc of the game's source available [here][2]. This documentation will be regularly updated and will be referred to throughout the blog.

<!-- more -->

If you build the game on your own, using the source, you typically get a binary `pyrogenesis` in the `binaries/system` directory and the game will be using data or scripts present in `binaries/data/mods/public`; unless the game is compiled with some flags specifying the binary, data or lib directories while setting up the workspace (info can be found [here][1]).

So let's begin! When the game is run, it starts off by performing some initializations.

1. The thread on which the game started is marked as the main thread and its named "main". [\[GameSetup.cpp:827\]][10]
2. The current time is noted so that elapsed time can be counted from this point. [\[GameSetup.cpp:838\]][11]
3. The profiler is initialized and started. The profiler is used for analysing framerate fluctuations or glitches and temporal relationships between threads. [\[GameSetup.cpp:842\]][12]
4. The locales are set for the game execution depending on the OS. [\[GameSetup.cpp:844\]][13]

After this is done, the game is then ready to start running. Now, 

1. The command line arguments are interpreted (if any given). [\[main.cpp:446\]][14]
2. A virtual file system is created and the game's cache and data are loaded. [\[main.cpp:465\]][15]
3. The game is now started and the graphics initialized. [\[main.cpp:504\]][16]
4. The input handlers are initialized and set. [\[main.cpp:506\]][17]

By now the game is up and running and the profiler would be capturing every frame.  When the game is quit, the shutdown sequence starts. You can see all this starting from [source/main.cpp][3] and related files. There's just too much code to inline in the post!

While this certainly gives a fair idea of how the game begins, its worth noting some classes and features available in the source.

The Core Engine modules are placed in [source/ps/][4] directory and they are used all over the source. A few of them are -

1. [ArchiveBuilder][20]: Used to package mod files into distributable archives.
2. [Console][21]: The in-game scripting console.
3. [ConfigDB][22]: Manage configuration variables for the game.
4. [CStr][23]: A string wrapper designed to provide better string handling.
5. [Filesystem][24]: A class to provide filesystem-like functionality to the API which makes it easier to access files for the game.
6. [GameSetup/CmdLineArgs][25]: A class to parse the command line arguments.
7. [GameSetup/Paths][26]: Wrapper class for OS Paths used in the game.

... and many more! You can have a look at [the directory][4] to view all the files in it.
Our work isn't done yet! Our initial focus has been on the AI which we'll be covering next.

To be continued ...

[1]: http://trac.wildfiregames.com/wiki/BuildAndDeploymentEnvironment
[2]: /docs/index.html
[3]: http://svn.wildfiregames.com/public/ps/trunk/source/main.cpp
[4]: /docs/dir_0dc38aac9085a57339423d7e064a603d.html
[10]: /docs/GameSetup_8cpp_source.html#l00827
[11]: /docs/GameSetup_8cpp_source.html#l00838
[12]: /docs/GameSetup_8cpp_source.html#l00842
[13]: /docs/GameSetup_8cpp_source.html#l00844
[14]: /docs/main_8cpp_source.html#l00446
[15]: /docs/main_8cpp_source.html#l00465
[16]: /docs/main_8cpp_source.html#l00504
[17]: /docs/main_8cpp_source.html#l00506
[20]: /docs/ArchiveBuilder_8h_source.html
[21]: /docs/CConsole_8h_source.html
[22]: /docs/ConfigDB_8h_source.html
[23]: /docs/CStr_8h_source.html
[24]: /docs/Filesystem_8h_source.html
[25]: /docs/CmdLineArgs_8h_source.html
[26]: /docs/Paths_8h_source.html