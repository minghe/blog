# Getting started—grunt入门指南
=======

<a href="http://www.36ria.com/6192/2013-7-11-20-48-30" rel="attachment wp-att-6204"><img src="http://www.36ria.com/wp-content/uploads/2013/07/2013-7-11-20-48-30.png" alt="2013-7-11 20-48-30" width="680" height="258" class="alignnone size-full wp-image-6204" /></a> 
## 什么是grunt？

<a href="http://gruntjs.com/" target="_blank">grunt</a>是javascript项目构建工具，在grunt流行之前，前端项目的构建打包大多数使用ant。（详细请看明河写的[《ant与前端》][1]），但ant对于前端而言，存在不友好，执行效率低，学习成本高的问题。 “人活一世，有的成了面子，有的成了里子都是时势使然”—《一代宗师》 grunt成为面子，ant成为里子，也是时势使然（只是说前端项目构建中），js模块化和场景复杂化，导致js文件颗粒化，而像less、coffscript等预编译语言的盛行，导致ant力不从心，时势造英雄，grunt应运而生。 grunt真正为大多数人所知，jquery居功至伟（那时grunt的插件可没这么丰富，jquery使用的构建工具，这名头就够响）。 
## grunt能干些什么？ grunt其实是哆啦a梦的百宝袋，是工具集，拥有非常丰富的任务插件，可以实现各式各样的构建目标。 按任务目标大致可分为四类： 

*   文件操作型：比如合并、压缩js和css文件等（包括）
*   预编译型：比如编译less、sass、coffeescript等
*   类库项目构建型：比如 angular、ember、backbone等（这种推荐使用yeoman）
*   工程质量保障型：比如jshint、jasmine、mocha等 除此之外还有像 watch （监听文件改变，自动触发构建）等功能。 后面明河会一一演示。 

## 安装grunt grunt依赖

<a href="http://nodejs.org/" target="_blank">NodeJs</a>和<a href="https://npmjs.org/" target="_blank">npm</a>，请确保你的计算机已经安装该环境。 特别留意下grunt是有二个版本：服务器端版本（grunt）和客户端版本（grunt-cli），我们需要安装的是客户端版本。 <pre class='brush: javascript;'>npm install -g grunt-cli
</pre>

<a href="http://www.36ria.com/6192/grunt-1" rel="attachment wp-att-6217"><img src="http://www.36ria.com/wp-content/uploads/2013/07/grunt-1.gif" alt="grunt-1" width="557" height="412" class="alignnone size-full wp-image-6217" /></a> （-g安装全局NodeJs模块，npm服务器有时会抽风，失败的多试几次） 如果你不慎安装了服务器端版，请现予以卸载： <pre class='brush: javascript;'>npm uninstall -g grunt
</pre>

<a href="https://github.com/minghe/36ria-demo/tree/master/node/grunt" target="_blank">demo请猛击这里查看</a>。 
## package.json 假设你有个工程目录叫demo，在工程根目录放个package.json package.json是npm的包配置文件，前面说过grunt是哆啦a梦的口袋，需要什么才掏出什么，package.json用于配置你需要拉取的grunt插件信息，比如下面的代码： 

<pre class='brush: javascript;'>{
    "name": "demo",
    "version": "1.0.0",
    "devDependencies": {
        "grunt-contrib-uglify": "~0.2.0"
    }
}

</pre> 留意

`devDependencies`字段，定义你要拉取的依赖模块，上面的代码，拉取**grunt-contrib-uglify**插件（用于压缩js），字段的值`~0.2.0`，指明需要模块的版本号，“~”是至少的意思。 在工程根目录启动命令行工具，运行： <pre class='brush: javascript;'>npm install
</pre>

<a href="http://www.36ria.com/6192/2013-7-13-17-50-19" rel="attachment wp-att-6218"><img src="http://www.36ria.com/wp-content/uploads/2013/07/2013-7-13-17-50-19.png" alt="2013-7-13 17-50-19" width="509" height="411" class="alignnone size-full wp-image-6218" /></a> 依赖拉取成功后，在demo工程中生成了node_modules目录，该目录就包含了grunt-contrib-uglify插件模块的代码。 到此，准备工作已经完成，来个“hello world！”吧。 
## Gruntfile.js 先在demo工程根目录创建个src目录放个测试文件，hello-grunt.js，代码如下： 

<pre class='brush: javascript;'>(function($){
    var str = "<p>
  hello grunt!
</p>";
    $('body').append(str);
})(jQuery)
</pre> 再在根目录下创建个build目录，我们的目标很简单，将src目录下的hello-grunt.js压缩到build目录下，文件名为hello-grunt-min.js。 在demo工程根目录下放个

