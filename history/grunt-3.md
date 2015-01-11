# 常用插件的使用—grunt入门指南（下）
=======

## less/sass/stylus预编译 在前端工程中使用css预编译器（less/sass/stylus）用于弥补css的语言缺陷，基本上是标配了，其中less和sass用的最多，但明河最喜欢使用的是stylus，grunt官方有对应的编译插件。 这里以

<a href="https://npmjs.org/package/grunt-contrib-stylus" target="_blank">grunt-contrib-stylus</a>为例。 <pre class='brush: javascript;'>stylus: {
            options: {
                //指定要扫描的目录（针对@import内的路径）
                paths: ['src'],
                //指定将图片路径转成base64数据的函数（比如配置urlfunc:"embedurl"，扫描embedurl(test.png)）
                urlfunc: 'embedurl',
                import: [      //  @import 'foo', 'bar/moo', 每一个.styl文件
                    'foo',
                    'bar/moo'
                ]
            },
            main: {
                files: [
                    {
                        expand: true,
                        cwd: 'src',
                        src: '*.styl',
                        dest: 'src',
                        ext: '.css'
                    }
                ]
            }
        }
</pre> 上述任务将src目录下的所有.styl文件编译成.css文件。 less，sass的编译插件用法类似，配置上有差异，sass推荐使用

<a href="https://npmjs.org/package/grunt-contrib-compass" target="_blank">grunt-contrib-compass</a>。 （PS:如果你使用webstrom或idea，其实不需要grunt来处理css的预编译，IDE已经内置处理了。） 如果每次修改完都要运行命令编译次，是非常麻烦的，所以我们还需要个实时监听插件<a href="https://npmjs.org/package/grunt-contrib-watch" target="_blank">grunt-contrib-watch</a>，后面会讲到。 当然亲也可以使用一些编译工具，比如<a href="http://www.livereload.com/" target="_blank">livereload</a>（livereload非常强大，但win平台的软件很弱）和<a href="http://koala-app.com/index-zh.html" target="_blank">koala</a>。 
## coffeescript编译 关于coffeescript的争议非常多，上次明河还听到facebook同学关于coffeescript的批评，在阿里技术论坛里也有过争论，不管如何coffeescript的确很诱人。 coffeescript有带了编译工具，可以满足实时编译需求，就是每次运行很麻烦，结合grunt使用，可以很好的消化coffeescript的编译成本（如果你使用webstrom，可以无视编译，已经自动处理鸟。） 

<a href="https://npmjs.org/package/grunt-contrib-coffee" target="_blank">grunt-contrib-coffee</a>用法举例： <pre class='brush: javascript;'>coffee: {
            compile: {
                options: {
                    //去掉匿名函数包裹
                    bare:true,
                    //当编译多个.coffee文件成单个.js时，先合并
                    join:true,
                    //编译.coffee文件时生成个.map文件，用于链接到coffee源码文件
                    sourceMap:true
                },
                files:[
                    {
                        expand: true,
                        cwd: 'src',
                        src: ['**/*.coffee'],
                        dest: 'src',
                        ext: '.js'
                    }
                ]
            }

        }
</pre>

## 实时监听插件

<a href="https://npmjs.org/package/grunt-contrib-watch" target="_blank">grunt-contrib-watch</a>非常重要，它能监测文件的修改，触发指定任务，比如less、coffee编译等。 先来看个最简单的demo： <pre class='brush: javascript;'>watch: {
            options: {
                spawn: false
            },
            scripts: {
                files: [ '&lt;%= pkg.version %>/**/*.coffee' ],
                tasks: [ 'coffee']
            }
        }
</pre> 监听源码目录下所有的.coffee文件的修改，触发coffee任务（编译coffee文件）。 

**spawn**配置一般禁掉，设置为true时，运行任务会开启子进程处理，可能会出现不稳定的问题。 **files**：监听哪些文件的修改 **tasks**：文件修改后触发哪些任务 <pre class='brush: javascript;'>coffee: {
            compile: {
                options: {
                    //去掉匿名函数包裹
                    bare:true
                },
                files:[
                    {
                        expand: true,
                        cwd: '&lt;%= pkg.version %>',
                        src: ['**/*.coffee'],
                        dest: '&lt;%= pkg.version %>',
                        ext: '.js'
                    }
                ]
            }

        }
</pre>

<pre class='brush: javascript;'>grunt.registerTask('w',['watch:scripts']);
</pre> 运行grunt w后，没有报错的情况下，随便修改个coffee文件，会出现类似如下结果： （PS:registerTask的name不能和task的名称重复，不然会出现死循环，比如这里不能grunt.registerTask('watch']);） 

<a href="http://www.36ria.com/6273/grunt-1-2" rel="attachment wp-att-6330"><img src="http://www.36ria.com/wp-content/uploads/2014/01/grunt-1.png" alt="grunt-1" width="435" height="389" class="alignnone size-full wp-image-6330" /></a> grunt-contrib-watch拥有丰富的配置，一般采用默认配置即可，grunt-contrib-watch可以结合<a href="https://npmjs.org/package/grunt-contrib-livereload" target="_blank">livereload</a>插件使用，可以实现文件发生改变后刷新页面，这个功能很有意思。 <pre class='brush: javascript;'>grunt.initConfig({
  watch: {
    options: {
      livereload: true,
    },
    css: {
      files: ['public/scss/*.scss'],
      tasks: ['compass'],
    },
  },
});
</pre> livereload会开启个服务器，默认端口号为35729。 在页面中引入监控脚本： 

<pre class='brush: xml;'><script src="http://localhost:35729/livereload.js"></script>
</pre> 或者安装livereload的

<a href="http://feedback.livereload.com/knowledgebase/articles/86242-how-do-i-install-and-use-the-browser-extensions-" target="_blank">浏览器插件</a>。 
## 图片压缩工具插件

<a href="https://npmjs.org/package/grunt-contrib-imagemin" target="_blank">grunt-contrib-imagemin</a>，能够快速的压缩工程内的图片，非常实用的前端工具，用法非常简单： <pre class='brush: javascript;'>imagemin: {
            dynamic: {
                files: [{
                    expand: true,
                    cwd: 'src/',
                    src: ['**/*.{png,jpg,gif}'],
                    dest: 'dist/'                
                }]
            }
        }
</pre>

## yuidoc文档生成插件

<a href="https://npmjs.org/package/grunt-contrib-yuidoc" target="_blank">grunt-contrib-yuidoc</a>：基于YUIDOC生成js API文档 <pre class='brush: javascript;'>yuidoc: {
    compile: {
      name: '&lt;%= pkg.name %>',
      description: '&lt;%= pkg.description %>',
      version: '&lt;%= pkg.version %>',
      url: '&lt;%= pkg.homepage %>',
      options: {
        paths: 'path/to/source/code/',
        themedir: 'path/to/custom/theme/',
        outdir: 'where/to/save/docs/'
      }
    }
  }
</pre>

**paths**：js源码位置； **themedir**：文档模板位置； **outdir**：文档输出位置。 
## karma单测回归插件

<a href="https://npmjs.org/package/grunt-karma" target="_blank">grunt-karma</a>：是<a href="http://blog.fens.me/nodejs-karma-jasmine/#gsc.tab=0" target="_blank">karma</a>的grunt插件版，webstrom内置karma回归功能。 demo： <pre class='brush: javascript;'>karma: {
  unit: {
    configFile: 'karma.conf.js'
  }
}

</pre> 会寻找karma.conf.j配置文件。

