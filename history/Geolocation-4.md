# Geolocation和Web Sockets基于NodeJs的实战（END）
=======

前面三篇教程：

*   [Geolocation和Web Sockets基于NodeJs的实战（1）][1]
*   [Geolocation和Web Sockets基于NodeJs的实战（2）][2]
*   [Geolocation和Web Sockets基于NodeJs的实战（3）][3]

明河将原理部分全部讲完了，在[第二篇教程][4]，我们已经实现一个可以定位了地图，不支持多用户的地理信息都标记在地图的功能，这篇的教程将继续实现这部分功能。

在[第一篇教程][5]中，明河有简单演示了sockets.io的用法，主要是服务器端上的，接下来我们来看下客户端的调用（API基本和服务器端保持一致）。

以下代码在[application.js][6]的`positionSuccess`函数（获取地理信息成功后触发）内。

##### 建立sockets.io连接

<pre class='brush: js; '>var socket = io.connect('/');
</pre>

##### 当页面处于用户操作下时发送请求

<pre class='brush: js; '>var doc = $(document);
        //用户使用应用时候推送消息
        doc.on('mousemove', function() {
            
        });
    }
</pre>

如果页面中鼠标发生了移动，我们判定为用户激活的客户端页面，这时候推送消息，所以我们监听mousemove事件发送请求，mousemove事件触发频率非常高，而我们的实时应用， 并不需要如此频繁的推送消息，所以需要设置下时间间隔，30毫秒就好。

<pre class='brush: js; '>var doc = $(document);
        var emit = $.now();
        doc.on('mousemove', function() {
            if ($.now() - emit > 30) {
                                socket.emit('send:coords', sentData);
                emit = $.now();
            }
        });
    }
</pre>

使用`socket.emit()`推送坐标信息，sentData的内容如下。

<pre class='brush: js; '>sentData = {
                id: userId,
                active: active,
                coords: [{
                    lat: lat,
                    lng: lng,
                    acr: acr
                }]
            }
</pre>

`coords`坐标信息来源于Geolocation的回调函数自带的信息。

`active`用于标识客户端是否处于激活状态（如果鼠标移出页面，设置active为false）。

<pre class='brush: js; '>doc.bind('mouseup mouseleave', function() {
        active = false;
    });
</pre>

mousemove事件监听器内的所有代码如下：

<pre class='brush: js; '>doc.on('mousemove', function() {
            active = true; 

            sentData = {
                id: userId,
                active: active,
                coords: [{
                    lat: lat,
                    lng: lng,
                    acr: acr
                }]
            }

            if ($.now() - emit > 30) {
                socket.emit('send:coords', sentData);
                emit = $.now();
            }
        });
    }
</pre>

##### 接收到消息后显示用户的地理坐标

这里的用户指其他访问该页面的用户。

<pre class='brush: js; '>var connects = {};
    socket.on('load:coords', function(data) {
        if (!(data.id in connects)) {
            setMarker(data);
        }
        connects[data.id] = data;
            connects[data.id].updated = $.now(); 
    });
</pre>

如果地图上存在用户的地理信息标记，就不需要再生成了。所以需要将数据保存到**connects**对象内。

`setMarker()`方法用于重新设置用户地理坐标标记，代码如下：

<pre class='brush: js; '>function setMarker(data) {
        for (i = 0; i &lt; data.coords.length; i++) {
            var marker = L.marker([data.coords[i].lat, data.coords[i].lng], { icon: yellowIcon }).addTo(map);
            marker.bindPopup('<p>
  One more external user is here!
</p>');
            markers[data.id] = marker;
        }
    }
</pre>

removeMarker方法用于删除坐标标记，代码如下：

<pre class='brush: js; '>function removeMarker(ident){
        delete connects[ident];
        map.removeLayer(markers[ident]);
    }
</pre>

我们希望每隔15秒（时间可以自己设定），自动移除非激活用户的坐标，防止用户太多，地图上都是红色标记。

<pre class='brush: js; '>setInterval(function() {
        for (ident in connects){
            if ($.now() - connects[ident].updated > 15000) {
                               removeMarker(ident);
            }
            }
        }, 15000);
</pre>

##### 地理信息获取失败的容错处理

<pre class='brush: js; '>navigator.geolocation.getCurrentPosition(positionSuccess, positionError, { enableHighAccuracy: true });
</pre>

<pre class='brush: js; '>function positionError(error) {
        var errors = {
            1: 'Authorization fails', // permission denied
            2: 'Can\'t detect your location', //position unavailable
            3: 'Connection timeout' // timeout
        };
        showError('Error:' + errors[error.code]);
    }

    function showError(msg) {
        info.addClass('error').text(msg);

        doc.click(function() { info.removeClass('error') });
    }
</pre>

完整代码，<a href="https://github.com/minghe/36ria-demo/blob/master/node/geolocation/public/js/application.js" target="_blank">请看这里</a>。

## 总结

这个系列教程到此全部结束，希望对大家了解Socket、Geolocation、Node有帮助，谢谢大家，有问题可以给明河留言。

 [1]: http://www.36ria.com/5914 "Geolocation和Web Sockets基于NodeJs的实战（1）"
 [2]: http://www.36ria.com/5943 "Geolocation和Web Sockets基于NodeJs的实战（2）"
 [3]: http://www.36ria.com/6045 "Geolocation和Web Sockets基于NodeJs的实战（3）"
 [4]: http://www.36ria.com/5943
 [5]: http://www.36ria.com/5914
 [6]: https://github.com/minghe/36ria-demo/blob/master/node/geolocation/public/js/application.js


