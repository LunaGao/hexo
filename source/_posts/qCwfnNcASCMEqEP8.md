---
title: How to setup Hexo And Winwin Hexo Editor
date: 2020-09-01 00:19:17
tags:
---
# 环境
Ubuntu 18.04 (Aliyun)

# Install Hexo
https://hexo.io/  跟着官网一步一步安装

# Install Winwin Hexo Editor
https://gitee.com/winwin_2011/winwin-hexo-editor   跟着官网一步一步做

# Install Nginx
https://www.nginx.com/resources/wiki/start/topics/tutorials/install/   跟着官网一步一步做

# Start up Winwin Hexo Editor
启动Winwin Hexo Editor, 记得使用pm2方式
默认端口为5777，用Nginx反向代理来进行外网访问

# SSL
https://github.com/acmesh-official/acme.sh 仔细阅读

配置这些之前，请先可以配置nginx，使其在网络中可以访问。

### winwin hexo editor
`acme.sh --issue --nginx -d xxxx.com`

### hexo
`acme.sh --issue -d www.maomishen.com -w /var/www/blog/public`

# Setup Nginx
发布Hexo
```
server {
	listen 80;
	listen [::]:80;
	server_name www.maomishen.com;
	rewrite ^(.*) https://$host$1 permanent;
}
server {
	listen 443 ssl;
	listen [::]:443 ssl;
	server_name www.maomishen.com;

	ssl on;
	ssl_certificate /root/.acme.sh/www.maomishen.com/www.maomishen.com.cer;
	ssl_certificate_key /root/.acme.sh/www.maomishen.com/www.maomishen.com.key;
	ssl_session_timeout 5m;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
	ssl_prefer_server_ciphers on;

	root /var/www/blog/public;

	index index.html;

	location / {
		try_files $uri $uri/ =404;
	}
}

```
发布Winwin Hexo Editor
```
server {
	listen 80;
	listen [::]:80;
	server_name xxx.com;
	rewrite ^(.*) https://$host$1 permanent;
}
server {
	listen 443 ssl;
	listen [::]:443 ssl;
	server_name xxx.com;

	ssl on;
	ssl_certificate /root/.acme.sh/xxx.com/xxx.com.cer;
	ssl_certificate_key /root/.acme.sh/xxx.com/xxx.com.key;
	ssl_session_timeout 5m;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
	ssl_prefer_server_ciphers on;

	location / {
		proxy_pass http://localhost:5777;
	}
}

```