---
layout: post
title: Grand refactoring of SublimeText Nodejs plugin
date: '2017-11-30 8:58:26'
tags:
    - sublimetext
    - Nodejs
    - refactor
    - plugin
---

I'm very happy to announce the second version of Nodejs Sublime Text plugin. 
This is big release with a big refactorings and new features.

## Intro

On November 2016 I've started to support (this is said very strongly), 
started to play around with SublimeText [Nodejs plugin](https://packagecontrol.io/packages/Nodejs). The plugin was very 
small and has several bugs and unknown issue under the hood.

In those days I was playing around with Nodejs and favorite code editor was 
SublimeText, as always. I installed Nodejs plugin from 
[packagecontrol.io](https://packagecontrol.io) and found several bugs as I 
mentioned earlier. I thought... Ohh, this is awful, take in count that Nodejs
was a hype, and still the hype, but nowadays with a small pressure of Go. IMHO. 😇

In a such situation I directly go the GitHub repository of the project and 
looking for recent activity, to be sure that project is not abandoned. I saw 
that last commit was made to the project was in late 2013. Also, users left 
issues, but with no answer from the maintainer. Then, I thought... There is a 
two piece of technology which I love, and I always want to contribute to the 
Open Source. No sooner said than done. 

The plan was simple. Fixing bugs which I will encounter with. Over time, I 
realized problem in fixing bugs, because of plugin architecture, missing of 
tests, debugging capabilities etc.

## The plans is changing

As I mentioned earlier I was suffering without tests and debug capabilities. 
One bug fix could fork another bugs and issues. More over, some bugs could be 
fixed on one platform and still exists on another ones. Then I decided to make 
my life easier and makes me more happier.

## What was done

A lot stuff was refactored and reconsidered. All changes logical could be 
divided in two groups: for user experience and for developer comfort.

### Developer comfort
Lets start with a list of changes and little explanation for what, how and 
possible why it was done.
- The whole code of plugin in earlier versions was located at one big file 
`Nodejs.py`. Will make decision to divide and re-locate the whole code to base 
into logical modules and move into `lib` folder. Now he have the following 
logical parts:
    1. `nodejs_base.py` - base command classes for the rest of Sublime Text commands
    2. `nodejs_command_thread.py` - classes for running underlying OS specific commands
    3. `nodejs_commands.py` - core classes for the plugin functionality (node_run, node_drun and etc.)
    4. `nodejs_constants.py` - several constants for PLUGIN_PATH and etc.
    5. `nodejs_debug.py` - debugging functions
    6. `nodejs_nvm.py` - class for detecting and working with NVM (Nodejs Version Manager)

- Added debugging capabilities. Throughout of the code you call `debug` functions 
to output some of debug data to the Sublime Text console. The data will be printed 
only if file `.debug_plugin` exists in the plugin root directory. Simple remove 
the file to disable debug output.

- Unit testing was introduced with help of great job done by [Randy3k](https://github.com/randy3k/UnitTesting).
This is a great module for writing acceptance tests for Sublime Text 
plugins. You can also use it to write simple plain unit tests for core parts 
of your plugin using plain old Python.

- The Randy3k unit testing plugin also allow you to run tests in CI (continuous 
integration) services such as Travis Ci and Appveyor. Bang! I used it as well as 
tests. Now we can test plugin functionality on different platforms with a 
different versions of Nodejs.

- Plugin now requires dependencies to be installed: `sheellenv` and `newterm`, 
which means after installing the plugin the Sublime Text will be restarted.

With the help of all the stuff listed above, we can develop new or bug fixing 
old functionality by enjoying.🕺

### User experience

Ok. We got to the most interest part - user interaction with plugin. As in 
previous part I will list of changes and reasons for it.
- It sad to say, but from now we only support Sublime Text 3. Sublime Text 2 
support is deprecated, because of long waited Sublime Text 3 official release.
It's not beta now.

- The most annoying (I think there was around 3-4 bugs related with it) incorrect 
auto complete. Sometimes it had replaced the part before dot: `os.chdir()`, 
`chdir()` replaced the `os.` part. Sometimes it had duplicated the part after a 
dot: `http.createServer()` duplicated the `http` part. The issue was solved by 
rewrite `tools/doc_builder.js` by adding Nodejs standard library modules names 
to the `Nodejs.sublime-completions` as a separate auto complete items.

- Now, on the plugin loading, it detects if a Node Version Manager is installed 
and if it is, it gets current Node version from the NVM and use it by default 
instead of globally installed one, if any.

- Also, on the plugin loading, it build completions list for a current version of 
Nodejs

- On the plugin loading, it install all Nodejs dependencies need for plugin tools, 
located under `tools` folder: `doc_builder.js` and `uglify_js.js`. Before, you 
will need to install it manually.

- At the core plugin, now, detects different versions of Nodejs. At the moment 
of release it is a Nodejs 6 branch and 8 branch aka LTS and current stable.

- Plugin settings now opens in style of Sublime Text 3, by `edit_settings` 
command, default settings on the left side and user custom settings on the right 
side

- From now, commands ('node_drun', 'node_npm_install' and etc.) could be run only 
when type or the buffer/file is type of `source.js`

- OK. What is the most important tool of every developer? You, right! It's a debugger.

### *Debugger* 

In previous versions of the plugin, Nodejs Debug (+ arguments)
command would run only the current file with a `debug` argument passed to `node` 
executable. It was a useless. Since version 6.3.0 of Nodejs it support debugging 
using Chrome DevTools debugger. Read more at [Paul Irish Medium blog](https://medium.com/@paul_irish/debugging-node-js-nightlies-with-chrome-devtools-7c4a1b95ae27).

Now, when you run Nodejs Debug (+ arguments) the `node` executable will run 
current file with flags `--inspect=localhost:60123 --debug-brk` for version 6 of 
Nodejs and `--inspect-brk=localhost:60123` for version 8. As you can see, 
debugger would run on a separate port 60123 which is not correlate with 
 the default Nodejs - 9229.

Then, you can connect to the debugger using steps instructed by the plugin:
```text
    Debugger is successfully started at localhost:60123.
    1. Now you can open Google Chrome and navigate to chrome://inspect.
    2. Then click Open dedicated DevTools for Node. 
    3. After click Add connection and add connection to localhost:60123
```

## What need to be done

- Recreate functionality of Nodejs cli debugger using Sublime Text 3 dependency - 
[sublime-pexpect](https://github.com/varp/sublime-pexpect), which I contributed 
to [packagecontrol.io](https://packagecontrol.io) recently.

- Track Nodejs processes ran by the plugin and kill only that processes in 
`_kill_node_processes()`. For that, need to be done work by contributing another 
Python package to the Sublime package control - [sublime-psutil](https://github.com/varp/sublime-psutil).

- Run current JS file in new terminal, instead of output to the Sublime Text 
output panel.