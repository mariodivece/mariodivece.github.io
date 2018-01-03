---
layout: post
title: "Windows Server 2016 Anonymous File and Printer Sharing"
date: 2017-01-24
nocomments: false
area: "blog"
description: Creating anonymous Windows Shares for printers
tags: Infrastructure Windows
---

# Windows Server 2016 Anonymous File and Printer Sharing

I wanted to experiment a bit with anonymous File and PRinter Sharing in Windows Server 2016. I needed local LAN users (not domain-joined) to be able to access folders and printers without getting prompted for a username and a password. I read a lot in the forums and I found a lot of stuff that isn't really necessary. Please note that this method anables the Guest account in the Server so please don't use it in an enterprise environment. Just manage Domain users instead.

## Preparation Work

`Start` > `Local Secirity Policy` > `Local Policies` > `Security Options`
 - Accounts: Guest Account Status: `Enabled`
 - Network access: Let Everyone permissions apply to anonymous users: `Enabled`
 - Network access: Do not allow anonymous enumeration of SAM accounts and shares: `Disabled`
 
<img class="img-fluid" src="/img/local-segurity-policy.png" />

## Sharing a Folder

Locate the folder you want to share > Right Click on it > `Properties . . .`
 - `Sharing` tab: `Advanced Sharing` > `Permissions` > `Add . . .` > Add `Everyone` > Check `Read` and `Change`
<img class="img-fluid" src="/img/file-sharing.png" />
 - `Security` tab: `Edit . . .` > `Add . . .` > Add `Everyone` > Check `Full Control` (or approriate file permissions)
<img class="img-fluid" src="/img/file-security.png" />

## Sharing a Printer
`Start` > `Devices and Printers` > Locate the printer to share and right-click on it > `Printer properties`
  - Sharing tab: `Share this printer`: ticked, `Render print jobs on client computers`: ticked
 <img class="img-fluid" src="/img/printer-sharing.png" />
  - Security tab: `Add . . .` > Add `Everyone` > Check all boxes
<img class="img-fluid" src="/img/printer-security.png" />

## References
 - http://serverfault.com/questions/51635/how-can-an-unauthenticated-user-access-a-windows-share
 - http://nikolar.com/2015/03/10/creating-network-share-with-anonymous-access/
 
