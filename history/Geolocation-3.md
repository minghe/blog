# Geolocation和Web Sockets基于NodeJs的实战（3）
=======

[第一篇教程][1]明河简单讲解了[socket.io][2]的用法，socket.io集成了多种通信方案： WebSocket、Adobe Flash Socket、AJAX long polling、AJAX multipart streaming、Forever Iframe、JSONP Polling（为了兼容所有的浏览器）。其中WebSocket是html5的主要规范内容之一，API易用性和调试性也是最好的，当然兼容性是个问题。 ![][3] （PS：IE那是一如既往的兼容性不佳，IE10还是很赞的。） 虽然这个系列教程并不会直接用到WebSocket，而是使用socket.io。明河觉得很有必要讲解下WebSocket，因为有助于更深入理解socket.io。 
## Websocket协议 使用chrome打开WebSocket的

[简单demo][4]，这是一个简单的聊天室（用二个支持Websocket的浏览器打开这个网页，输入文字试试。） 看下请求的情况： ![][5] 请求地址的协议非常特殊是`ws`，这是Websocket特有的协议，专门用于Websocket客户端和服务器端的握手通信。 Websocket的请求头去掉了http请求头大量无用的信息，从效率和成本上比ajax（http）高很多。 Websocket的通信内容也更为直观： ![][5] ws协议与http协议的区别是：ws是基于一个TCP连接的全双工的通讯方式，也就是说，服务端可以主动推数据到客户端，客户端也可以发送数据到服务端，有连接且有状态。而http协议， 无连接且无状态。 http发送一个请求后立即丢弃，而ws会保持连接，表象特征：ajax的方式会不断发送请求，而Websocket只会有一个请求。好比有二个岛通行，http是用船来说，船的航行可以是自由灵活的，但运量有限；ws就像在二个岛之间建立起了一座桥，运量的提升可想而知。当然正如桥不会替代http一样，ws并不会替代http，而是对http的非常好的补充。 
### 请求头关键字段的含义 留意：Websocket的版本不同，请求头信息会有差异。 

`Origin` Origin字段，可以用来做身份验证的作用，用于鉴别请求源域名，可以减少非法来源的连接。 在请求中，还会伴随传过来头信息的md5码，可以起到防篡改的作用，可以进一步增强安全性验证。 `Sec-WebSocket-Key` 随机生成的，服务器端会用这些数据来构造出一个SHA-1的信息摘要。 最老的websocket草案标准中是没有安全key，草案7.5、7.6中有两个安全key，而草案10中只有一个安全key，即将7.5、7.6中http头中的"Sec-WebSocket-Key1"与"Sec-WebSocket-Key2"合并为了一个"Sec-WebSocket-Key"，网上很多的教程都很久，写的都是“Sec-WebSocket-Key1”和“Sec-WebSocket-Key2”，这是错误的，现在只有“Sec-WebSocket-Key”。 详细内容可以看[《基于Websocket草案10协议的升级及基于Netty的握手实现》][6] 其他的字段不是太重要，明河就不再累述。 再推荐一篇好文章：[《一起来用Websocket（二）：Websocket协议详细分析》][7]。 
## Websocket的使用

<pre class='brush: js; '>if (window.MozWebSocket) {
  window.WebSocket = window.MozWebSocket;
}
</pre> 之前firefox很坑爹，WebSocket也不知道出于什么理由改成MozWebSocket，现在又是WebSocket了，html5 demo中的这行代码其实没太大意义，诡异的是demo无法在firefox运行通过。 来看下如何建立WebSocket连接： 

<pre class='brush: js; '>function openConnection() {
  // uses global 'conn' object
  if (conn.readyState === undefined || conn.readyState > 1) {
    conn = new WebSocket('ws://node.remysharp.com:8001');    
    conn.onopen = function () {
      state.className = 'success';
      state.innerHTML = 'Socket open';
    };

    conn.onmessage = function (event) {
      // console.log(event.data);
      var message = event.data; //JSON.parse(event.data);
      if (!(/^\d+$/).test(message)) {
        log.innerHTML = '<li class="them">
  ' + message.replace(/[&lt;>&]/g, function (m) { return entities[m]; }) + '
</li>' + log.innerHTML;
      } else {
        connected.innerHTML = message;
      }
    };
    
    conn.onclose = function (event) {
      state.className = 'fail';
      state.innerHTML = 'Socket closed';
    };
  }
}

</pre> 核心代码： 

<pre class='brush: js; '>conn = new WebSocket('ws://node.remysharp.com:8001');  
</pre> WebSocket有几个常用的事件： 

*   onopen：建立连接后
*   onmessage：接受到信息后
*   onclose：连接关闭后 来看下如何处理传过来的数据： 

<pre class='brush: js; '>conn.onmessage = function (event) {
      // console.log(event.data);
      var message = event.data; //JSON.parse(event.data);
    };
</pre>

`event.data`就包含了信息数据。 客户端的代码就这么简单！ 实际应用中，你会发现复杂的多： 
*   如何处理兼容性问题？
*   如何保证数据安全？
*   在服务器端如何处理WebSocket通信？ 幸运的是，我们有socket.io，socket.io非常强大，大大简化了WebSocket的成本，而IE下也可以建立通信（不谈效率，至少可用）。

 [1]: http://www.36ria.com/5914
 [2]: http://socket.io/#home
 [3]: http://s2.36ria.com/201302/4922/32475_o.png
 [4]: http://html5demos.com/web-socket
 [5]: http://s3.36ria.com/201302/4922/32476_o.png
 [6]: http://blog.csdn.net/fenglibing/article/details/6852497
 [7]: http://www.cnblogs.com/dtdnh520/archive/2010/11/11/1875223.html

