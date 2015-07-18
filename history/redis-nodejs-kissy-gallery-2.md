# Redis+NodeJs实战—kissy gallery的重构（2）
======

上一篇教程简单地介绍了Redis以及NodeJs调用Redis，这篇教程将以几个需求场景演示Redis的小而美。

### 天生的缓存专家

Redis非常适合于做缓存，比如缓存搜索结果，缓存html页面。gallery需要缓存[组件文档页面][1]以提高访问速度。

模板使用Jade。

    var jade = require('jade');
    exports.doc = function(req,res){
        var name = req.params.name;
        var data = {'name':name};
        //缓存键名
        var cacheKey = 'cache:doc:'+name;
        //判断是否存在文档html缓存
        db.get(cacheKey).then(function(html){
            if(!html){
                //解析jade模板
                jade.renderFile('./gallery-express/views/show.jade',data,function(err,html){
                    if(err){
                        res.send(err);
                        return false;
                    }
                    //缓存jade解析后的html内容
                    db.set(cacheKey,html) ;
                    //设置15分钟的生存时间（15分钟后自动删除缓存）
                    db.expire(cacheKey,900);
                    res.send(html);
                })
            }else{
                res.send(html);
            }
        })
    }
    

db为**then-redis**实例，后面不再说明。

缓存的键名举例 **cache:doc:uploader**，由于Redis只能存储键值数据，键的取名是技巧，要避免键名重复，保证语义性，但最后不要太长。

这里使用**:**分割,cache表明键的用途，doc表明缓存文档页面，uploader表明缓存uploader组件的文档。

**db.get(cacheKey)** 捞出页面缓存内容，如果不存在就使用jade渲染。

**db.set(cacheKey,html)** 将Jade解析后的html缓存到cacheKey中。

**db.expire(cacheKey,900);** 设置键的生存时间，以秒为单位，900即15分钟。

生存时间的存在和内存数据库的天然基因证明Redis是天生的缓存专家，过期自动删除，省时省事省代码，同时读写快速。

当然亲也可以使用个js变量来缓存，但生存时间就不好控制，需要额外写js逻辑代码。

### 使用散列类型存储组件信息

假设我的组件信息类似：

    {
        "name": "uploader",
        "version": "1.5.4",
        "author": {"name": "明河", "email": "minghe12@126.com", "role": "author"},
        "cover": "http://img02.taobaocdn.com/tps/i2/T1C1X_Xs8gXXcd0fwt-322-176.png",
        "desc": "异步文件上传组件",
        "forks": "84",
        "watchers": "110",
        "updated_at": "1406000750000",
        "created_at": "1365563272000"
    }
    

如果是关系型的数据库就非常简单，使用一个二维表的结构：

<a href="http://www.36ria.com/6521/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2014-07-23-%e4%b8%8a%e5%8d%889-28-12" rel="attachment wp-att-6524"><img src="http://www.36ria.com/wp-content/uploads/2014/07/屏幕快照-2014-07-23-上午9.28.12-680x150.png" alt="屏幕快照 2014-07-23 上午9.28.12" width="450" height="99" class="alignnone size-large wp-image-6524" /></a>

但Redis是没有这种结构数据类型的，我们只有键值，如何表达这样的数据呢，我们有散列类型！

所谓散列，可以理解为键值保存的是一个js Object数据对象（ps：留意Redis键的原子性质，即最小操作对象，不能像JSON那样嵌套其他的数据类型！）。

        db.hset('repo:1','name','uploader','version','1.5.4','created_at','1406000750000');
    

我们使用 **hset** 来创建个散列键，数据以参数的方式传递进去。

使用**hgetall**获取散列所有字段数据：

        db.hgetall('repo:1').then(function(data){
    
        });
    

如果你只要获取散列其中几个字段数据呢？

        //获取name和version字段数据 
        db.hmget('repo:1','name','version').then();
    

Redis的散列数据是增改合一形式，不存在单独的modify操作，填完后数据后，如果需要修改，依旧调用**db.hset()**即可。

我们使用的键名形式是**repo:$id**，id对应关系数据库中的id字段。如果我新增一个组件，即**repo:2**。

问题来了，假设我们有十条组件数据，如何获取这十条数据呢？

### 使用列表类型存储id列表

假设你有id 1-10 十条数据，我们要获取这十条数据，简单版（这是错误代码，只用于演示）

    exports.all = function(req,res){
        var data = [];
        for(var i = 1;i< 11;i++){
            db.hgetall('repo:'+i).then(function(repo){
                data.push(repo);
            });
        }
        console.log(data);
    }
    

我们使用一个循环来获取所有的组件数据。

但有三个问题：

1.  列表是会增长的，如何知道最大长度？
2.  如果我删除了部分组件数据呢？
3.  如果我需要排序呢？

所以上面的代码实现不可行。我们需要使用新的数据类型：列表类型。

列表类型数据可以理解为一维数组数据，比如：["1","2","3"]。留意不可嵌套使用，即不能表达[["1","2"],["2","3"]]这样的数据结构。

我们把id存储在列表中，维护这个列表即可。

        //向列表右边添加一个数据
        db.rpush('repo:ids',1);
        //向列表右边添加二个数据
        db.rpush('repo:ids',2,3);
        //向列表左边添加一个数据
        db.lpush('repo:ids',0);
    

（PS:在Redis中你不用关键键存不存在，不存在的话会自动创建1个。）

**获取id列表：**

    db.lrange('repo:ids',0,-1);
    

**lrange()** 用于获取指定范围的列表数据，**0,-1** 是获取所有的列表数据。

**获取列表长度：**

    db.llen('repo:ids');
    

还有一个问题我们需要解决：获取组件数据的操作是异步的，如何保证循环获取数据的正确性？

then-redis 模块使用RSVP promise库，所以我们需要用到RSVP中的**all**方法。

    var RSVP = require('rsvp');
    exports.all = function(req,res){
        //先获取所有的id
        db.lrange('repo:ids',0,-1).then(function(ids){
            if(!ids.length) return false;
    
            //获取单条散列数据
            var promises = ids.map(function(id){
                return db.hgetall('repo:'+id);
            });
            //当所有的promise执行完成后，执行这里的then方法
            return RSVP.all(promises).then(function(data){
                //所有的数据
                return data;
            },function(err){
                console.log(err);
                return err;
            })
        });
    }
    

RSVP.all()，接受一个promise数组，当这个数组内的所有promise都执行完成，会执行自己的then方法。

 [1]: http://kissygalleryteam.github.io/uploader/doc/guide/index.html