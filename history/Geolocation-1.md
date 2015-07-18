# Geolocation和Web Sockets基于NodeJs的实战（1）
=======

 明河在<a href="http://tympanus.net/codrops/" target="_blank">codrops</a>上看到<a href="http://tympanus.net/codrops/2012/10/11/real-time-geolocation-service-with-node-js/" target="_blank">《REAL-TIME GEOLOCATION SERVICE WITH NODE.JS》</a>文章，非常好的一篇实战教程，讲解使用html5的Geolocation和Web Sockets基于NodeJs创建一个本地地理信息的简单应用，demo非常棒，用到的几个热门技术点，实用性颇强，就是教程不够详细，所以明河做下扩写，想借这篇讲解几个热门技术点。 
## 通过这个系列教程你将学到什么？

*   html5的<a href="http://caniuse.com/#search=geolocation" target="_blank">Geolocation</a>的使用
*   html5的<a href="http://caniuse.com/#search=Sockets" target="_blank">Web Sockets</a>的使用
*   NodeJs中<a href="http://socket.io/" target="_blank"> socket.io</a>的使用 
*   <a href="https://github.com/remy/nodemon" target="_blank">nodemon</a>的使用
*   <a href="https://github.com/cloudhead/node-static" target="_blank">node-static</a>的使用
*   <a href="http://leafletjs.com/" target="_blank">leafletjs </a>API的调用 如果亲对上述知识点感兴趣，不可错过这篇教程哦。 

<a href="http://www.36ria.com/5914/map" rel="attachment wp-att-5926"><img src="http://www.36ria.com/wp-content/uploads/2012/12/map.jpg" alt="" title="map" class="alignnone size-full wp-image-5926" /></a> （-\_-!明河住的地方暴露了，O(∩\_∩)O哈！） <ul class="tow-columns clearfix">
  <li class="l">
    <a target="_blank" class="btn-view-demo" href="http://tympanus.net/Tutorials/RealtimeGeolocationNode/">点此查看demo</a>
  </li>
  <li class="l">
  </li>
</ul> 明河木有NodeJs的服务器，demo还是用原文的。源码下载，将在教程完成后放出 

## NodeJs的准备（切糕，少不了神器）

<a href="http://www.36ria.com/5914/1-17" rel="attachment wp-att-5916"><img src="http://www.36ria.com/wp-content/uploads/2012/12/1.png" alt="" title="1" width="396" height="290" class="alignnone size-full wp-image-5916" /></a> 
### nodemon：一个称职的打手

<a href="https://github.com/remy/nodemon" target="_blank">nodemon</a>的作用是监听你的NodeJs的应用，如果文件有任何变化，将自动重启你的NodeJs，有这个“打手”在，“卖糕”省心太多了，不需要人肉重复重启NodeJs服务器。 那么去哪“雇佣”nodemon，当然不是蓝翔技校，直接从NodeJs的军火库（`npm`）拉取就是了。 <pre class='brush: js; '>npm install nodemon -g
</pre>

`-g`拉取为NodeJs的全局模块。 **nodemon这个打手如何使用么？** 创建个**geolocation**目录，然后建个**demo.js**，代码如下： <pre class='brush: js; '>var http = require('http');
var server = http.createServer(function(req,res){
    res.writeHead(200,{'content-Type':'text/plain'});
    res.end('Hello NodeJs');
}).listen(7878);
</pre> 使用命令行工具进入该目录，然后运行： 

<pre class='brush: js; '>nodemon demo.js localhost 7878
</pre>

<a href="http://www.36ria.com/5914/2-16" rel="attachment wp-att-5917"><img src="http://www.36ria.com/wp-content/uploads/2012/12/2.jpg" alt="" title="2" width="580" height="161" class="alignnone size-full wp-image-5917" /></a> 接下来用浏览器打开localhost:7878，就会打印出“Hello NodeJs”。 亲可以试着修改这句文案，刷新下服务器，就会发现文案跟着变了，你无需重启服务器。 
### node-static：寒冰绵掌，化血为冰

<a href="https://github.com/cloudhead/node-static" target="_blank">node-static</a>的作用是静态化NodeJs应用的神器，是个静态文件服务模块，这本篇教程中我们将使用这个模块来渲染html页面。 **node-static的使用** 韦一笑修炼寒冰绵掌以致寒毒入体，成为吸血鬼，node-static的静态化服务倒没有这样的风险，使用也非常的简单。 安装node-static： <pre class='brush: js; '>npm install node-static -g
</pre> 创建个

**static-demo.js**，代码内容如下： <pre class='brush: js; '>var static = require('node-static');
//指向静态文件资源位置
var files = new static.Server('./public');

