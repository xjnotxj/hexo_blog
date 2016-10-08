---
title: Nginx+CI 出现 404 错误
date: 2016-03-03 17:32:00
tags: php-CodeIgniter
categories: 技术
---

## 背景： 

最近刚学 ci 框架，做了个简单的项目，在本地搭服务器的环境都调通了，但是部署到远程服务器时：

> http://example.com/(index.php)/ 可以访问（为配置的默认 controller-class）
> http://example.com/(index.php)/[controller-class]/[controller-method] 不可以访问（提示 **404** 错误！）

## 原因：

对于 `/index.php/abc` 这种 url，Apache 和 Lighttpd 会按 "**index.php?abc**" 来解释，而 nginx 会认为是请求名字是 **"index.php" 的目录下的 abc 文件**的内容。所以 CI 在 nginx 下不配置 rewrite 是无法运行的，而在 Apache 和 Lighttpd 则正常。
 
## 解决： 
```xml  
server {
        listen 80;
        
        <!- 替换 example -->
        server_name example.com;
        root /data/wwwroot/example/          index index.php index.html index.htm;
        <!- end -->

        location ~* \.(css|js|swf|htm|jpg|png|gif|json|atlas)?$ {
            expires 1d;
            add_header Pragma public;
            add_header Cache-Control "public";
        }
        
        <!- 替换 controller-class -->
        location /controller-class/ {
            if (!-e $request_filename) {
                rewrite ^/controller-class/(.*)$  /controller-class/index.php?q=$uri&$args;
            }
        }
        <!- end -->
 
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                        fastcgi_param  PHP_VALUE        open_basedir=$document_root:/tmp/:/proc/;
            include        fastcgi_params;
        }

    }
 ```
 