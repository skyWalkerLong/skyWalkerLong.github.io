---
layout:     post
title:      git命令
subtitle:   git快速上手
date:       2017-10-26
author:     lc
header-img: img/post-bg-universe.jpg
catalog:    true
tags:
    - tools
---

#### git提交相关命令 ####
1. cd进入项目根目录
2. 建立.git文件夹  
`git init`
3. 将项目的所有文件添加进仓库  
`git add .`  
*若添加特定文件，将.换成文件名即可*  
4. 代码提交到本地head  
`git commit -m "代码提交信息"`
5. 在github网站上创建repository，复制http地址
6. 本地仓库关联http地址  
`git remote add origin http地址`
7. 上传前pull  
`git pull origin master`
8. 上传  
`git push -u origin master`

#### 注： ####
> - 若第7步失败，直接进行第8步  
- 上传过程中输用户名和密码时输入github的用户名和密码
- 若需要为单个项目配置用户名和email，操作如下：  
`git config user.name "用户名"`  
`git config user.email "email"`