**Gruntfile.js**，文件内容如下： <pre class='brush: javascript;'>module.exports = function(grunt) {

    // 构建任务配置
    grunt.initConfig({
        //读取package.json的内容，形成个json数据
        pkg: grunt.file.readJSON('package.json'),
        uglify: {
            //文件头部输出信息
            options: {
                banner: '/*! &lt;%= pkg.name %> &lt;%= grunt.template.today("yyyy-mm-dd") %> */\n'
            },
            //具体任务配置
            build: {
                //源文件
                src: 'src/hello-grunt.js',
                //目标文件
                dest: 'build/hello-grunt-min.js'
            }
        }
    });

    // 加载指定插件任务
    grunt.loadNpmTasks('grunt-contrib-uglify');

    // 默认执行的任务
    grunt.registerTask('default', ['uglify']);
};
</pre> 看不懂上面的代码，没关系，我们先运行看看效果。 执行

`grunt`命令： <a href="http://www.36ria.com/6192/2013-7-13-18-07-41" rel="attachment wp-att-6219"><img src="http://www.36ria.com/wp-content/uploads/2013/07/2013-7-13-18-07-41.png" alt="2013-7-13 18-07-41" width="426" height="98" class="alignnone size-full wp-image-6219" /></a> "Done,without error"，说明已经构建成功，且没有错误，你可以看到grunt是非常迅速的！ 来看下目录，你就会看到build下出现了hello-grunt-min.js，文件内容如下： <pre class='brush: javascript;'>/*! demo 2013-07-13 */
!function(a){var b="<p>
  hello grunt!
</p>";a("body").append(b)}(jQuery);
</pre> 已经使用uglify压缩成功！ 

### 接下来我们来重点看下Gruntfile.js代码的含义。 所有grunt的代码，必须放在module.exports函数内，参数grunt为grunt对象，当你运行命令

`grunt`时，grunt系统会去读此函数的grunt构建配置。 grunt对象有几个核心方法。 
##### grunt.initConfig(obj) initConfig用于配置构建信息，第一个参数必须是个object。 

<pre class='brush: javascript;'>// 构建任务配置
    grunt.initConfig({

    });
</pre>

##### grunt.file.readJSON(path) 读取一个json文件，通常我们会把构建任务的基本配置写在独立的json文件内，方便我们修改。 

<pre class='brush: javascript;'>// 构建任务配置
    grunt.initConfig({
        //读取package.json的内容，形成个json数据
        pkg: grunt.file.readJSON('package.json')
    });
</pre> 上面代码，将读取的json赋值给pkg字段，想要获取配置的值，就可以使用

`pkg.name`这样的形式。 
##### grunt.loadNpmTasks(pluginName) 加载指定插件任务，示例代码如下： 

<pre class='brush: javascript;'>grunt.loadNpmTasks('grunt-contrib-uglify');
</pre> 可以加载多个： 

<pre class='brush: javascript;'>grunt.loadNpmTasks('grunt-contrib-uglify');
    //css压缩
    grunt.loadNpmTasks('grunt-contrib-cssmin');
</pre>

##### grunt.registerTask(taskName,taskArray) 注册任务，比如下面的代码： 

<pre class='brush: javascript;'>grunt.registerTask('default', ['uglify']);
</pre> 注册默认执行的任务，即你运行grunt命令时，触发的任务构建。 第二个参数为任务目标名，在initConfig()中配置： 

<pre class='brush: javascript;'>grunt.initConfig({
        //读取package.json的内容，形成个json数据
        pkg: grunt.file.readJSON('package.json'),
        uglify: {
            //文件头部输出信息
            options: {
                banner: '/*! &lt;%= pkg.name %> &lt;%= grunt.template.today("yyyy-mm-dd") %> */\n'
            },
            //具体任务配置
            build: {
                //源文件
                src: 'src/hello-grunt.js',
                //目标文件
                dest: 'build/hello-grunt-min.js'
            }
        }
    });
</pre> uglify为任务目标名，

**options**为grunt-contrib-uglify插件配置参数。 **banner**向输出文件打印你需要的信息。 **build**为具体任务构建配置 分别指定src和dest，就大功告成！ 更多细节留待下一篇讲解~~~~ 
## 总结 grunt的优势在于Gruntfile.js就是js，你可以随心所欲的写js代码，这在构建复杂工程任务目标时非常有用。 grunt的另外一个优势就是快！“天下武功，唯快不破！” 后续明河将更详细介绍grunt细节~~~ 打完，收工~

 [1]: http://book.36ria.com/ant/index.html



