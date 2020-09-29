---
title: gcc48_on_CentOS
tags:
  - centos
  - gcc
categories:
  - lab
date: 2014-11-12 00:00:00
---

CentOS 6.5に入ってるGCCは4.4.7の古いバージョンなので，新しいやつを入れます．
<!-- more -->
{% vimhl bash %}
root ~ # cat /etc/redhat-release
CentOS release 6.5 (Final)
root ~ # gcc --version
gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-11)
Copyright (C) 2010 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.))))
{% endvimhl %}
RedHat Developer Toolset v2.0のレポジトリをyumに入れてインストール

{% vimhl bash %}
root ~ # wget http://people.centos.org/tru/devtools-2/devtools-2.repo -O /etc/yum.repos.d/devtools-2.repo
root ~ # yum install devtoolset-2-gcc devtoolset-2-binutils
root ~ # yum install devtoolset-2-gcc-c++ devtoolset-2-gcc-gfortran
{% endvimhl %}

リンクを貼る

{% vimhl bash %}
ln -s /opt/rh/devtoolset-2/root/usr/bin/gcc /usr/local/bin/gcc48
ln -s /opt/rh/devtoolset-2/root/usr/bin/g++ /usr/local/bin/g++
{% endvimhl %}
以上．
