---
title: Machine Learning Foundations Memo3
tags:
  - machine learning
  - python
categories:
  - ml
date: 2016-05-08 00:00:00
---

機械学習の勉強日記 Day3

<!-- more -->

##Learning with Different Output Space
yes or no -> **binary classification**

Multiclass Classification : Coin Recognition Problem

- 1c, 5c, 10c, 25c
  - by(size, mass)

$Y={1,2,...,k}$(abstractly), binary classification : special case with $k=2$

**regression Problem**

**supervised learning** : all $y_{n}$

**unsupervised learning** : no $y_{n}$ -> **clustering**

**semi-supervised learning** : some $y_{n}$

**reinforcement learning** : implicit $y_{n}$ by goodness

**batch** supervised Multiclass classification : learn from all knonwn data **duck feeding-all knonwn data**

**onlie** hypothesis 'improves' through receiving data instances **passive sequential data**

**active learning** improve hypothesis with fewer labels (hopefully) by asking questions **strategically-observed data**

**concrete** features(input) : often including 'human itelligence' on the learning task

**raw** features : 'simple physical meaning' : thus more difficult for ML than concrete features
 often need human or machines to **convert to concrete ones**
