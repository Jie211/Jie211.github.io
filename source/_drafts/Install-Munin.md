---
title: Install Munin
tags:
  - centos
  - gpu
  - server
  - munin
categories:
  - lab
date: 2016-04-07 00:00:00
---

#Install Munin
OS: CentOS 6
<!-- more -->
+ Server side install

1. install Apache httpd and turn on

		# yum -y install httpd
		# /etc/rc.d/init.d/httpd start
2. Open 80 port for Apache

		# vi /etc/sysconfig/iptables
		add
		-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
		# /etc/rc.d/init.d/iptables restart
3. install EPELリポジトリ

		# yum -y install epel-release
		# vi /etc/yum.repos.d/epel.repo
		# enabled=1 -> enabled=0
4. install Munin

		# yum -y install munin --enablerepo=epel
5. BASIC認証

		# htpasswd -c /etc/munin/munin-htpasswd user
		...
		#
6. turn on

		# service munin-node start

7. test

		view http://192.168.100.12/munin/

+ Client side install

1. install EPELリポジトリ

		# yum -y install epel-release
		# vi /etc/yum.repos.d/epel.repo
		# enabled=1 -> enabled=0
2. install Munin

		# yum -y install munin-node --enablerepo=epel
3. allow access from server

		#vi /etc/munin/munin-node.conf
		allow ^192\.168\.100\.12$
4. open 4949 port

		# vi /etc/sysconfig/iptables
		add
		-A INPUT -m state --state NEW -m tcp -p tcp --dport 4949 -j ACCEPT
		# /etc/rc.d/init.d/iptables restart
5. turn on

		# service munin-node start
6. on server side, set to watch client

		# vi /etc/munin/munin.conf
		[GPU;miro]
		    address 192.168.100.5
		    use_node_name yes
7. restart munin

		#service httpd restart
		#service munin-node restart

参考文献

[Muninのインストールと設定方法](http://changineer.info/server/monitoring/monitoring_munin.html)

[Muninによるサーバ監視システムを構築する](http://success.tracpath.com/blog/2014/02/05/muninによるサーバ監視システムを構築するcentos-6-5/)

[github munin-monitoring/contrib](https://github.com/munin-monitoring/contrib)

[Muninでアラートメールの送信](http://castor.s26.xrea.com/blog/2007/10/19)

[サーバ監視システム Munin を構築してみた](http://qiita.com/murachi1208/items/2d27d386a2891ccf4ed1)
