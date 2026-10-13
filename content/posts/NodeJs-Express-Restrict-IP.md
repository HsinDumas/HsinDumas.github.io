---
title: NodeJs Express Restrict IP
date: 2018-09-13 18:06:08
categories:
- JS
tags: 
- NodeJs
- Express
---

## 限制请求IP地址

```js
var ip = req.headers['x-forwarded-for'] 
		|| req.connection.remoteAddress 
		|| req.socket.remoteAddress 
		|| req.connection.socket.remoteAddress;
// ::ffff:192.168.1.31
ip.match(/\d+.\d+.\d+.\d+/)[0].startsWith("192.168.")

/*
req.headers['x-forwarded-for']  判断是否有反向代理IP(头信息：x-forwarded-for)
req.connection.remoteAddress    判断connection的远程IP
req.socket.remoteAddress        判断后端的socket的IP
*/
```