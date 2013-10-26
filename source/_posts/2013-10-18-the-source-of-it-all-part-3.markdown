---
layout: post
title: "The source of it all...! [Part 3]"
date: 2013-10-18 07:46
comments: true
categories: 
---

In the last two posts we looked at how the game source is organized and how it launches. We'll now go deeper and delve into the huge codebase of C++/JS and how these two languages communicate.

<!-- more -->

The first thing to know is that 0 A.D. uses a lot of wrappers and home grown entities like a string class, filesystem class, a compressor, parsers for parsing game related files, thread managers, etc. All such classes can be found in **source/ps** directory (ps stands for *pyrogenesis*, the name of the game engine). But what interests us, lies in the **source/simulation2** directory. This contains all the core components that are used by the Engine to communicate with the simulation aspects of the game. So what do we have here? 

{% codeblock Root Directory %}
(0ad)source@svn : simulation2$ tree
.
|-- components/
|-- docs/
|-- helpers/
|-- scripting/
|-- serialization/
|-- system/
|-- tests/
|-- MessageTypes.h
|-- Simulation2.cpp
|-- Simulation2.h
`-- TypeList.h

7 directories, 4 files
{% endcodeblock %}

Majority of the code here resides in the [components/][101] directory. This has most of the major components (as the dir name suggests) required for running handling the simulation, including code which runs the scripts written in JS. Most of the components are implemented with an supposed-to-be-interface class which the actual component class inherits. Example, `CCmpAIManager` inherits `ICmpAIManager`; where the first letter represents a class or interface respectively. (more info on the coding guidelines and conventions can be seen [here][1])

Since our main target is the AI, let's try to understand how the AI begins. A lot of components are registered in the game; one of them being the AIManager component, defined in [CCmpAIManager.cpp][102]. This has the [CCmpAIManager][103] class. Before going to the details of this class, let us see something very interesting. 

The source uses SpiderMonkey to bind C++ and JS. The C++ source exposes a variable called ``Engine`` to the JS runtime, which is an instance of the [ScriptInterface][105] class. This is created in the singleton [ScriptingHost][106] class. The singleton object creating is handled through a [Singleton][107] base class. So what's the result? The JS runtime can now access the C++ routines through the ``Engine`` object (which internally is an object of `ScriptInterface`).  
The next step would be to attach routines to this Engine object so that they'd be exposed to the JS runtime and this is done through the ``RegisterFunction`` function, in the file [NativeWrapperDecls.h][108] which registers a C++ function with the `Engine`object. So if a function named ``Foo`` is registered, then this function can be accessed in JS as `Engine.Foo()`. Ain't it a beauty??!

So, we have a function `Script_QueryInterface` of the [CComponentManager][109] class registered with the `Engine` object with the name `QueryInterface`. What this does is give an object of the queried interface. So to obtain an object of the AIManager component, (whose interface is [ICmpAIManager][110] and implemented in [CCmpAIManager][103]; henceforth, we'll call this *AIManager*) all we need to do is use `Engine.QueryInterface`. To specify that we require the AIManager component, the ID is passed as the second argument to this function. The ID of each component is declared as an *enum* in [source/simulation2/system/Components.h][111].
This is exactly what the script [InitGame.js#L13][112] does. So there it is! The JS runtime's access to the Engine's AIManager through the JS object `cmpAIManager`; this object is the gateway for all communication between C++ and JS (those related to AI)

Now lets unwind our discussion stack. "How does the AI start?" was the question we started off with. Understand that `cmpAIManager` is an object of the AIManager component. This component has a function - [AddPlayer][113], defined in [CCmpAIManager][103]. If you check [InitGame.js#L20][113], this function is called through the `cmpAIManager`. The [CAIWorker][104] class implements the worker thread for the AIManager. An object of this class is stored in `CCmpAIManager`  as `m_Worker`. The AIWorker class has an `AddPlayer` function. This is called by the `AIManager`'s `AddPlayer` function. You can observe this [here][115].  
The `CAIWorker.AddPlayer` function initializes the AI by calling the appropriate AI module (The directory of the AI-bot which contains all the relevant AI scripts. The order of functions that are called go like this -  
The `AIManager.AddPlayer`[[1]][116] calls the `AIWorker.AddPlayer`[[2]][117]. This first initializes the AI by calling `AIPlayer.Initialize`[[3]][118] which in turn calls `AIPlayer.LoadScripts`[[4]][119]. This function is responsible for loading the AI module by using `ScriptInterface.LoadGlobalScriptFile`[[5]][120] which physically loads and executes the script file using SpiderMonkey bindings.

And there you go! The AI module is loaded! So, while setting up the game, if you select the AI bot as **Aegis Bot** for an AI Player, then the [aegis module][121] will load. Make a note of the file `_init.js`[[6]][122] in the module. This file loads modules which are to be loaded before the rest of the current module is loaded. For example, the *Aegis Bot* requires *Common API v3* to be loaded (you can have a look at the *Common API v3* module [here][125]). Also note that `Engine.IncludeModule` is registered to `CAIPlayer::IncludeModule`[[7]][124] (which can be seen [here][123]) which again calls the `AIPlayer.LoadScripts`[[8]][119] function.

Phew! Learning how the JS is executed is one exhausting task! See you in the next post. Bye!


[1]: http://trac.wildfiregames.com/wiki/Coding_Conventions
[101]: /docs/dir_1d614d6cd62ee91a565feed8a30f6de5.html
[102]: /docs/CCmpAIManager_8cpp_source.html
[103]: /docs/classCCmpAIManager.html
[104]: /docs/classCAIWorker.html
[105]: /docs/classScriptInterface.html
[106]: /docs/classScriptingHost.html
[107]: /docs/classSingleton.html
[108]: /docs/NativeWrapperDecls_8h_source.html
[109]: /docs/classCComponentManager.html
[110]: /docs/classICmpAIManager.html
[111]: /docs/Components_8h_source.html
[112]: https://github.com/0ad/0ad/blob/master/binaries/data/mods/public/simulation/helpers/InitGame.js#L13
[113]: https://github.com/0ad/0ad/blob/master/binaries/data/mods/public/simulation/helpers/InitGame.js#L20
[114]: https://github.com/0ad/0ad/blob/master/binaries/data/mods/public/simulation/helpers/InitGame.js
[115]: /docs/CCmpAIManager_8cpp_source.html#l00965
[116]: /docs/CCmpAIManager_8cpp_source.html#l00963
[117]: /docs/CCmpAIManager_8cpp_source.html#l00512
[118]: /docs/CCmpAIManager_8cpp_source.html#l00209
[119]: /docs/CCmpAIManager_8cpp_source.html#l00185
[120]: /docs/ScriptInterface_8cpp_source.html#l00952
[121]: https://github.com/0ad/0ad/blob/master/binaries/data/mods/public/simulation/ai/aegis
[122]: https://github.com/0ad/0ad/blob/master/binaries/data/mods/public/simulation/ai/aegis/_init.js
[123]: /docs/ScriptInterface_8cpp_source.html#l00089
[124]: /docs/ScriptInterface_8cpp_source.html#l00106
[125]: https://github.com/0ad/0ad/blob/master/binaries/data/mods/public/simulation/ai/common-api-v3