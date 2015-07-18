# 前端项目可以更简单—Yeoman入门指南（1）
=======

今天明河介绍一个比较新的前端工具：<a href="http://yeoman.io/" target="_blank">Yeoman</a>。 
## 什么是Yeoman 很难使用一句话来表述Yeoman用途，因为Yeoman其实是三个工具的集合：

<a href="http://yeoman.io/" target="_blank">YO</a>、<a href="https://github.com/gruntjs/grunt" target="_blank">GRUNT</a>、<a href="http://bower.io/" target="_blank">BOWER</a>，所以需要先解释下这三个工具的用途。 <img src="https://raw.github.com/yeoman/yeoman.io/gh-pages/media/toolset.png" alt="" width="860" /> 
*   <a href="http://yeoman.io/" target="_blank">YO</a>：Yeoman核心工具，项目工程依赖目录和文件生成工具，项目生产环境和编译环境生成工具
*   <a href="https://github.com/gruntjs/grunt" target="_blank">GRUNT</a>：grunt去年很火，前端构建工具，jquery就是使用这个工具打包的
*   <a href="http://bower.io/" target="_blank">BOWER</a>：Web开发的包管理器，概念上类似npm，npm专注于nodeJs模块，而bower专注于CSS、JavaScript、图像等前端相关内容的管理 infoq的有篇yeoman的介绍文章：

<a href="http://www.infoq.com/cn/news/2012/09/yeoman" target="_blank">《Yeoman：构建漂亮Web应用的工具和框架》</a>，标题对yeoman的定义不算准确。 **yeoman定义了一套用于提高前端工程师效率的规范工作流工具。** 效率和规范是yeoman的核心诉求，后面明河讲解yeoman使用场景时会说到，yeoman是如何提高你的项目效率。 打个比喻：如果前端项目是工厂的产品的话，yeoman就像工厂的流水线，标准化、傻瓜化、批量化产品生产，生产过程乏味了，但效率提高了。 
## 什么场景下使用yeoman？ 假设，明河接到一个项目：火车票订票系统，代码层面，前几天思考的问题如下： 1.项目目录该如何规划？ 2.使用什么类库来支撑系统开发？ 3.生产环境如何搭建（比如很多前端的生产环境是基于php，也有基于NodeJs） 4.编译环境如何搭建（编译环境其实应该归到生产环境中，但前端很多人使用coffeescript/less/sass等，所以需要编译环境） 5.单元测试环境如何搭建？ 6.调试环境如何搭建（本地代理远程assets等） 7.开发完毕后打包部署如何处理？ 我相信多数前端在项目coding前肯定都会碰上类似的问题，你是花半天、1天、2天解决？ 假如你是多人合作呢？问题更严重，如何保持团队环境和代码规范的一致性？教团队成员装依赖？配置工具？大费口舌告之规范？ 没做一个项目，你都会遇到相同问题，再重复一遍？ 亲累不？ 

**用yeoman！1行命名，15秒进入coding状态！** 想尝试下吗？往下看~ 
## yeoman的简单使用

### 安装yeoman 需要用到

<a href="http://www.infoq.com/cn/articles/nodejs-npm-install-config" target="_blank">npm</a>，安装 yo、grunt-cli、bower <pre class='brush: javascript;'>npm install -g yo grunt-cli bower

</pre> 安装成功后，会看到下面的提示： 

![][1] 留意yo的二个模块：generator-mocha和generator-webapp，**generator-**前缀的模块表明它是一种工程模版，比如你的web工程是基于<a href="http://www.bootcss.com/" target="_blank">bootstrap</a>框架，那么可以安装<a href="https://github.com/yeoman/generator-bootstrap" target="_blank">generator-bootstrap</a>，使用yo来自动生成该目录（会带上bootstrap的源码）。 <pre class='brush: javascript;'>npm install -g generator-bootstrap
</pre>

### 生成工程目录 创建（或打开）你的工程根目录，比如demo-app，然后运行命令： 

<pre class='brush: javascript;'>yo webapp
</pre> webapp是yo自带的工程模版带有：

