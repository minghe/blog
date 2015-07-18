# compass实战—入门篇
=======

<a href="http://www.36ria.com/6417/compass" rel="attachment wp-att-6418"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass.png" alt="compass" width="520" height="250" class="alignnone size-full wp-image-6418" /></a>

sass作为css预编译器逐渐成为主流关于在于[compass][1]，compass对于sass，就像ruby on rails对于ruby。compass是Sass的工具库，涵盖基于sass开发的完整工作流，推荐阅读[《Compass用法指南》][2]，这篇文章不够深入，故开个系列教程讲解compass（教程重点不在sass，[sass的资料传送门][3]）。

一个好的css工具库必须包含如下几个部分：

*   工具支撑
*   css3支持
*   css常用设计模式库
*   响应式设计支持
*   兼容性hack支持
*   sprite图片支持
*   retina支持
*   字体支持

compsass完全满足上述需求，可以提高css编写效率和提高代码的可维护性，这个系列教程会覆盖上述内容。

## compass的安装

compass让人比较头疼的是存在对ruby环境的依赖（mac请无视），在团队协作中，先要搞定ruby环境，虽然不难，但有时会各种意外，比如gem镜像源不行，无法拉去代码，版本太低之类的，看人品...

less和stylus就简单的多，明河有一段时间用的是[stylus][4]，对其爱不释手，只是stylus有些小众，而且[nib][5]库不够给力....

### 安装ruby

推荐一篇文章：[《在Mac/Win中安装SASS/Compass》][6]

[下载入口传送门][7]，下载exe文件安装即可。

（PS：留意自己机器是32位，还是64位，安装界面勾选上环境变量注入。）

<a href="http://www.36ria.com/6417/compass-5" rel="attachment wp-att-6430"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-5.jpg" alt="compass-5" width="503" height="392" class="alignnone size-full wp-image-6430" /></a>

安装成功后，运行**gem**命令试试~

### 安装sass和compass

    gem install sass
    
    gem install compass
    

安装成功后运行**compass**命令试试，compass拥有丰富的命令指令，但一般比较少用，更多的我们还是使用grunt插件，这样能够集成到前端工作流中。

## compass的简单使用

试试用compass创建工程目录：

    compass create demo
    

<a href="http://www.36ria.com/6417/compass-2" rel="attachment wp-att-6422"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-2.png" alt="compass-2" width="452" height="253" class="alignnone size-full wp-image-6422" /></a>

创建成功的目录文件如下图：

<a href="http://www.36ria.com/6417/compass-3" rel="attachment wp-att-6423"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-3.png" alt="compass-3" width="207" height="125" class="alignnone size-full wp-image-6423" /></a>

*   config.rb是配置文件
*   stylesheets是编译后的css文件
*   sass是sass原文件

这种方式适合演练，但不大适合实际项目工程中。sass源目录和输出目录是可以配置的，请看config.rb：

    css_dir = "stylesheets"
    sass_dir = "sass"
    

运行**compass compile**命令即可执行sass的编译。

**compass watch**应该是最常用的命令，会监听sass源文件的改变自动编译。

## 使用gui工具编译

可视化编译工具很多，这里推荐[koala][8]，使用非常简单，拖拽目录到工具内即可。

![enter image description here][9]

除此之外还推荐[LiveReload][10]，文件改变后会自动刷新页面，懒人必备。

gui工具属于简单粗暴型的，很多时候项目只需要编译index.sass生成Index.css即可，但工具也会编译同级目录的其他sass文件，不够精细。

所以个人喜欢使用grunt来自定义compass的任务。

## compass demo工程

[简单demo工程传送门][11]，拉下来后运行**npm install**。

重点看下grunt如何写：我们的目标是将src/index.sass，编译成css文件。

核心插件：[grunt-contrib-compass][12]。

完整Gruntfile.js请看[github][13]。

任务定义：

     compass: {
            dist: {
                options: {
                    sassDir: '<%= srcBase %>',
                    specify: '<%= srcBase %>/index.sass',
                    cssDir : '<%= srcBase %>'
                }
            }
        }
    

上述 srcBase 变量指向src目录。

*   sassDir：sass的源目录
*   cssDir： 编译后css目标目录
*   specify：特别指定编译文件，demo中我们只需要编译index.sass

grunt.loadNpmTasks('grunt-contrib-compass');

grunt.registerTask('default', ['compass']);

最后运行**grunt**即编译成功：

<a href="http://www.36ria.com/6417/compass-4" rel="attachment wp-att-6425"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-4.png" alt="compass-4" width="329" height="303" class="alignnone size-full wp-image-6425" /></a>

我们迈出了关键的第一步，下一篇继续折腾grunt。

 [1]: http://compass-style.org/
 [2]: http://www.ruanyifeng.com/blog/2012/11/compass.html
 [3]: http://www.w3cplus.com/sassguide/syntax.html
 [4]: http://learnboost.github.io/stylus/
 [5]: http://visionmedia.github.io/nib/
 [6]: http://www.tuicool.com/articles/FF7fEv
 [7]: http://rubyforge.org/frs/?group_id=167
 [8]: http://koala-app.com/index-zh.html
 [9]: http://koala-app.com/img/screenshot.png
 [10]: http://livereload.com/
 [11]: https://github.com/minghe/compass-demo
 [12]: https://github.com/gruntjs/grunt-contrib-compass

