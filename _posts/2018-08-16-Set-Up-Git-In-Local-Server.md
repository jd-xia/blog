<<<<<<< HEAD
------

2018-08-16-Set-Up-Git-In-Local-Server
=======
---
title: Set Up Git In Local Server
date: 2018-08-16 00:00:00 Z
---

## 2018-08-16-Set-Up-Git-In-Local-Server
>>>>>>> 5d48952d7e87333ef48ebd58b0283432aa491ef2

title:  Set Up Git In Local Server
date: 2018-08-16 11:15:00 Z
tags:

- Git Local Server
- SSH
  layout: post
  thumbnail: https://douglasjd.github.io/blog/images/git.png

------

## Local Server - Window 10

### Create SSH Key

- Go to https://git-scm.com/downloads down load git 

- Run git-bash.exe

- In a bash shell, run

  ```shell
  ssh-keygen -t rsa -C "your_email@example.com"
  ```

- Associating the key with your email address helps you to identify the key later on. 

  You will a response similar to this:

  ```shell
  xiajiandao@MVINB-HKT MINGW64 /
  $ ssh-keygen -t rsa -C "Hackett@mvitech.com"
  Generating public/private rsa key pair.
  Enter file in which to save the key (/c/Users/xiajiandao/.ssh/id_rsa):
  ```

- Just press <Enter> to accept the default location and file name. 

- Enter and re-enter a passphrase when promoted.  Remember it as it will be used in below steps

- Two files will be generated

  - id_rsa          <RSA Private Key>
  - id_rsa.pub   <RSA Public Key>

## Remote Server - Centos 

- Login as root user

- Install and Configure Git

  ```
  yum install git
  ```

- you create a `git` user and a `.ssh` directory for that user.

  ```shell
  sudo adduser git
  su git
  $ cd
  $ mkdir .ssh && chmod 700 .ssh
  $ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
  ```

- You just append them to the `git` user’s `authorized_keys` file in its `.ssh` directory: 

  ```shel
  $ cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
  ```

- you can set up an empty repository for them by running `git init` with the `--bare` option, which initializes the repository without a working directory:

  ```console
  $ cd ~
  $ mkdir project.git
  $ cd project.git
  $ git init --bare
  Initialized empty Git repository in /srv/git/project.git/
  ```



## Local Server - Window 10

- Download SourceTree tool. [](https://www.sourcetreeapp.com/)

- Go to Tool-> Add SSH Key 

- Import created private key under `c/Users/xiajiandao/.ssh/id_rsa`

- Go to File -> Clone/New, clone the remote repository

  ```form
  1. Source Path = git@remote_server:project.git
  2. Destination Path =  #Choose an local location#
  ```

- Click clone.



## Reference

- http://tedfelix.com/software/git-server.html
- https://www.youtube.com/watch?v=r93gUZbjWEo
- https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server

- https://confluence.atlassian.com/bitbucketserver/creating-ssh-keys-776639788.html