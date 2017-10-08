---
title: gcc49
tags:
  - centos
  - gcc49
categories:
  - lab
date: 2015-08-28 00:00:00
---

gcc5.2.0の次にCent OS 6のデフォルトのgccも4.9にすることになった．
<!-- more -->
{% vimhl sh %}
yum install scl-utils

wget https://www.softwarecollections.org/en/scls/rhscl/devtoolset-3/epel-6-x86_64/download/rhscl-devtoolset-3-epel-6-x86_64.noarch.rpm

yum install rhscl-devtoolset-3-*.noarch.rpm
//perftools->SystemTap, Valgrind, OProfile, Dyninst
//toolchain->GCC, GDB, binutils, elfutils, dwz, memstomp, strace, ltrace

yum install devtoolset-3-perftools devtoolset-3-toolchain

/etc/profile.d/devtool3.sh
source /opt/rh/devtoolset-3/enable
{% endvimhl %}