var http = require('http');
var server = http.createServer(function(req,res){
    //页面交给node-static渲染
    req.addListener('end',function(){
        files.serve(req,res);
    })
}).listen(7878);
</pre> 创建个

**public**目录，在其下建个static-demo.html，内容如下： <pre class='brush: xml; '>



 Hello node-static！


</pre> 启动NodeJs服务器，访问

<a href="http://localhost:7878/static-demo.html" target="_blank">http://localhost:7878/static-demo.html</a>试试。 通过node-static你就可以访问到public目录下js、css文件了。 node-static拥有比较丰富的功能，比如指定错误页面等，明河不再展开论述，详细请看<a href="https://github.com/cloudhead/node-static" target="_blank">文档</a>。 
### socket.io：强悍三轮车，切糕重？表示无压力

<a href="http://socket.io/#home" target="_blank">socket.io</a>是NodeJs非常著名且常用的模块，用于客服端和服务器端的实时通信，留意**实时**二字，这是socket.io存在的最重要理由，web页面是无法持久化连接，这就导致在一些实时性要求高的应用中，比如网页游戏，聊天室，长连接推送，再比如本文中的本地地理信息追踪，当你用手机打开应用并移动时，应用上你的坐标也会跟着移动，这个过程是实时的。 socket.io简化了WebSocket API（关于html5的WebSocket API会在下一篇介绍），socket.io不只是包含了WebSocket方案，还包含了 Flash Socket, AJAX long-polling, AJAX multipart streaming, Forever IFrame, JSONP polling方案，保证兼容所有浏览器和各种设备。 **来看下socket.io的使用** 创建个socket-js，代码如下： <pre class='brush: js; '>var static = require('node-static');
//指向静态文件资源位置
var files = new static.Server('./public');

var http = require('http');
var server = http.createServer(function(req,res){
    //页面交给node-static渲染
    req.addListener('end',function(){
        files.serve(req,res);
    })
});

var io = require('socket.io').listen(server);
io.sockets.on('connection',function(socket){
    socket.emit('news', { hello: 'world' });
    socket.on('minghe event', function (data) {
        console.log(data);
    });
});

server.listen(7878);
</pre>

`io.sockets.on('connection', function(socket) {})`最为重要，初始化来自客户端的连接。 `socket.emit('news', { hello: 'world' });`emit向客户端广播消息。 <codesocket.on('minghe event', function(data, callback) {})></code>监听客户端广播的消息，可以理解为监听自定义事件。 socket.io的消息事件可以自由命名，建议采用`send:coords`这种方式，方式命名冲突，同时提高语义性。 **在public目录下新建个socket-io-demo.html** 代码如下： <pre class='brush: xml; '>



<script>
    var socket = io.connect('http://localhost:7878');
    socket.on('news', function (data) {
        console.log(data);
        socket.emit('minghe event', { my: 'data' });
    });
</script>


</pre> 需要引入sokect.io.js库哦。 启动服务器，打开

<a href="http://localhost:7878/socket-io-demo.html" target="_blank">http://localhost:7878/socket-io-demo.html</a>。 chrome下的请求结果如下： <a href="http://www.36ria.com/5914/3-20" rel="attachment wp-att-5922"><img src="http://www.36ria.com/wp-content/uploads/2012/12/3.jpg" alt="" title="3" width="577" height="223" class="alignnone size-full wp-image-5922" /></a> 使用的是html5的webSocket，可以看下请求头。 <a href="http://www.36ria.com/5914/4-18" rel="attachment wp-att-5923"><img src="http://www.36ria.com/wp-content/uploads/2012/12/4.jpg" alt="" title="4" width="544" height="271" class="alignnone size-full wp-image-5923" /></a> 明显跟http请求不同。 来看下在IE8下发送请求： <a href="http://www.36ria.com/5914/5-16" rel="attachment wp-att-5924"><img src="http://www.36ria.com/wp-content/uploads/2012/12/5.jpg" alt="" title="5" width="400" height="149" class="alignnone size-full wp-image-5924" /></a> 标准的http请求，IE10前的版本都不支持webSocket。 继续看下服务器的log输出情况： <a href="http://www.36ria.com/5914/6-15" rel="attachment wp-att-5925"><img src="http://www.36ria.com/wp-content/uploads/2012/12/6.jpg" alt="" title="6" width="518" height="277" class="alignnone size-full wp-image-5925" /></a> 
## 总结
 
至此NodeJs方面的知识准备完毕，下一篇教程明河将讲解，html5的Geolocation和Web Sockets。

