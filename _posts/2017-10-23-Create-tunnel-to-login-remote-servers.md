---
title: How to use SSH tunneling to access restricted Servers.
date: 2017-10-23 17:28:00 Z
tags:
- ssh
layout: post
thumbnail: https://douglasjd.github.io/blog/images/ssh.jpg
---

Guide
================
- Preparation
- Command SSH
- Practise

Preparation
=====================
- Windows Server A: 192.0.0.100:   Your own personal laptop Internal IP
- Linux   Server B: 10.0.0.100:    Local Server Internal IP
- Linux   Server B: 50.0.0.100:    Local Server External IP (Another Network Interface)
- Linux   Server C: 172.0.0.100:   Remote Server Extermal IP

<!--more-->

- Create a user in server B.</br>
  User: revtunnel             </br>
  Password: tunnel123      </br>

- Grant Server A has permission to access port 1022 at server B
- Grant Server C has permission to access port 80 at server B
- Map port 80 to 22 in server B.

Command SSH
======================
```
-f' Requests ssh to go to background just before command execution.
-N' Do not execute a remote command. This is useful for just forwarding ports (protocol version 2 only).
-R Specifies that the given port on the remote (server) host is to be forwarded to the given host and port on the local side.
-p port Port to connect to on the remote host.
```

Practise
=========================
- Go to server C and execute below command
```
ssh -f -N -R 50.0.0.100:1022:172.0.0.100:22 revtunnel@50.0.0.100 -p 80
```
- Open Putty to connect 10.0.0.100:1022 (It requires login user and password.)
- It will automatically connect to remote server 172.0.0.100
