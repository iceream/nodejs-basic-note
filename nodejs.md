# NodeJS-基础

[toc]

## 初识nodejs

* 前端最新主流JavaScript运行环境
  1. Node.js是一个基于Chrome V8引擎的JavaScript运行环境。
  2. Node.js使用了一个事件驱动、非阻塞式I/O的模型，使其轻量又高效。
  3. Node.js的包管理器npm,是全球最大的开源库生态系统。

* NodeJS为什么这么火
  1. 使用JavaScript
  2. 速度非常快（V8引擎 & non-block）
  3. Node.js的包管理器npm,是全球最大的开源库生态系统。

* 将要学到的知识点
  1. NodeJS的工作原理，如V8引擎/模块/事件队列/文件系统
  2. 搭建服务器（Express/路由/Template模板）
  3. 实战项目（TodoApp）
  
* 需要的知识
  1. HTML + CSS
  2. JavaScript
  3. Command Line
  
  ```js
  console.log("Hello");
  //全局对象
  //定时器
  setTimeout(function(){
      console.log('3 seconds have passed.');
  },3000)
  
  var time = 0;
  var timer = setInterval(function () {
      time += 2;
      console.log(time + ' seconds hava passed');
      if(time > 4)
          clearInterval(timer);
  },2000)
  //路径
  console.log(__dirname);     // 不包含文件名的路径
  console.log(__filename);    // 包含文件名的路径
  
  //global 全局对象，在NodeJS中全局对象是global而不是Window
  console.log(global);
  ```

## V8引擎的原理 

* JavaScript引擎（Engines）
  1. 电脑根本不认识也不理解JavaScript。
  2. JavaScript引擎起到的作用就是让电脑识别JS代码。
  3. 浏览器自带了JS引擎。
* V8引擎
  1. Node.js是使用C++写的
  2. V8引擎是node.js的核心，也是用C++写的
  3. V8引擎的作用就是让JS代码能够让电脑识别

## Module模块和Require引入的使用

