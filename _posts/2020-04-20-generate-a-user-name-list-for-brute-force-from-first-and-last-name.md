
---
layout: post
title: "Generate a user name list for brute force from first and last name"
date: 2020-04-20 22:00:00
tags: jtr bruteforce wordlist
description: How to generate user name lists suitable for brute force attacks from first name and last name using custom John the Ripper rules
comments: true
---

## Intro
If you need to perform a brute force attack against some organization you will definitely need a good list with user names. Companies in general use one or another email pattern for their employees, like ``` First_Name@company.com``` or ```f.name@company.com```. 
The problem occurs when you don't know what pattern exactly was used by users or the particular company doesn't force employees to use any specific pattern at all.

## Open source solutions
I faced with the same problem and didn't find any "usable" and quick solution in open source tools, except some [custom scripts](https://gist.github.com/superkojiman/11076951). 
But for me personally it's more convenient to use well-known tools for password generation, like **hashcat** or **John the Ripper**. Unfortunately, I didn't find a set of rules which can be used to create good username lists with all suitable permutations. So, i decided to spend some time and create such rules.

## Username generation from first and last name
To start with username generation you should first create a list with company employees' first and last name, like this:
```
John Doe
Robert Roe
Zachery Zoe
```
Social networks like LinkedIn or custom search engines like [Email Hunter](https://hunter.io/) might be useful here.
John the Ripper rules are listed in ```/etc/john/john.cfg``` and you need to add my new rules to the end of this config.
To do this, execute the following one-liner:
```
curl https://gist.githubusercontent.com/dzmitry-savitski/65c249051e54a8a4f17a534d311ab3d4/raw/5514e8b23e52cac8534cc3fdfbeb61cbb351411c/user-name-rules.txt >> /etc/john/john.conf
```
Or add all the rules manually to the end of your ```/etc/john/john.cfg```

## Usage
I've created two sets of rules for case insensitive and case sensitive lists generation.
To generate only lowercase logins use the following command line:
```bash
john --wordlist=first_last_names.txt --rules=Login-Generator-i --stdout > usernames.txt
```
For case sensitive output use:
```bash
john --wordlist=first_last_names.txt --rules=Login-Generator --stdout > usernames.txt
```
John the Ripper will produce list like this for **John Doe**:
```
johndoe
john_doe
john.doe
john-doe
john
doe
jdoe
j.doe
j_doe
j-doe
johnd
john_d
john.d
john-d
doejohn
doe_john
doe.john
doe-john
djon
d.john
d_john
d-john
doej
doe_j
doe.j
doe-j
```
It might be also suitable to append domain to our word list:
```bash
john --wordlist=first_last_names.txt --rules=Login-Generator --mask=?w@victim.com --stdout > usernames.txt
```
Output:
```
johndoe@victim.com
john_doe@victim.com
john.doe@victim.com
...
```

## References
 - [John the Ripper rules](https://www.openwall.com/john/doc/RULES.shtml)
 - [Hashcat rules](https://hashcat.net/wiki/doku.php?id=rule_based_attack)
 - [ Creating a user name list for brute force attacks](https://blog.techorganic.com/2011/07/17/creating-a-user-name-list-for-brute-force-attacks/)

### Good luck and happy hacking!

