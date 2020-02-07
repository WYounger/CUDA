```bash
#准备安装git和node.js
#创建一个远程仓库 userName.github.io
# https://github.com/WYounger/WYounger.github.io.git
#搭建本地hexo
npm install -g hexo-cli
cd /blog
hexo init 
npm install

# 安装 hexo-deployer-git 自动部署发布工具
npm install hexo-deployer-git  --save

#常用命令
hexo g #生成
hexo d #部署

#更换主题
git clone https://github.com/iissnan/hexo-theme-grace themes/hexo-theme-grace
#配置_config.yml下的theme
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: hexo-theme-grace 

#写文章
#目录 /blog/source/_posts
#格式要求
---
title:blogTitle
---

#发布
#_config.yml中配置github
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/yuorname/yuorname.git  //您已经创建好的仓库
  branch: master
  
#设置分类
cd /blog
hexo new page categories
#source/categories/index.md
---
title: 文章分类
date: 2017-05-27 13:47:40
type: "categories"  #添加
---
#给文章添加categories,一个page只能属于一个categories
---
title: title
date: date
categories: #添加
- test
---

#设置tags
cd /blog
hexo new page tags
#source/tags/index.md
---
title: 文章分类
date: date
type: "tags" #添加
---
#给文章添加tags,一个page可以有多个tag
---
title: title
date: 2017-05-26 12:12:57
categories: 
- web前端
tags:
- jQuery
- 表格
- 表单验证
---


#添加搜索框 LocalSearch

```



```bash
#创建_posts/dir1/fileName.md，新文件相对路径是_posts文件夹
hexo new -p ./dir1/fileName title  #title必填
```

