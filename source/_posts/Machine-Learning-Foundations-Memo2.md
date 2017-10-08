---
title: Machine Learning Foundations Memo2
tags:
  - machine learning
  - python
categories:
  - ml
date: 2016-05-03 00:00:00
---
機械学習の勉強日記 Day2

<!-- more -->
#2day From Learning to Machine Learning

*Give a computer a fish, you feed it for a day, teach it how to fish, you feed it for a lifetime.*

**learning** : acquiring skill : with experience accumulated from observations

observations -> learning -> skill

**skill** <-> improve some performance measure

**machine learning** : improving some performance measure with experience accumulated/computed from data

data -> ML -> improved performance measure

**why** use machine learning??

if need to 'define' trees, human will learn from data and recognize a tree

ML can be easier to build than hand-programmed system

**ML** : an *alternative route* to build complicated systems

when we cannot program the system manually or we cannot 'define the solution' easily

##key essence of machine learning

1. exists **some ‘underlying pattern’** to be learned
2. but **no** programmable *definition*
3. somehow there is **data** about the pattern

##A Possible Solution
1. pattern : rating
2. learning : known rating

##Metaphor Using Credit Approval

$$input : {x} \in {X}(customer application)$$

$$output : y \in Y(good / bad)$$

unknown pattern to be learned <-> **target function**
$$f : X \to Y$$

data <-> training examples : $$D = (x1, y1), (x1,y2)$$

**hypothesis** <-> skill with hopefully good performance:
$$g: X \to Y (learned formula to be used)$$

$$({x}_{n},{y}_{n}) f \to ML \to g$$

$$assume g \in H$$

hypothesis set H can contain good or bad hypotheses
up to A to pick the “best” one as g

----
Machine Learning : use data to compute hypothesis g that approximates target f

Data Mining : use huge data to find property that is interesting

if ‘interesting property’ same as ‘hypothesis that approximate target’ - ML = DM

if ‘interesting property’ related to  ‘hypothesis that approximate target’ - DM can help ML, and vice versa

----
Machine Learning : use data to compute hypothesis g that approximates target f

Artificial Intelligence : compute something that shows intelligent behavior

ML can realize AI, among other routes

----
Machine Learning : use data to compute hypothesis g that approximates target f

Statistics : use data to make inference about an unkonow process

Statistics can be used to achieve ML
