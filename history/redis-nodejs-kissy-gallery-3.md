# Redis+NodeJs实战—kissy gallery的重构（3）
======

上一篇教程我们学习了散列数据和列表数据，还有些问题没解决：

1.  如何保证id不会重复？
2.  如何对列表数据进行排序？

### 自增长的id

在关系型数据库中id是自增加的，而且是唯一。而在Redis中如何保证呢？特别在并发写操作中，如果出现竞态，就容易出问题。

使用**incr()** 方法：

    db.incr('repo:autoId');
    

自动给 **repo:autoId** 这个键值+1。

如果你希望每次递增2：

    db.incrby('repo:autoId',2);
    

结合前面的添加组件数据的例子：

        db.incr('repo:autoId');
        db.get('repo:autoId').then(function(id){
            db.hset('repo:'+id,'name','uploader');
        })
    

### 如何按创建时间给组件数据排序

使用**sort**方法可以给列表排序：

        db.sort('repo:ids','BY','repo:*->created_at','DESC','LIMIT',0,10).then(function(ids){
            //ids: []
        })
    

sort方法参数比较多，但不难理解。

1.  'BY','repo:*->created_at' 根据repo:*（散列数据，*通配符表示，捞出所有repo:前缀的数据，比如repo:1，repo:2）的created_at来对列表进行排序。
2.  'DESC' 降序排列
3.  LIMIT',0,10 从0索引值开始，捞出10条数据

sort方法是比较耗性能的操作，所以推荐缓存排序结果：

        db.sort('repo:ids','BY','repo:*->created_at','store','cache:repo:sort');
    

将排序结果缓存进 **cache:repo:sort** 键，完整的逻辑如下：

        var cacheKey = 'cache:repo:sort';
        db.exists(cacheKey).then(function(exist){
            if(exist){
                return db.lrange(cacheKey,0,-1);
            }else{             
                return db.sort('repo:ids','BY','repo:*->created_at','DESC','LIMIT',0,10,'store',cacheKey).then(function(size){
                    //缓存生存时间为10分钟
                    db.expire(cacheKey,600);
                    return db.lrange(cacheKey,0,-1);
                })
            }
        })
    

**db.exists** 用于判断一个key是否存在。

### 如何存储组件的标签数据

我们不大可能使用散列数据类型存储标签数据，使用列表数据存储比较怪，而Redis中定义了一种**集合**数据类型，是存储标签数据的最好类型。

<a href="http://www.36ria.com/6528/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2014-07-23-%e4%b8%8b%e5%8d%889-51-02" rel="attachment wp-att-6530"><img src="http://www.36ria.com/wp-content/uploads/2014/07/屏幕快照-2014-07-23-下午9.51.02.png" alt="屏幕快照 2014-07-23 下午9.51.02" width="614" height="148" class="alignnone size-full wp-image-6530" /></a>

如何表达上图的数据结构呢？

        db.sadd('repo:1:tags','表单');
    

通常我们添加的标签不只是一个：

        var data = ['表单','上传组件'];
        db.multi();
        data.forEach(function(tag){
            db.sadd('repo:1:tags',tag);
        });
        return db.exec().then(function (reply) {
            return data;
        });
    

这里我们需要学习个新的东西：**事务**，我们需要在多次添加tag的过程中，没有其他数据库的写的操作插入进来。

**db.multi();** 开启事务，将下文所有的操作缓存起来

**db.exec()** 开始执行缓存中得所有操作。

**集合的优势**

标签数据不会重复，集合会自动处理，集合可以进行交集、并集操作。

比如获取repo:1与repo:2共有的标签：

        db.sinter('repo:1:tags','repo:2:tags').then(function(data){
            //['表单']
        })
    

**获取集合所有数据**

    db.smembers('repo:1:tags')
    

同样也可以使用sort()方法对集合进行排序！

Redis 中还有个数据类型叫有序集合，故名思议，就是带顺序的集合类型。可以[看篇教程][2]。

## 总结

Redis 拥有非常丰富的命令，教程篇幅有限，无法一一说明，亲们可以在Redis官网中找到。推荐[《Redis 命令参考》][3]

 [2]: http://redisbook.readthedocs.org/en/latest/datatype/sorted_set.html
 [3]: http://redis.readthedocs.org/en/latest/index.html