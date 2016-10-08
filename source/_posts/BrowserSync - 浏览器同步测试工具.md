---
title: BrowserSync - 浏览器同步测试工具
date: 2016-06-01 22:46:00
tags: 前端
---
  
## 背景：
之前在学 gulp 的时候，使用 `gulp-livereload` 来实时自动刷新页面省时开发，但一直比较难用，现在找到新的替代神器。

## 安装：

```php
// 使用淘宝镜像会快些
npm install -g browser-sync --registry=https://registry.npm.taobao.org 
```

## 启动：

#### （1）静态网页（服务器模式）

 先进入项目目录，然后：
 
 ```php
 // --files 路径是相对于运行该命令的项目（目录） 
 browser-sync start --server --files "css/*.css, *.html"
 // 如果你的文件层级比较深，您可以考虑使用 **（表示任意目录）匹配，任意目录下任意.css 或 .html文件。 
 browser-sync start --server --files "**/*.css, **/*.html"
 // --监听整个项目
 browser-sync start --server --files "**" 
 ```
#### （2）动态网页（代理模式）

```php
// 主机名可以是ip或域名
browser-sync start --proxy "主机名" "css/*.css"
```

---

#### Reference：

[ Browsersync 中文网 - 省时的浏览器同步测试工具](http://www.browsersync.cn/) 
 