<a href="http://html5boilerplate.com/" target="_blank">html5 Boilerplate</a>、jquery、<a href="http://modernizr.com/" target="_blank">Modernizr</a>、<a href="http://www.bootcss.com/" target="_blank">Bootstrap</a>、<a href="http://requirejs.org/" target="_blank">RequireJS</a>等框架。 ![][2] yeoman有个非常人性化的功能，带有问答功能，比如上图中，yeoman询问你，你的工程需要Bootstrap(sass)框架吗？ 需要输入“n”，因为需要sass运行环境（ruby）window下麻烦，先不引入。 yeoman还会询问你，你的工程需要RequireJS模块加载框架吗？ 我们继续输入“Y”，OK，工程生成完毕，可以看下目录情况。 ![][3] ![][4] 一切如此简单，你不需要再手动创建工程目录，也不需要再费事的google搜索下需要的框架，然后再下载，解压到工程目录。 使用一行命令，然后就立马进入coding状态！这就是yeoman提高前端工作的地方。 还没完，yeoman如果只能帮你创建工程目录，还不足以给我们惊喜。 如果我的工程中需要sass/less/coffscript编译呢？ 我的工程中有ajax请求，需要放在apache的目录下？ 我写了单元测试，如何回归？ ..... 这些问题，在yeoman中都不是问题，因为已经集成在内了。 
### 起个Node服务器 yeoman内置了Node服务器服务，而且会监听工程目录下的文件的改变，一旦文件发生改变会重新编译文件（sass/less/coffscript）。 下面轮到

**grunt**上场，在工程目录下，运行命令： <pre class='brush: javascript;'>grunt server
</pre>

![][5] 启动成功后会自动打开本地浏览器，默认地址为**http://localhost:9000/**，9000端口号，可能被占用，这时候打开gruntfile.js文件（grunt的任务配置文件），找到： <pre class='brush: javascript;'>options: {
                port: 9000,
                // change this to '0.0.0.0' to access the server from outside
                hostname: 'localhost'
            }
</pre> 修改下

**port**，即可。 
### 回归测试用例 Yeoman默认使用

<a href="http://visionmedia.github.io/mocha/" target="_blank">mocha</a>作为测试框架，是在 PhantomJS环境下进行回归测试。 运行如下命令： <pre class='brush: javascript;'>grunt test
</pre>

![][6] 启动成功，并回归成功，你可以在test目录下找到测试代码： ![][7] 
### 工程中引入其他类库 比如，我想要在工程中引入 

<a href="http://underscorejs.org/" target="_blank">underscore</a> <pre class='brush: javascript;'>bower install underscore  
grunt                
</pre> bower上场了，使用bower从在线包管理器中拉取underscore代码 

## Yeoman特性总结 偷下懒，抄下其他人的翻译： 

*   快速创建骨架应用程序——使用可自定义的模板（例如：HTML5、Boilerplate、Twitter Bootstrap等）、AMD（通过RequireJS）以及其他工具轻松地创建新项目的骨架。
*   自动编译CoffeeScrip和Compass——在做出变更的时候，Yeoman的LiveReload监视进程会自动编译源文件，并刷新浏览器，而不需要你手动执行。
*   自动完善你的脚本——所有脚本都会自动针对jshint（软件开发中的静态代码分析工具，用于检查JavaScript源代码是否符合编码规范）运行，从而确保它们遵循语言的最佳实践。
*   内建的预览服务器——你不需要启动自己的HTTP服务器。内建的服务器用一条命令就可以启动
*   非常棒的图像优化——Yeoman使用OptPNG和JPEGTran对所有图像做了优化，从而你的用户可以花费更少时间下载资源，有更多时间来使用你的应用程序。
*   生成AppCache清单——Yeoman会为你生成应用程序缓存的清单，你只需要构建项目就好
*   杀手级”的构建过程——你所做的工作不仅被精简到最少，让你更加专注，而且Yeoman还会优化所有图像文件和HTML文件、编译你的CoffeeScript和Compass文件、生成应用程序的缓存清单，如果你使用AMD，那么它还会通过r.js来传递这些模块。这会为你节省大量工作
*   集成的包管理——Yeoman让你可以通过命令行（例如，yeoman搜索查询）轻松地查找新的包，安装并保持更新，而不需要你打开浏览器
*   对ES6模块语法的支持——你可以使用最新的ECMAScript 6模块语法来编写模块。这还是一种实验性的特性，它会被转换成eS5，从而你可以在所有流行的浏览器中使用编写的代码
*   PhantomJS单元测试——你可以通过PhantomJS轻松地运行单元测试。当你创建新的应用程序的时候，它还会为你自动创建测试内容的骨架

 [1]: http://s1.36ria.com/201304/4922/35120_o.png
 [2]: http://s1.36ria.com/201304/4922/35121_o.png
 [3]: http://s2.36ria.com/201304/4922/35123_o.png
 [4]: http://s3.36ria.com/201304/4922/35124_o.png
 [5]: http://s1.36ria.com/201304/4922/35148_o.png
 [6]: http://s2.36ria.com/201304/4922/35149_o.png
 [7]: http://s3.36ria.com/201304/4922/35150_o.png

