---
layout: post
title: Simple backups for non-tech users with udev
image: "/content/images/2017/08/Backup-e1450691392717.jpg"
date: '2015-10-30 06:00:28'
tags:
- udev
- automatic
- devops
- non-tech
- sharepoint
- backups
---

<h2>Intro</h2>
<p>
We are using SharePoint 2010 Foundation SP2 as our <a rel="nofollow" href="http://en.wikipedia.org/wiki/Document_management_system">EDM</a> system. Every day we are making backups of our EDM system on external hard drive. A week ago our CEO has decided to has a second copy of the archives and keep it into a safe. 
</p>

<h2>No sooner said that done!</h2>
<p>
Let's make criterias of future backup system:
<ul>
  <li>it must be automatic</li>
  <li>it can do everyone, even non-tech users</li>
  <li>it must do ONLY onto specific external hard drive</li>
</ul>
</p>
<p>
So, what is a system doing? System consists of the following components: a udev rule and a bash script, which run when you insert the concrete external hard drive into destination host. Also, the system depends on the following system packages: cifs-utils, at, ntfs-3g.  
</p>

<p>Backups of our EDM system is lying on a Windows (SMB) share.
When the external hard drive is inserted, the bash script will be runned and will create an `at` job. The `at` job at the specific time (by default 11pm on Saturday) will remove all content on the hard drive, then copy all files and folders from the Windows (SMB) share to the external hard drive.
</p>

<h2>Source Code</h2>
<p>
The source code is available at <a rel="nofollow" href="https://github.com/varp/udevbackup">GitHub</a> and distributed under <a rel="nofollow" href="https://opensource.org/licenses/MIT">MIT License</a>.
</p>

<h2>Installation</h2>
<p>
As every backuping system, our system also need a some bit of configuration. First of all you need to install the system's required packages:
```bash
sudo apt-get install git-core cifs-utils at ntfs-3g -y
git clone https://github.com/varp/udevbackup.git && cd udevbackup
```
Second you need obtain your external hard drive serial number. To obtain serial number run:
```bash
sudo udevadm info --query=all --attribute-walk --root /dev/sdb1
```
The next phase of configuraion: 
```bash
HARD_SERIAL=<your hard serial is obtained from the previous step> SMB_DOMAIN=<domain.local> SMB_SHARE=<//server/share> SMB_USER=<user> SMB_PASSWORD=<password> make configure
``` 
</p>
<p>
To <b>install</b> just run the following in a terminal:
```bash
sudo make install
```
</p>