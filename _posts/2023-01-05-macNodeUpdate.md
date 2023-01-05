---
layout: post
title: mac如何升级node版本
category: root
summary: '在使用vue3\vite时，node需要在16版本或者更高版本'
author: hjy
---

# 一、在这里我用的Node.js的多版本管理器n来升级的

* 1、sudo npm cache clean -f //清除nodejs的cache
* 2、sudo npm install -g n //使用npm安装n模块
* 3、npm view node versions // node所有版本
* 4、sudo n latest // 升级到最新版本
  
    sudo n stable // 升级到稳定版本
    
    sudo n xx.xx // 升级到具体版本号
* 5、node -v查看一下

end