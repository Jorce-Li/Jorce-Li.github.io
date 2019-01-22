---
layout: post
title: "本地https开发"
subtitle: 'http-https'
author: "Jorce"
header-style: text
tags:
  - https
  - 聊天室
---

Update: 还是在做聊天室的部分遇到的问题记录一下

---

# 本地前端起https服务

### vue-cli 3.0

在vue.config.js文件的devServer中添加如下代码
```js
devServer:{
  host:'192.168.1.xxx',//局域网IP
  https:{
    key: fs.readFileSync(path.join(__dirname, './cert/192.168.1.xxx-key.pem')),//
    cert: fs.readFileSync(path.join(__dirname, './cert/192.168.1.xxx.pem'))//
  }
}
//The certificate is at ".pem" and the key at "-key.pem" 
```
然后再用serve启动即可。
其中的key 和 cert 则需要使用mkcert或其他工具进行生成

#### [mkcert](https://github.com/FiloSottile/mkcert)

使用choco进行安装 安装完成之后
```js
mkcert -install
mkcert 192.168.1.xxx
```
即可生成上述两个文件

# 本地Node起https服务
  
同上述步骤使用mkcert生成证书之后
```js
var privateKey  = fs.readFileSync('./192.168.1.xxx-key.pem', 'utf8');
// var certificate = fs.readFileSync('./file.crt', 'utf8');
var certificate = fs.readFileSync('./192.168.1.xxx.pem', 'utf8');
var credentials = {key: privateKey, cert: certificate};

const server = require('https').createServer(credentials,app);
```  
启动即可

