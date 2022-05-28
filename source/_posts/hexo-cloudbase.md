---
title: hexo_cloudbase
date: 2022-05-28 23:21:20
categories: Hexo
---
参考[博客](https://cloud.tencent.com/developer/article/1812818)  
```
tcb login   //登录Cloudbase
cd public   
tcb hosting delete -e envId //删除里面的内容
tcb hosting deploy -e $envId    //推送
```