[Module](http://nodejs.cn/api/modules.html 'Module')

在Node.js中，文件和模块是一一对应的（每个文件被视为一个模块）

```js
// app.js部分
// require 引入模块
var stuff = require('./stuff');
console.log(stuff.counter(['Herry','Bucky','Emily']));
console.log(stuff.adder(4,stuff.pi));
```

```js
// stuff.js部分
var counter = function (arr){
    return '一共有'+ arr.length + '个元素。';
}

var adder = function(a,b){
    return `你需要计算的两个值和为：${a+b}`;
}

var pi = 3.142
// module,公开属性和方法
// 第一种方法
/*
module.exports.counter = counter;
module.exports.adder = adder;
module.exports.pi = pi;
*/
// 第二种方法
module.exports = {
    counter:counter,
    adder:adder,
    pi:pi
}

// 问题：JS中的数字精度丢失问题 !important
```

## 事件模块

[Events](http://nodejs.cn/api/events.html '事件触发器http://nodejs.cn/api/events.html')

1. 大多数Node.js核心API都是采用惯用的异步事件驱动架构（fs/http）
2. 所有能触发事件的对象都是EventEmitter类的实例
3. 事件流程：引入模块->创建EventEmitter对象->注册事件->触发事件

```js
// 事件模块

// 1.引入事件模块
var events = require('events');

// 2.创建EventEmitter对象
var myEmitter = new events.EventEmitter;

// 注册事件
myEmitter.on('someEvent',function(msg){
    // 使用setImmediate()方法切换到异步的操作模式 ！important
    setImmediate(function(){
        console.log(msg);
    })
})

// 触发事件
myEmitter.emit('someEvent','实现事件并传递此参数到注册事件的回调函数中');

console.log('1');
```

## 文件系统模块

[fs](http://nodejs.cn/api/fs.html '文件系统http://nodejs.cn/api/fs.html')

文件系统主要对项目中的文件进行操作

1. 读取文件（fs.readFile）
2. 写入文件（fs.writeFile）
3. 流程：引入fs模块->调用方法->异常捕获

```js
// 文件系统
// 1.引入文件系统
var fs = require('fs');

// 2.通过对象调用方法
// 同步读取文件
var readMe = fs.readFileSync('readMe.txt','utf8');
console.log(readMe);
// 同步写入文件
fs.writeFileSync('writeMe.txt',readMe);

// 异步读取文件
fs.readFile('readMe.txt','utf8',function (err,data) {
    if (err) throw err;
    console.log(data);
})

// 异步写入文件

fs.readFile('readMe.txt','utf8',function (err,data) {
    if (err) throw err;
    fs.writeFile('writeMe2.txt',data,function(err){
        if (err) throw err;
    });
})
```

## 文件系统操作

1. 创建文件夹（fs.mkdir）
2. 删除文件夹（fs.rmdir）
3. 删除文件（fs.unlink）
4. 流程：引入fs模块->调用方法->异常捕获

```js
// 1.引入文件系统模块
var fs = require('fs');

// 2.使用模块对象调用方法
// 删除文件
fs.unlink('writeMe2.txt',function(err){
    if(err)
        console.log('文件不存在!' + '\n' + err);
    else
        consloe.log('文件删除成功！');
})

// 创建文件夹 同步
fs.mkdirSync('stuff');

// 删除文件夹 同步
fs.rmdirSync('stuff');

// 异步创建和删除文件夹
fs.mkdir('stuff',function(){
    fs.readFile('readMe.txt','utf8',function(err,data){
        if (err) throw err;
        fs.writeFile('./stuff/writeMe.txt',data,function (err) {
            if (err) throw err;
        });
    });
});

// 异步删除文件夹
// 1.先删除文件夹中的文件 2.再删除外部的文件夹
fs.unlink('./stuff/writeMe.txt',function () {
    fs.rmdir('stuff',function (err) {
        if (err) throw err;
        console.log('文件删除成功！');
     })
})
```

## Http创建服务器

[http](http://nodejs.cn/api/http.html 'http , http://nodejs.cn/api/http.html')

客户端和服务器的关系 Client & Server

```js
// 通过http模块，创建本地服务器

var http = require('http');

// 创建服务器方法
var server = http.createServer(function(req,res){
    console.log('客户端向服务器发送请求：' + req.url);
    res.writeHead(200,{"Content-type":"text/plain"});
    res.end("Server is working!");
});

// 服务对象监听服务器地址及端口号
server.listen(8888,"127.0.0.1");
console.log('server is running...');
```

## 缓存区及流 Buffer & Stream

[Buffer](http://nodejs.cn/api/buffer.html)，缓存区，可以在TCP流和文件系统操作等场景中处理二进制数据流

[stream](http://nodejs.cn/api/stream.html#stream_stream)，流，在Node.js中是处理流数据的抽象接口

[Node.js 中的缓冲区（Buffer）究竟是什么？](https://juejin.im/post/5d3a3b8ff265da1b8d166323)

```js
// Buffer 类在全局作用域中，因此无需使用 require('buffer').Buffer。
// 引入流模块
const stream = require('stream');
```

## 读写数据流

```js
var http = require('http');
var fs = require('fs');

// 创建服务器
var server = http.createServer(function (req,res) {
    res.writeHead(200,{"Content-type":"text/plain"});
    var myReadStream = fs.createReadStream(__dirname + '/readMe.txt','utf8');
    myReadStream.pipe(res);
})

server.listen(3000,'127.0.0.1');
console.log('Server is running...');
```

## 读取HTMl和JSON数据

```js
var http = require('http');
var fs = require('fs');

// 创建服务器
var server = http.createServer(function (req,res) {
    // 读取HTML
    res.writeHead(200,{"Content-type":"text/html"});
    // 读取JSON
    // res.writeHead(200,{"Content-type":"application/json"});
    console.log('客户端向服务器发送请求：' + req.url);
    var myReadStream = fs.createReadStream(__dirname + '/index.html','utf8');
    myReadStream.pipe(res);
})

server.listen(3000,'127.0.0.1');
console.log('Server is running...');
```

# 数据和框架

## Route 路由

```js
var http = require('http');
var fs = require('fs');

// 创建服务器
var server = http.createServer(function (req,res) {
    if (req.url === '/home' || req.url === '/'){
        res.writeHead(200,{"Content-type":"text/html"});
        fs.createReadStream(__dirname + '/index.html','utf8').pipe(res);
    }else if (req.url === '/contact'){
        res.writeHead(200,{"Content-type":"text/html"});
        fs.createReadStream(__dirname + '/contact.html').pipe(res);
    }else if (req.url === '/api/docs' || req.url === '/docs') {
        res.writeHead(200,{"Content-type":"text/html"});
        fs.createReadStream(__dirname + '/api/docs.html').pipe(res);
    }
});

server.listen(3000,'127.0.0.1');
console.log('Server is running...');
```

## NPM

[npm官网](https://www.npmjs.com/)

Node Package Manager

NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题

* 什么是Package

  package用于定义项目中所需要的各种模块，以及项目的配置信息（比如、版本、许可证等元数据）

  安装第三方库之前首先要安装这个东西

  ```js
  // 安装package.json
  $ npm init
  // 需要把所安装的模块记录到package.json中则需要在安装模块时加上--save
  // $ npm install jquery --save 
  // 开发者方式则是
  // $ npm install jquery --save-dev
  ```

* 如何使用NPM

  1. 安装模块

     ```js
     $ npm install <Module Name>
     // 示例
     $ npm install jquery
     ```

  2. 卸载模块

     ```js
     $ npm uninstall <Module Name>
     // 示例
     $ npm uninstall jquery
     ```

##  初识Express框架及nodemon

[Express官网](https://expressjs.com/zh-cn/)

* Express框架

  1. 基于Node.js平台，快速、开放、极简的web开发框架。

* 安装Express

  ```js
  $ npm install express --save
  ```

* [nodemon]()
  
1. 在开发环境下，往往需要一个工具来自动重启项目工程，我们可以借助nodemon带代替node进行启动
  
* 安装nodemon

  ```js
  // 全局安装
  $ npm install -g nodemon
  // 本地安装
  $ npm install --save-dev nodemon
  ```

* 使用nodemon

  ```js
  nodemon 项目名
  // 如
  nodemon app
  ```


## Express框架

1. 已经封装好了服务器
2. 封装好了路由
3. 中间件
4. 还有网络请求

* 使用方法

  1. npm安装Express框架
  2. 引入Express模块
  3. 实例化Express的对象
  4. 通过对象进行调用各种方法：`get()`方法
5. 获取路由：`req.params.id`
  
  ```js
  // 引入Express模块
  var express= require('express');
  
  // 实例化express对象
  var app = express();
  
  // 通过对象调用对应的方法
  // 根据用户请求的地址，返回对应的数据信息
  app.get('/',function(req,res){
      console.log(req.url);
      res.send('This is home page!');
  });
  app.get('/contact',function(req,res){
      console.log(req.url);
     res.send('This is contact page!');
  });
  // 路由参数 req.params.id
  app.get('/profile/:id',function(req,res){
      res.send('您所访问的路径参数为：' + req.params.id); // 获取路由的方法
  });
  
  // 监听服务器端口号
  app.listen(8888);
  
  ```

## EJS模板引擎1

[EJS](https://ejs.co/)

* 使用方法
  1. 引入Express模块
  2. 实例化express对象
  3. 配置视图引擎
  4. 使用`res.sendFile()`返回页面
  5. 使用`res.render('EJS文件名',变量)`使用EJS
  6. 在ejs文件中中使用 `<%= 变量%>` 输出

```js
// 安装
$ npm install ejs --sava-dev
```

```js
// 目录结构
views
	--peofile.ejs
app.js
```

```js
// app.js
// 引入Express模块
var express= require('express');

// 实例化express对象
var app = express();
// 配置视图引擎
app.set('view engine','ejs');
// 通过对象调用对应的方法
// 根据用户请求的地址，返回对应的数据信息
app.get('/',function(req,res){
    console.log(req.url);
    res.sendFile(__dirname + '/index.html');
});
app.get('/contact',function(req,res){
    console.log(req.url);
   res.sendFile(__dirname + '/contact.html');
});
// 路由参数 req.params.id
app.get('/profile/:id',function(req,res){
    res.render('profile',{name:"a Page"});
});

// 监听服务器端口号
app.listen(8888);
```

```html
<!-- profile.ejs -->
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
	<h1>Welcome to <%=name%> </h1>
	<p>This is the best website.</p>
</body>
</html>
```

## EJS模板引擎2

```js
// app.js
// 引入Express模块
var express= require('express');

// 实例化express对象
var app = express();
// 配置视图引擎
app.set('view engine','ejs');
// 通过对象调用对应的方法
// 根据用户请求的地址，返回对应的数据信息
app.get('/',function(req,res){
    console.log(req.url);
    res.sendFile(__dirname + '/index.html');
});
app.get('/contact',function(req,res){
    console.log(req.url);
   res.sendFile(__dirname + '/contact.html');
});
// 路由参数 req.params.id
app.get('/profile/:id',function(req,res){
    var data = [{age:29,name:["Henry","Emily"]},{age:30,name:["Bucky","Elyse","John"]}];
    res.render('profile',{websiteName:req.params.id,data:data});
});

// 监听服务器端口号
app.listen(8888);

```

```html
<!-- profile.ejs -->
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1>Welcome to <%= websiteName%> </h1>
    <h2>个人介绍</h2>
    <ul>
        <% data.forEach(function (item) { %>
                <li><%= item.age %></li>
                <% item.name.forEach(function(item){ %>
                    <li><%= item %></li>
                <% }) %>
        <% }) %>
    </ul>
</body>
</html>
```

## 公共模板-Don't repeat yourself！

* 实施计划
  1. 使用EJS替代HTMl
  2. 创建导航（公共模板）
  3. 解决外部样式不可用的问题

```js
// 目录结构
public
	--nav.ejs
views
	--index.ejs
	--contact.ejs
app.js
```

```js
// app.js
// 解决外部样式不可用的问题
// 引入Express模块
var express= require('express');

// 实例化express对象
var app = express();
// 配置视图引擎
app.set('view engine','ejs');

// 让服务器识别外部样式表
app.use('/assets',express.static('assets'))
// 通过对象调用对应的方法
// 根据用户请求的地址，返回对应的数据信息

app.get('/',function(req,res){

    res.render('index');
});
app.get('/contact',function(req,res){
   res.render('contact');
});
// 路由参数 req.params.id
app.get('/profile/:id',function(req,res){
    var data = [{age:29,name:["Henry","Emily"]},{age:30,name:["Bucky","Elyse","John"]}];
    res.render('profile',{websiteName:req.params.id,data:data});
});

// 监听服务器端口号
app.listen(8888);

```

```ejs
<!-- nav.ejs -->
<div id="nav"> <!--只能有一个根标签-->
    <ul>
        <li><a href="/" class="href">主页</a></li>
        <li><a href="/contact" class="href">联系我们</a></li>
    </ul>
</div>
```

```ejs
<!-- index.ejs -->
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="../assets/style.css">
</head>
<body>
    <%- include('../public/nav'); %>
    <h1>欢迎来到主页！</h1>
    <p>This is the best website.</p>
</body>
</html>
```

```ejs
<!--contact.ejs-->
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="../assets/style.css">
</head>
<body>
    <%- include('../public/nav'); %>
    <h1>Welcome to 联系我们页面！</h1>
    <p>电话：40088888888</p>
</body>
</html>
```

