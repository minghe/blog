# Gulp.js深入讲解
=======

上周明河发了篇《Gulp.js—比Grunt更易用的前端构建工具》，同时也贴在阿里的技术站上，引起了不少前端同学的讨论。

**赤温**（开发工程师）说：这东西（Gulp.js与Grunt）差距不大，社区才是王道！

**英布**（高级前端开发工程师）说：一个是以配置的方式，一个是以编程的方式，感觉各有千秋吧，复杂流程还是使用编程的方式比较好，简单的那就随便了。

**诚冉**（前端开发工程师）说：曾经苦于将grunt的各个工具黏合在一起的过程，grunt这个东西生出来就不是给小项目用的，必须是一定规模的并且持续更新的项目，才舍得花这么大力气来“制造工具”。一点也不夸张，虽然grunt的组件越来越多，但概念就像import一样，称不上是框架，顶多是一种合作机制。Nodejs的世界里，好像不跟stream和pipe沾点边的概念都不好用似的，现在写Web服务框架，基本都跟connect一个风格，req和res一层一层的流经各个handler。刚一看到glup下意识的觉得有点俗气了，但是想想Unix的哲学，标准化的输入输出，不需要过多的元信息描述，简单实用，组件连接起来产生的巨大威力，任何人都无法拒绝！

**猎隼**（资深前端开发工程师）说：基于node.js的工具有一点让人深恶痛绝：会创建一个文件夹，而且里边的文件有大量的冗余。这对项目文件是一种污染，很有可能让一些不明真相的工具把这些代码也一起搜索了，比如一些查找替换程序。比较喜欢ruby的管理方式，需要的包在本机只需要安装一次，而且不会像node那样创建一个文件夹

**半边**同学推荐了一个讨论Gulp.js与Grunt的[issue][2]，明河也推荐一篇文章[《No Need To Grunt, Take A Gulp Of Fresh Air》][3]。

Gulp.js处理构建任务如下图：

<a href="http://www.36ria.com/6382/b0b77qn" rel="attachment wp-att-6403"><img src="http://www.36ria.com/wp-content/uploads/2014/03/B0B77QN.png" alt="B0B77QN" width="684" height="239" class="alignnone size-full wp-image-6403" /></a>

Grunt处理任务：

<a href="http://www.36ria.com/6382/oecgjus" rel="attachment wp-att-6404"><img src="http://www.36ria.com/wp-content/uploads/2014/03/oeCGJUS.png" alt="oeCGJUS" width="1224" height="214" class="alignnone size-full wp-image-6404" /></a>

（上图来自<http://slid.es/contra/gulp>）

## 理解Gulp.js的核心设计

streaming很容易联想到河流，河流有哪些特征？流动、源源不断、具有方向、有起点、有终点，而这些特征也是Gulp.js构建代码的特点。

[Gulp.js][4]官网上的简介是**The streaming build system**，核心的词是streaming（流动式），如何理解这个词呢？[《Gulp.js—比Grunt更易用的前端构建工具》][1]，明河讲到Gulp.js的精髓在于对Node.js中[Streams API][5]的利用，所以想要理解Gulp.js，我们必须理解Streams，streaming其实就是Streams的设计思想。

流(stream)的概念来自unix，核心思想是do one thing well，一个大工程系统应该是由各个小且独立的管子连接而成，如诚冉同学所说：Unix的哲学，标准化的输入输出，不需要过多的元信息描述，简单实用，组件连接起来产生的巨大威力，任何人都无法拒绝！，如下图：

![gulp.js][6]

推荐下[《stream-handbook》][7]，非常详细地说明了如何使用Streams，也提供了很多Streams的第三方模块供参考。

我们以经典的Nodejs读取文件逻辑来说明Streams与传统方式的差异。

使用fs模块读取一个json文件

### 传统的方式：

    var dataJson, fs;
    
    fs = require('fs');
    
    dataJson = './gallery-db/component-info.json';
    
    exports.all = function(req, res) {
        fs.readFile(dataJson,function(err, data){
            if (err) {
                console.log(err);
            } else {
                res.end(data);
            }
        });
    };
    

fs.readFile()是将文件全部读取放进内存，然后触发回调，这种方式存在二方面的瓶颈。

**1.读取大文件时容易造成内存泄漏**

比如一个上传视屏的服务，面对少者二三百M，多者七八G的文件，明显这种方式不可行，所以我们需要分断上传，如果使用传统方式，就会发现非常蛋疼，估计没人想这么干....

**2.深恶痛绝的回调问题**

这是我自己写NodeJs代码最深恶痛绝的地方，在提供大的服务时，比如读取4-5个文件，回调层层嵌套，可读性和维护性非常大，恶心指数陡增。

而且每次你都要对err进行处理：

    if (err) {
        console.log(err);
    } else {
        //...
    }
    

代码啰嗦，不够简练。[koa][8]已经解决了这个问题，NodeJs的发展史就是跟回调斗争史啊。

