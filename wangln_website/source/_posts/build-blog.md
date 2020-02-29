---
title: 从零搭建一个博客
categories: 笔记
tags: 笔记
date: 2019-09-05 14:36:21
---
## 前言
搭建博客前，首先要知道几个配置文件和做好准备工作

## 准备工作
* 下载nodejs,npm并且安装
* 下载git并且安装
* 必须有一个github账号,没有去注册一个

## 目录结构
``` bash
node_modules：是依赖包
public：存放的是生成的页面
scaffolds：命令生成文章等的模板
source：用命令创建的各种文章
themes：主题
_config.yml：整个博客的配置
db.json：source解析所得到的
package.json：项目所需模块项目的配置信息
```

## 快速开始

### 新建一篇文章—(在./source目录执行)

``` bash
$ hexo new "build-blog"
```
## github创建项目 
在github上创建项目project,命名格式必须为:用户名.github.io(eg:acmwln.github.io)

## 安装Hexo基本框架和Hexo基本命令

```bash
mkdir wangln_website #创建文件夹 比如我的叫wangln_website
npm install -g hexo #全局安装hexo
cd wangln_website
hexo init #初始化hexo框架
hexo server #启动本地服务,默认端口是4000
hexo clean #清理缓存文件
hexo generate  #编译生成静态页面 简写hexo g
hexo publish #发布草稿
hexo delopy #发布静态文件 简写hexo d
```

访问 localhost:4000 就可以看到默认的页面

## 博客关联到Github仓库

修改wangln_website下面的_config.yml 

```bash
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/用户名/用户名.github.io.git  ##eg.https://github.com/acmwln/acmwln.github.io.git
  branch: master
  message: blog

```

## 安装 hexo-deployer-git
```bash
npm install hexo-deployer-git --save #安装 hexo-deployer-git,快速把代码托管到 GitHub 上
hexo clean #清理缓存文件
hexo generate  #编译生成静态页面 简写hexo g
hexo delopy #自动提交,将 hexo 项目托管到 GitHub 上,简写 hexo d

```

打开 https://用户名.github.io就可以了(eg:https://acmwln.github.io)



