# 任务配置详解—grunt入门指南
=======

任务配置指的是`grunt.initConfig({})`中的任务配置，上一篇文章我们配置了`uglify`（这里指任务名，而不是插件名）任务，用于压缩js文件，接单演示了任务配置的功能。 这一篇教程将深入讲解任务配置的细节。 
## 多任务目标 构建中有二个关键字：任务（task）和目标（target），一个任务可以包含多个任务目标。 

<pre class='brush: javascript;'>grunt.initConfig({
        //任务：合并文件
        concat: {
            //目标：构建“foo”
            foo: {

            },
            //目标：构建“bar”
            bar: {

            }
        },
        //任务：压缩文件
        uglify: {
            //目标：压缩“bar”目标指定的文件
            bar: {

            }
        }
    });
</pre> 你可以只调用任务中一个目标的执行： 

<pre class='brush: javascript;'>grunt.registerTask('default', ['uglify:bar']);
</pre> 格式是

`任务名:目标名` 
## 非任务属性 在initConfig()的参数对象中的属性并非都是任务或目标，比如： 

<pre class='brush: javascript;'>grunt.initConfig({
        pkg: {author:"minghe",email:"minghe@36@gmail.com"},
        //任务：压缩文件
        uglify: {
            //目标：压缩“bar”目标指定的文件
            bar: {
                options: {
                    banner: '/*! &lt;%= pkg.author %>'
                }
            }
        }
    });
</pre> 这里的pkg其实是构建配置，在任务中可以使用类似

`pkg.author`这样的形式获取值。 
## Options 大部分的任务都带有options配置属性，用于配置插件的参数，比如常见的

`banner`属性，向构建后文件头打印自定义信息。 以<a href="https://github.com/gruntjs/grunt-contrib-uglify" target="_blank">uglify</a>为例（uglify足够典型而且配置参数很丰富） <pre class='brush: javascript;'>uglify: {
            bar: {
                options: {
                    banner: '/*! &lt;%= pkg.author %>*/',
                    //添加文字到压缩后的文件尾部
                    footer:'/*! 这是压缩文件尾部 */',
                    //美化代码
                    beautify: {
                        //中文ascii化，非常有用！防止中文乱码的神配置
                        ascii_only: true
                    }

                }
            }
        }
</pre> 还有其他有意思的配置，留到下期演示。 

## Files 大部分的任务目标都是处理文件（watch也算是吧），所以文件的配置是任务目标的核心。 共有二种配置files的方式： 

<pre class='brush: javascript;'>uglify: {
            build: {
                //源文件
                src: 'src/hello-grunt.js',
                //目标文件
                dest: 'build/hello-grunt-min.js'
            }
        }
</pre> 还有一种： 

<pre class='brush: javascript;'>uglify: {
           files: {
                'build/hello-grunt-min.js': ['src/hello-grunt.js']
           }
        }
</pre> 明河推荐大家使用第二种方式，更为简洁直观。 files还支持额外的配置属性（需要额外配置属性时只能使用第一种配置方式。）比如下面的代码： 

<pre class='brush: javascript;'>grunt.initConfig({
  concat: {
    bar: {
      files: [
        {src: ['src/bb1.js', 'src/bbb1.js'], dest: 'dest/b1/', filter: 'isFile'},
      ],
    },
  },
});
</pre> filter：过滤器（函数），函数参数为files的src，return true时才会构建该文件。 filter函数可以自定义： 

<pre class='brush: javascript;'>grunt.initConfig({
  clean: {
    foo: {
      src: ['tmp/**/*'],
      filter: function(filepath) {
        return grunt.file.isDir(filepath);
      },
    },
  },
});
</pre> grunt.file.isDir()判断filepath是否是目录。 

## 文件的简单正则匹配语法 文件名（目录路径）的匹配，基本上和ant一样。 

*   `*` 匹配除了**/**外的任意数量的字符，比如`foo/*.js`
*   `?` 匹配除了**/**外的单个字符
*   `**` 匹配包含**/**的任意数量的字符，比如`foo/**/*.js`
*   `!` 排除指定文件，比如`src: ['foo/*.js', '!foo/bar.js']`
*   `{}` 可以理解为“or”表达式，比如`src: 'foo/{a,b}*.js'` 用法举例： 

<pre class='brush: javascript;'>//匹配foo目录下所有th开头的js文件
{src: 'foo/th*.js', dest: ...}

//等价于{src: ['foo/a*.js', 'foo/b*.js'], dest: ...}
{src: 'foo/{a,b}*.js', dest: ...}

//优先处理bar.js，然后再处理其他文件
{src: ['foo/bar.js', 'foo/*.js'], dest: ...}

//排除处理foo/bar.js文件
src: ['foo/*.js', '!foo/bar.js'], dest: ...}

</pre>



