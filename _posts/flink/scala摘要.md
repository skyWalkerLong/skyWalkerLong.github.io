---
layout:     post
title:      scala
subtitle:   scala语法
date:       2020-09-09
author:     lc
header-img: img/post-bg-universe.jpg
catalog:    true
tags:
    - 实时计算
---
### 前言
本篇文章主要记录一些scala的语法
### 持续更新
- 循环10次并创建一个包含10个健值对的map
```
    var curFTemp = (1 to 10).map {
      i => ("sensor_" + (taskIdx * 10 + i), 65 + (rand.nextGaussian() * 20))
    }
```
