---
layout: post
title: Grand refactoring of SublimeText NodeJs plugin
image: "/content/images/2017/07/mysql_charset_guessing.png"
date: '2017-11-30 8:58:26'
tags:
    - sublimetext
    - nodejs
    - refactor
    - plugin
---

# Intro

On November 2016 I've started to support (this is said very strongly), 
started to play around with SublimeText NodeJs plugin. The plugin was very 
small and has several bugs and unknown issue under the hood.

In those days I was playing around with NodeJs and favorite code editor was 
SublimeText, as always. I installed NodeJs plugin from 
(packagecontrol.io)[https://packagecontrol.io] and found several bugs as I 
mentioned earlier. I thought... Ohh, this is awful, take in count that NodeJs
was a hype, and still the hype, but nowadays with a small pressure of Go.

In a such situation I directly go the GitHub repository of the project and 
looking for recent activity, to be sure that project is not abandoned. I saw 
that last commit was made to the project was in late 2013. Also, users left 
issues, but with no answer from the maintainer. Then, I thought... There is a 
two piece of technology which I love, and I always want to contribute to the 
Open Source. No sooner said than done. 

The plan was simple. Fixing bugs which I will encounter with. 