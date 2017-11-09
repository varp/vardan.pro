---
layout: post
title: Installation of TeamViewer on Ubuntu via SSH (Terminal)
image: https://vardan-pro.s3.amazonaws.com/2016/Aug/team-1470634134424.png
date: '2015-05-06 05:26:00'
tags:
- trusty
- ubuntu
- teamviewer
- ssh
- terminal
---

<h2>Intro</h2><p>When I searched a solution to access remotely my Unity desktop on 14.04, I
have faced with a little problem. It appears to be that all the
solutions I found (xdrp, RealVNC, X11 forwarding) might only work
with 2D desktop environments like as XFCE, Mate (the first two
solutions) or can only forward “raw” X apps’ renders (the last
solution). No one of it fit my needs.<br/></p><p>The only solution that fits needs was the TeamViewer.  It&rsquo;s not hundred
percent a UNIX solution, but it appears to be the only one solution
that can give me access to Unity 3D session. Ok, I thought, let&rsquo;s
install TeamViewer! But, I was have only SSH access to my Ubuntu box.</p><h2>Installing</h2><p>If you will try to install TeamViewer 64bit deb you will be fail because
in Ubuntu 14.04 (amd64) and above the <b>lib32asound2 </b>package was removed which on the TeamViewer
64bit depends. Though, we will install i386 package of TeamViwer.
Enough words!</p>
```bash
$ cd ~/Downloads && wget http://download.teamviewer.com/download/teamviewer_i386.deb
$ sudo dpkg –add-architecture i386
$ sudo dpkg -i ~/Downloads/teamviewer_i386.deb
$ sudo cp -v /opt/teamviewer/tv_bin/script/teamviewerd.sysv /etc/init.d/teamviewerd
$ sudo teamviewer daemon enable
$ sudo teamviewer daemon start
$ sudo update-rc.d teamviewerd defaults
$ sudo teamviewer --passwd <at least 8 characters>
$ sudo reboot
```
<p>Reconnect to your Ubuntu box after it was rebooted and type in SSH terminal:</p>
```bash
sudo teamviwer info
```
<p>There you will see version of TeamViewer, ID of your TeamViewer to connect
to and the TeamViewer daemon
status.</p>
<p>That&rsquo;s all. Grab your TeamViewer ID and remotely enjoy your Unity 3D session =).</p>
