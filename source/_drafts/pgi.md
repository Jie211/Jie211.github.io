---
title: pgi
tags:
  - centos
  - pgi
categories:
  - lab
date: 2015-08-29 00:00:00
---

install Nvidia OpenACC Toolkits with pgi compiler memo
<!-- more -->

OpenACCToolkitsをダウンロードし，installスクリプトを実行してインストール．
今回はAMD対応以外は全部インストールした．
PGI の　University Developer License　は一年毎に審査付きで繰り返し利用できる．

その申請は３日かかるかもしれないので，PGIからのメールが来る前にはNVのサイトでToolkitsをダウンロードした後にNVからのメールには90日の試しライセンスがついてるのでそれを使う．

    #!bash
    export PGI=/opt/pgi;
    export PATH=/opt/pgi/linux86-64/15.7/bin:$PATH;
    export MANPATH=$MANPATH:/opt/pgi/linux86-64/15.7/man;
    export LM_LICENSE_FILE=$LM_LICENSE_FILE:/opt/pgi/license.dat;

試しライセンスは直接ライセンスデータを/opt/pgi/XX.X/license.datに書き込む

University Developer Licenseは先ず $lmutil lmhostid でhostidを生成，次メールからのリンクに飛んで，hostnameとhostidを入力してライセンスデータを生成，/opt/pgi/XXX/license.datに書き込む

    #!bash
    //ライセンスサーバーを起動
    $ PGI/linux86-64/XX.X/bin/lmgrd.rc start

start license server upon reboot

    #!bash
    $ cp $PGI/linux86-64/15.7/bin/lmgrd.rc /etc/init.d/lmgrd
    $ /sbin/chkconfig --add lmgrd

pgi c langurage

    #!bash
    $ pgcc -acc pi.c
