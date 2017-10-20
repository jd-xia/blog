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
$  keytool -genkeypair -dname "cn=Douglas JD, ou=JavaSoft, o=Oracle, c=SG" -alias svrkeys -keyalg RSA -keypass pass123 -keystore svr_keystore.jks -storepass pass321 -validity 180
```

Here the command was named -genKey in earlier release.  While the old one is till supproted, -genkeypair is preferred going forward.


Export self-signed certification from keystore.
==========

```
$ keytool -export -keystore svr_keystore.jks -alias svrkeys -file svr_selfsigned.cer
Enter keystore password:  pass321
```

Print the self-signed cert as following,
```
$ keytool -printcert -file svr_selfsigned.cer
Owner: CN=Douglas JD, OU=JavaSoft, O=Oracle, C=SG
Issuer: CN=Douglas JD, OU=JavaSoft, O=Oracle, C=SG
Serial number: 450824f5
Valid from: Fri Oct 20 10:43:38 SGT 2017 until: Wed Apr 18 10:43:38 SGT 2018
Certificate fingerprints:
         MD5:  16:ED:F9:1F:A4:8C:35:5C:F5:8D:53:86:3A:41:A0:AD
         SHA1: 4C:E3:0A:44:1C:93:8F:37:17:E7:F6:F1:42:64:F4:29:67:F1:C5:83
         SHA256: C6:F9:83:90:C1:D5:5C:59:79:22:2D:11:AC:46:17:45:EE:A9:98:2B:D3:99:8E:88:0D:4F:47:26:BA:0C:58:54
         Signature algorithm name: SHA256withRSA
         Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 77 12 73 80 19 76 58 55   A8 61 26 DD BD CC 38 1C  w.s..vXU.a&...8.
0010: 14 E4 9C 8C              
```
