---
title: gprof google-perftools
tags:
  - gprof
categories:
  - lab
date: 2014-12-12 00:00:00
---

google-perftools　高性能ですが，結果の読み方イマイチわからない．．．
<!-- more -->
{% vimhl bash %}
1. link -L/usr/local/lib/ -lprofiler
2. export CPUPROFILE=prof.out
3. ./a.out
    -> PROFILE: interrupts/evictions/bytes = 95/1/1608
4. pprof ./a.out prof.out
    -> top
5. pprof --dot ./a.out prof.out > prof.dot
  dot -T png prof.dot > prof.png
6. pprof --text ./a.out prof.out
{% endvimhl %}

gprofはGNUのものですが，Macには対応してない．．．残念
{% vimhl bash %}
    #!bash
    1. link -pg
    2. ./a.out
        -> gmon.out
    3. gprof a.out gmon.out
{% endvimhl %}

結果の読み方については

Flat profile: selfは関数消費時間　cumulativeは累計時間
Call graph: 関数の呼び出し関係
さらに，gprof を実行するとき -Aのオプションをつけるとソースコードのどの行が何回実行されたかがわかる．

[gprof](http://minus9d.hatenablog.com/entry/20140112/1389502918 "gprof")
[google-perftools](http://homepage2.nifty.com/takaaki024/tips/programs/c/c-linux.html "google-perftools")
