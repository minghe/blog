# Redis+NodeJs实战—kissy gallery的重构（1）
======

[kissy gallery][1]是kissy组件库集合网站，用于展示kissy的组件信息和文档，采用NodeJs开发，第一个版本发布后，每周挂掉3次 -_-! ，原因是那时候图简单，使用文件存储组件数据（是个json文件），在写操作时没有做队列处理，造成竞态写数据，很容易导致读取文件时出现读取json出错。

饱受摧残后下定决心使用数据库，数据库的选择很多，比如广受NodeJs开发者喜欢的[mongodb][2],简单趁手，就像右手，但我选择了 [Redis][3]，原因是右手用多了，其实左手也不错 -_-! 。

## 为什么选择 Redis

### 1.快，变态快

Redis是内存数据库，所有的数据都存储在内存中，很多系统将Redis用作缓存，Redis同时也支持持久化数据。

kissy gallery在重构前，请求[http://gallery.kissyui.com/api/coms?callback=jsonp20&len=12][4]，为210ms，而使用Redis之后只要30ms。

### 2.简单

Redis采用键值存储结构，相对于关系型数据库来说比较独特。这种结构在使用中非常简单直观，后面会通过代码详细演示。同时Redis非常丰富，数据类型能覆盖大部分的需求场景。

### 3.稳定

以前kissy gallery系统每周挂掉3次 ，而使用Redis之后还没挂掉过...

Redis 现在在不少大公司中使用，证明其可靠性。

## Hello Redis

Redis的安装非常简单，请看[《Redis (一) 安装》][5]，安装成功后运行**./redis-server**，界面如下图：

<a href="http://www.36ria.com/6504/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2014-07-18-%e4%b8%8b%e5%8d%887-38-21" rel="attachment wp-att-6505"><img src="http://www.36ria.com/wp-content/uploads/2014/07/屏幕快照-2014-07-18-下午7.38.21-666x400.png" alt="屏幕快照 2014-07-18 下午7.38.21" width="450" height="270" class="alignnone size-large wp-image-6505" /></a>

运行**./redis-cli**，使用命令**info**查看下Redis信息：

<a href="http://www.36ria.com/6504/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2014-07-18-%e4%b8%8b%e5%8d%887-40-57" rel="attachment wp-att-6508"><img src="http://www.36ria.com/wp-content/uploads/2014/07/屏幕快照-2014-07-18-下午7.40.57-505x400.png" alt="屏幕快照 2014-07-18 下午7.40.57" width="450" height="356" class="alignnone size-large wp-image-6508" /></a>

试试Redis的简单命令，存储个名字：

    set name minghe
    

(log: ok)

获取名字：

    get name
    

(log: minghe)

Redis就是这么简单。

## NodeJs使用Redis

NodeJs使用Redis，一般使用node_redis模块，但由于NodeJs的异步特性，为了防止出现大量恶心的回调，明河推荐使用[then-redis][6]模块，该模块自带promise模块[rsvp][7]，读写数据库时不会出现大量回调。

### 连接数据库

    var redis = require('then-redis');
    /**
     * 连接redis数据库
     */
    var db = redis.createClient();
    db.connect().then(function(){
        var timer = new Date();
        console.log(timer+': 成功连接redis数据库 ^_^');
    });
    

建议就初始化一个redis（这里指then-redis模块）实例。

### 设置/获取数据库键值

试试设置名字：

    db.set('name','minghe');
    

获取名字：

    db.get('name').then(function(name){
      console.log(name);
    })
    

对redis实例所有get方法都是异步的。但我们不需要set()后的返回值，所以只要保证set命令发出即可，无需set().then()。

### 如何捕获数据库操作错误？

    db.get('name').then(function(name){
      //success
    },function(err){
      //error
      console.log(err);
    })
    

then()方法的第二个参数为错误回调。

### 获取多个键值

    db.get('name').then(function(name){
        return db.get('email').then(function(email){
            return {name:name,email:email};
        })
    })
    

留意promise的使用。

 [1]: http://gallery.kissyui.com/
 [2]: http://www.mongodb.org/
 [3]: http://www.redis.cn/
 [4]: http://gallery.kissyui.com/api/coms?_ksTS=1404351614399_19&callback=jsonp20&len=12
 [5]: http://blog.csdn.net/chenggong2dm/article/details/6100001
 [6]: https://www.npmjs.org/package/then-redis
 [7]: https://www.npmjs.org/package/rsvp