---
layout:     post
title:      supervisor 监控nginx 一直在重启的问题
subtitle:   "supervisor 监控nginx 一直在重启的问题"
date:       2015-10-28 12:00:00
author:     "Dan"
header-img: "img/home-bg-o.jpg"
tags:
    - supervisor
    - 后端开发
    - nginx
    - python
---


>下滑这里查看更多内容

# supervisor 监控nginx 一直在重启的问题
supervisor 监控nginx ，写好配置文件之后，发现一直在重启，排查之后发现是命令不对：

command = /usr/local/bin/nginx 这个命令默认是后台启动，但是supervisor不能监控后台程序，所以supervisor就一直执行这个命令。

加上-g 'daemon off;'这个参数可解决这问题，这个参数的意思是在前台运行。

command = /usr/local/bin/nginx  -g 'daemon off;'

完整的supervisor 监控nginx 配置如下：
```
[program:nginx]
 
command = /usr/local/bin/nginx  -g 'daemon off;'
 
stdout_logfile=/Users/ddios/nginx_stdout.log
 
stdout_logfile_maxbytes=10MB
 
stderr_logfile=/Users/ddios/nginx_stderr.log
 
user = ddios
 
stderr_logfile_maxbytes=10MB
 
autostart=true
 
autorestart=true
```