### 流的方式：

    var dataJson, fs;
    
    fs = require('fs');
    
    dataJson = './gallery-db/component-info.json';
    
    exports.all = function(req, res) {
      var stream;
      stream = fs.createReadStream(dataJson);
      stream.on('error', function(err) {
        return console.log(err);
      });
      stream.on('data', function(chunk) {
        return console.log('got %d bytes of data', chunk.length);
      });
      return stream.pipe(res);
    };
    

fs.createReadStream()创建一个**readable streams**（只读的流），请看[《stream-handbook》][7]中的readable streams部分，readable streams是有状态的，比如可以监听其**data**事件，读取数据后会触发，比如**error**事件，读取数据失败后会触发。

**stream.pipe(res)**，**pipe**方法是流对象的核心方法，这句代码可以理解为，res（结果集，实际上是一个writeable streams，写流）对象接收从stream而来的数据，并予以处理输出。所以Gulp.js中的pipe()方法并不是gulp的方法，而是流对象的方法。pipe()返回的是res的返回的对象。

上述代码会把**component-info.json**的内容打印出来。

流的种类有：**readable** streams、**writeable** streams（比如Gulp.js中的gulp.dest('./build')f方法）、**transform**（明河很不喜欢这个词，词不达意，称之为**through**更为合适，表示这是读/写流，后面会详细讲解）streams，**duplex** streams（duplex可以理解为循环的流，比如服务器端和客户端的实时通信，由于与Gulp.js无关，不展开讲）。

## 如何开发个Gulp.js插件？

所有的Gulp.js插件基本都是through（后面不再使用transform这个词） streams，即是消费者（接收gulp.src() 传递出来的数据，然后进行处理加工处理），又是生产者（将加工后的数据传递出去），我们以[gulp-clean][9]为例（这个插件用于清理目录和文件）。

先看下gulp-clean的API用法：

    var gulp = require('gulp');
    var clean = require('gulp-clean');
    
    gulp.task('default', function() {
        gulp.src('app/tmp')
            .pipe(clean());
    });
    

非常简单的代码，目的是残忍地将tmp目录干掉。

gulp-clean的[源码传送门][10]。

### 引入依赖模块

    var rimraf = require('rimraf');
    var es = require('event-stream');
    var gutil = require('gulp-util');
    var path = require('path');
    

[rimraf][11] 模块用于删除目录。

gulp-clean插件使用了个through streams的包装模块：[event-stream][12]，用于简化streams调用的api，明河推荐使用[through2][13]，会更好用。

path 模块就不说了，大家太熟悉了。

### 定义供外部调用插件方法

    module.exports = function (options) {
      return es.map(function (file, cb) {
        //具体业务逻辑
      });
    };
    

**es.map()**会创建一个through streams，我们只要关注写业务逻辑就好，不用关注如何创建streams。

file为上游读流产生的文件数据，cb是核心方法用于向下游传递处理后的数据。

    return cb(null, file);
    

### 具体业务代码

    // 获取文件绝对路径
    var filepath = file.path;
    var cwd = file.cwd;
    var relative = path.relative(cwd, filepath);
    
    // 防止路径错误
    if (!(relative.substr(0, 2) === '..') && relative !== '' || (options ? (options.force && typeof options.force === 'boolean') : false)) {
      //删除目录
      rimraf(filepath, function (error) {
        if (!error) {
          return cb(null, file);
        } else {
          return cb(new Error('Unable to delete "' + filepath + '" file (' + error.message + ').'), file);
        }
      });
    //打印错误消息
    } else if (relative === '') {
      gutil.log('gulp-clean: Cannot delete current working directory. (' + filepath + ')');
      return cb(null, file);
    }
    //打印错误消息
    else {
      gutil.log('gulp-clean: Cannot delete files outside the current working directory. (' + filepath + ')');
      return cb(null, file);
    }
    

代码非常简单，获取文件（目录）路径，调用rimraf()删除之即可。

一个简单的Gulp.js插件就完成了，so easy！

## 总结

Gulp.js的使用和插件的开发都很简单，当然里面还有很多细节，明河就不展开讲了，请看Gulp.js的官方文档。

 [1]: http://www.36ria.com/6373
 [2]: https://github.com/gulpjs/gulp/issues/81
 [3]: http://travismaynard.com/writing/no-need-to-grunt-take-a-gulp-of-fresh-air
 [4]: http://gulpjs.com/
 [5]: http://nodejs.org/api/stream.html
 [6]: http://s2.36ria.com/201402/4922/43423_o.jpg
 [7]: https://github.com/substack/stream-handbook
 [8]: https://github.com/koajs/koa
 [9]: https://www.npmjs.org/package/gulp-clean/
 [10]: https://github.com/peter-vilja/gulp-clean/blob/master/index.js
 [11]: https://www.npmjs.org/package/rimraf
 [12]: https://www.npmjs.org/package/event-stream
 [13]: https://www.npmjs.org/package/through2

