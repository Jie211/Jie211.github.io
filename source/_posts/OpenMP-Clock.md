---
title: OpenMP Clock
tags:
  - openmp
  - clock
categories:
  - lab
date: 2014-12-05 00:00:00
---

clock関数で落ちた．．．
<!-- more -->

clock()を使って実行時間を測ったら完全にだめだ．．．gettimeofday()とomp_get_wtime()じゃないと正しい結果はでない．．．このミスで一日無駄になった．．．俺バカだな．．．
