---
layout:     post
title:      ES基础
subtitle:   ES基础只是
date:       2017-10-26
author:     lc
header-img: img/post-bg-universe.jpg
catalog:    true
tags:
    - elasticSearch
---

## Es各参数与mysql对应关系
| mysql | Es |
|:----:|:----:|
| database | index |
| table | type |
| row | document |
| field | field |

## Es相关查询操作
- 查看索引：GET _cat/indices?v
- 查看某索引下所有type：GET index/_search 
