---
layout: post
title: Script for guessing MySQL table charset and collation
image: "/content/images/2017/07/mysql_charset_guessing.png"
date: '2017-07-10 17:40:26'
tags:
    - bash
    - mysql
    - charset
    - collation
    - script
---

## Intro
Sometimes we getting in a situation where have MySQL database where we have tables and even and columns in that tables in different charset/collations.

In such situations, we can "stick" in the problem where a DB has one charset/collation and a table has another charset/collation. On other hands, we can have tables where charset/collation match with DB's charset, but data inserted in that tables in a third charset/collation, and it seems like corrupted.

In such situation, we can have the following: when we trying to select data from a table whose charset/collation match with the DB's charset or not match but inserted in different charset/collation we get the:
```
+------+------------------------------+----------------------+
| id   | name                | surname                       |
+------+------------------------------+----------------------+
| 5100 | Ð                   | Ð¡Ð˜ÐÐ®ÐšÐžÐ’                 |
| 5234 | Ð¡Ð•Ð Ð“Ð•Ð™        | ÐœÐÐ§Ð˜ÐÐ¡ÐšÐ˜Ð™              |
+------+------------------------------+----------------------+
```
The data seems like it to be corrupted. To solve the problem if the data exactly corrupted or by mistake inserted in the wrong charset/collation, we must check the data against charset/collation supported by your version of MySQL. And then we can decide, the data really corrupted (if we can not find at least one right charset/collation combination with readable text) or to the data applied wrong charset/collation.

## Preparation
Okey. First of all, we must find out which charset and collation support your installed MySQL version. For that, we go the terminal and:
```bash
mysql -u root -p'<your password>' -e 'show collation'
```
The next, we must get the output and convert to the following format: the first column of the output and the second column separated by the symbol '|'. For that purpose, I just copy-pasted output to the Sublime Text editor and with a help of multiline editing feature got the needed format. For example:
```bash
latin1_swedish_ci|latin1
latin1_bin|latin1
latin1_general_ci|latin1
latin2_general_ci|latin2
latin2_bin|latin2
ascii_general_ci|ascii
ascii_bin|ascii
```
After you have done converting, save it under you `$HOME` directory path with name `mysql.collations.txt`.

## Code
Okey. We get to the fun part 😊 The following simple line of the Bash script, get the input file prepared in the previous step and iterating through it. On the each iteration it changes a connection charset and collation and select one row from your desired table and duplicate its output to a file in the current working directory with name `mysql.codepage.guess.log`.

```bash
for row in $(cat "$HOME/mysql.collations.txt"); do charset=$(echo $row | cut -f2 -d'|'); collate=$(echo $row | cut -f1 -d'|'); echo -e "\t\tCHECKING: $charset => $collate"; mysql -u root -p'<your password>' -e "use <your db name>; set names $charset collate $collate; select * from <your table name> limit 1;"; done | tee mysql.codepage.guess.log
```
## Result
Here is presented a few lines of the file `mysql.codepage.guess.log` from my run (the outpur is cut for simplicity):
```bash
		CHECKING: latin1 => latin1_swedish_ci
id	name	surname	
5100	А	СИНЮКОВ	
		CHECKING: latin2 => latin2_bin
id	name	surname
5100	??	??????????????
```
That's all. Here you can see that the right charset/collation for my table is `latin1_swedish_ci`. On the production, someone before me has set collation for the table to the `utf8_general_ci` but data was inserted in `latin1_swedish_ci`. Thank you for your time 🕺