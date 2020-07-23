---
layout: nvm
title: nvm安装node关闭终端失效踩坑记
date: 2020-07-23 14:16:28
categories: 前端
tags: 
    - 前端
---

## 序
这篇文章用来解决解决 `nvm is not compatible with the npm config "prefix"`,在使用`nvm install` 指定版本`node`的时候安装好后，关闭终端重启终端失效的问题。这个问题困扰很久,最终得到解决，遂记录解决方案，不再踩坑。

## 使用nvm安装指定版本的node

```javascript
nvm install v12.18.2          //安装指定版本的node
nvm use v12.18.2
nvm alias default v12.18.2    //把 node v12.18.2 变成默认版本

```

## Mac系统每次打开终端都会提示下面这段话

```javascript
nvm is not compatible with the npm config "prefix" option: currently set to "/usr/local" 
Run npm config delete prefix or nvm use --delete-prefix v12.18.2 --silent to unset it.

```

## 现状问题
终端运行 `nvm use --delete-prefix v12.18.2 --silent` 可以解决，但是重新打开终端之后还是会有问题。

## 解决

1. 删除并重新设置 `nvm prefix`

2. 运行 `nvm use --delete-prefix v12.18.2 --silent` 确保npm命令可以使用；

3. 运行 `npm config delete prefix` 删除 `nvm prefix`；

4. 运行 `npm config set prefix $NVM_DIR/versions/node/v12.18.2` 设置 `nvm prefix` 为v12.18.2。


ps: 注意：更改版本号，用错误消息中的提示的版本号。




