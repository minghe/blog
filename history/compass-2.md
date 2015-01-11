# compass实战—grunt
=======

这篇教程将深入讲解compass与grunt的结合，包括[grunt-contrib-watch][2]、[grunt-contrib-compass][3]、sprite等。

## grunt-contrib-watch：实时编译sass文件

[点此查看完整Gruntfile.js][4]

    grunt.loadNpmTasks('grunt-contrib-watch');
    

注册watch任务：

    grunt.registerTask('dev', ['watch']);
    

watch任务配置：

        watch: {
            compass: {
                files: ['<%= srcBase %>/**/*.sass'],
                tasks: ['compass']
            }
        }
    

监听src目录下所有的sass文件的改变，自动触发compass编译任务。

运行 **grunt dev** 命令，然后随便改下sass文件内容试试。

<a href="http://www.36ria.com/6433/compass-6" rel="attachment wp-att-6436"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-6.png" alt="compass-6" width="575" height="314" class="alignnone size-full wp-image-6436" /></a>

### Live Reloading

我们还希望sass文件改变后自动刷新页面，Live Reloading勘称神器。

页面中引入：

    <script src="//localhost:35729/livereload.js"></script>
    

35729为livereload默认端口号。

除了静态引用外，还可以安装个[chrome插件][5]自动注入。

watch增加livereload配置：

        watch: {
            options: {
                livereload: true
            },
            compass: {
                files: ['<%= srcBase %>/**/*.sass'],
                tasks: ['compass']
            }
        }
    

然后运行 **grunt dev** ,打开demo（可以参考明河写的[demo][6]），随便改变sass文件内容试试，页面自动刷新了！

## grunt-contrib-compass

上一篇教程已经简单介绍 grunt-contrib-compass 的使用， grunt-contrib-compass拥有非常丰富的配置，明河重点拎出常用的来讲。

### specify

**specify: '<%= srcBase %>/index.sass'** specify可以指定编译文件。

### banner

在开启specify后可以配置banner:"demo compass"，向该文件添加自定义注释，比如 licensing。

### config/raw

使用grunt-contrib-compass后无需在工程目录下安放config.rb配置文件，如果有需要也可以配置：

    compass: {
    dist: {
      options: {
        config: 'config/config.rb',  // css_dir = 'dev/css'
        raw: 'preferred_syntax = :sass\n' // Use `raw` since it's not directly available
      }
    }
    }
    

**config** 指向config.rb文件，而raw也可以配置compass，为变量配置语句。

grunt-contrib-compass 有个 **watch:true**，会调用 **compsass watch** 命令实时编译sass文件，不大推荐使用，使用 grunt-contrib-watch 更为强大，且可以集成其他任务，比如coffeescript编译等。

### assetCacheBuster

默认编译时会生成.sass-cache目录，如果你不希望有该目录，可以配置 **assetCacheBuster: false** 。

### imagesPath

图片所在目录，这个配置与sprite（雪碧图）息息相关，下一篇讲解sprite时会用到。

### httpPath

用于指定工程上线后的http路径，比如配置：**httpPath:'http://g.assets.daily.taobao.net/tb/tb-fp/14.0.0'**，代码中的图片或字体文件路径自动加上该段路径。后面会讲到开发环境和线上环境的配置。

httpImagesPath/httpFontsPath 都与之相关。

### importPath

@import '' 依赖sass文件时，特别指定的寻找的目录。

下一篇教程我们来看下sprite的处理，这是compass的亮点，可以切实地提高效率....

 [1]: http://www.36ria.com/6417
 [2]: https://github.com/gruntjs/grunt-contrib-watch
 [3]: https://github.com/gruntjs/grunt-contrib-compass
 [4]: https://github.com/minghe/compass-demo/blob/master/Gruntfile.js
 [5]: http://feedback.livereload.com/knowledgebase/articles/86242-how-do-i-install-and-use-the-browser-extensions-
 [6]: https://github.com/minghe/compass-demo/blob/master/demo/demo.html

