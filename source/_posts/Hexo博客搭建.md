---
title: First blog
date: 2024-04-19 11:01:00
tags: [环境配置/搭建]
---

# Hexo博客搭建

## hexo安装

`npm install hexo -g`

## 初始化博客

`hexo init [文件夹名字]`

## 安装依赖

`npm install`

## 本地预览

`hexo serve`

`hexo n + 文件名`创建博客文章

进行做之后都需要重新生成blog`hexo g`,因为是静态文件，不重新生成是不会有任何改变的

`hexo s`本地预览指令

`npm install hexo-server --save`安装依赖

`hexo clean`清理之前上传的缓存，再上传

`hexo d`上传到仓库命令

`hexo g`生成文件

顺序：clean-> 生成->预览->上传


## 如果blog整个文件丢失解决办法

1.`git clone + 仓库地址`

![image-20240419210024323](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240419210024323.png)

2.安装依赖 `npm install`

3.运行命令生成`hexo g`

4.本地预览`hexo s`

5.完成恢复

## [小林小林爱代码 - 记录分享学习的点滴 (linff214.github.io)](https://linff214.github.io/)

## 推备份仓库命令

### ...or create a new repository on the command line

```
echo "# blog" >> README.md
git init
git add README.md
git add .
git commit -m"first commit(改成自己的仓库文件夹)"//提交到了本地
git branch -M main//推上远程
git remote add origin https://github.com/constown/blog.git
git push -u origin main
```

### ..or push an existing repository from the command line
```
git remote add origin https://github.com/constown/blog.git`
git branch -M main
git push -u origin main
```

`git push -u origin main`

## Gulp压缩

1.安装Gulp的cli

`npm install --global gulp-cli`

2.安装gulp工具

`npm install gulp -g`

3.压缩HTML

`npm install gulp-htmlclean --save-dev`

`npm install --save gulp-html-minifier-terser`

4.压缩CSS

`npm install gulp-clean-css --save-dev`

5.

`npm install --save-dev gulp-uglify`

`npm install --save-dev gulp-babel @babel/core @babel/preset-env`

6.压缩图片

`npm install --save-dev gulp-imagemin`

7.创建gulpfile文件

图标库——fontawesome

PICGO的token —— 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

自定义域名——https://cdn.jsdelivr.net/gh/Linff214/picodemo