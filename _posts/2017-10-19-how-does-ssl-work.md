---
layout: post
tags: SSL
date: 2017-10-19 17:28
thumbnail: https://douglasjd.github.io/blog/images/ssl.jpg
title: How does SSL works
published: true
---
Guide
============
- SSL work flow
- Create a keystore and generate the key pair in server
- Create a selfsigned cert in serer.
- Create a keystore and generate the key pair in client
- Create a selfsigned cert in client.
- Import selfsigned cert (Server) in client truststore.
- Import selfsigned cert (Client) in server truststore.
- Enable SSLv3 in JAVA8
- Custimize Key and trust manage and create a SSLSocketServer
- Custimize Key and trust manage and create a SSLSocketServer

Create a key store and generate the key pair in server
============
The first thing you need to do is create a keystore and generate the key pair. You could use a command such as the following:
```
keytool -genkeypair -dname "cn=jd, ou=Java, o=Oracle, c=SG"
      -alias sslkeys -keypass password1 -keystore svr_keystore.jks  -storepass password1 -validity 180
```
