---
title: How does SSL works
date: 2017-10-19 17:28:00 Z
tags:
- SSL
layout: post
thumbnail: https://douglasjd.github.io/blog/images/ssl.jpg
---

Guide
============
- SSL work flow
- Create a keystore and generate the key pair in server
- Export self-signed certification from keystore.
- Create a keystore and generate key pair in client and export the self-signed cert.
- Import self-signed cert (Server) in client truststore.
- Import self-signed cert (Client) in server truststore.
- Enable SSLv3 in JAVA8
- Custimize Keystore and truststore manager and create a SSLSocketServer
- Custimize Keystore and truststore manager and create a SSLSocketServer

Create a keystore and generate the key pair in server
============
The first thing you need to do is create a keystore and generate the key pair. You could use a command such as the following:
```
D:\>keytool -genkeypair -alias sslkeys -validity 180
Enter keystore password:password1
What is your first and last name?
  [Unknown]:  jd
What is the name of your organizational unit?
  [Unknown]:  java
What is the name of your organization?
  [Unknown]:  oracle
What is the name of your City or Locality?
  [Unknown]:  sg
What is the name of your State or Province?
  [Unknown]:  sg
What is the two-letter country code for this unit?
  [Unknown]:  65
Is CN=jd, OU=java, O=oracle, L=sg, ST=sg, C=65 correct?
  [no]:  yes

Enter key password for <sslkeys>
        (RETURN if same as keystore password):
Re-enter new password:password2
D:\>
```

Export self-signed certification from keystore.
==========
```
keytool -export -keystore svr_keystore.jks -alias svr_cert -file svr_selfsigned.cer
```
