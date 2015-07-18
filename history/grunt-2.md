# 常用插件的使用—grunt入门指南（上）
=======

上一篇文章明河简单讲解了grunt使用，这一篇文章将接着讲解grunt的几个常见任务。 先来看个图： <a href="http://www.36ria.com/6226/2013-7-20-13-17-08" rel="attachment wp-att-6230"><img src="http://www.36ria.com/wp-content/uploads/2013/07/2013-7-20-13-17-08.png" alt="2013-7-20 13-17-08" width="706" height="229" class="alignnone size-full wp-image-6230" /></a> 上图是这二年前端炙手可热的项目，这么项目多多少少都需要构建工具支持，grunt是不二之选。 前面二篇教程，明河大量使用**grunt-contrib-uglify**插件进行演示，一方面是uglify（压缩js文件）任务，基本上是代码发布前必须执行的，另一方面grunt-contrib-uglify非常典型，堪称grunt的插件的代码。 接下来明河再拎出一些常用的插件，演示grunt的用法。 
## [grunt-contrib-concat][1] 非常常用的grunt插件，用于合并任意文件，用法也非常简单： 

<pre class='brush: javascript;'>npm install grunt-contrib-concat --save-dev
</pre>

<pre class='brush: javascript;'>grunt.loadNpmTasks('grunt-contrib-concat');
</pre> （后面的插件演示就不再贴安装插件和注册插件的代码，大同小异。） 任务：合并src下的js文件到build目录，合并后文件名为built.js。 

<pre class='brush: javascript;'>grunt.initConfig({
        concat: {
            options: {
                //文件内容的分隔符
                separator: ';'
            },
            dist: {
                src: ['src/*.js'],
                dest: 'build/built.js'
            }
        }
    });

</pre> 向文件追加一些额外信息： 

<pre class='brush: javascript;'>grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        concat: {
            options: {
                //文件内容的分隔符
                separator: ';',
                stripBanners: true,
                banner: '/*! &lt;%= pkg.name %> - v&lt;%= pkg.version %> - ' +
                            '&lt;%= grunt.template.today("yyyy-mm-dd") %> */'
            },
            dist: {
            }
        }
    });

</pre> 自定义进程函数，比如你需要在合并文件前，对文件名进行处理等。 

<pre class='brush: javascript;'>grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        concat: {
            options: {
                 // Replace all 'use strict' statements in the code with a single one at the top
                banner: "'use strict';\n",
                process: function(src, filepath) {
                        return '// Source: ' + filepath + '\n' +
                                 src.replace(/(^|\n)[ \t]*('use strict'|"use strict");?\s*/g, '$1');
                }
            },
            dist: {
            }
        }
    });

</pre>

## [grunt-contrib-copy][2] 顾名思义，用于复制文件或目录的插件。 

<pre class='brush: javascript;'>copy: {
  main: {
    files: [
      {src: ['path/*'], dest: 'dest/', filter: 'isFile'}, // 复制path目录下的所有文件
      {src: ['path/**'], dest: 'dest/'}, // 复制path目录下的所有目录和文件
    ]
  }
}
</pre> 有复制，必然有删除。 

## [grunt-contrib-clean][3]

<pre class='brush: javascript;'>clean: {
  build: {
    src: ["path/to/dir/one", "path/to/dir/two"]
  }
}
</pre>

## [grunt-contrib-compress][4] 用于压缩文件和目录成为zip包，不是很常用。 

<pre class='brush: javascript;'>compress: {
  main: {
    options: {
      archive: 'archive.zip'
    },
    files: [
      {src: ['path/*'], dest: 'internal_folder/', filter: 'isFile'}, path下所有的js
      {src: ['path/**'], dest: 'internal_folder2/'}, // path下的所有目录和文件
    ]
  }
}
</pre>

## [grunt-contrib-jshint][5] jshint用于javascript代码检查（并会给出建议），发布js代码前执行jshint任务，可以避免出现一些低级语法问题。 jshint拥有非常丰富的配置，可以自由控制检验的级别。 

<pre class='brush: javascript;'>module.exports = function(grunt) {

    // 构建任务配置
    grunt.initConfig({
        //读取package.json的内容，形成个json数据
        pkg: grunt.file.readJSON('package.json'),
        jshint: {
            options: {
                //大括号包裹
                curly: true,
                //对于简单类型，使用===和!==，而不是==和!=
                eqeqeq: true,
                //对于首字母大写的函数（声明的类），强制使用new
                newcap: true,
                //禁用arguments.caller和arguments.callee
                noarg: true,
                //对于属性使用aaa.bbb而不是aaa['bbb']
                sub: true,
                //查找所有未定义变量
                undef: true,
                //查找类似与if(a = 0)这样的代码
                boss: true,
                //指定运行环境为node.js
                node: true
            },
            //具体任务配置
            files: {
                src: ['src/*.js']
            }
        }
    });

    // 加载指定插件任务
    grunt.loadNpmTasks('grunt-contrib-jshint');

    // 默认执行的任务
    grunt.registerTask('default', ['jshint']);
};
</pre> 配置的含义，明河都写在代码注释中，就不再累述了。 运行

`grunt`命令后： <a href="http://www.36ria.com/6226/2013-8-3-14-48-40" rel="attachment wp-att-6238"><img src="http://www.36ria.com/wp-content/uploads/2013/08/2013-8-3-14-48-40.png" alt="2013-8-3 14-48-40" width="381" height="337" class="alignnone size-full wp-image-6238" /></a> jshint比较有意思的是还可以结合[grunt-contrib-concat][6]插件使用，在合并文件前（后）对js进行检查。 <pre class='brush: javascript;'>grunt.initConfig({
  concat: {
    dist: {
      src: ['src/foo.js', 'src/bar.js'],
      dest: 'dist/output.js'
    }
  },
  jshint: {
    beforeconcat: ['src/foo.js', 'src/bar.js'],
    afterconcat: ['dist/output.js']
  }
});
</pre> 类似的还有个

[grunt-contrib-csslint][7]插件，用于css代码检查，用法基本一样，只是配置上存在差异，貌似css检查的需求有些鸡肋，就不再演示了。 
## [grunt-contrib-mincss][8] 非常常用的插件，用于css压缩。 用法相对于grunt-contrib-uglify简单很多： 

<pre class='brush: javascript;'>mincss: {
  compress: {
    files: {
      "path/to/output.css": ["path/to/input_one.css", "path/to/input_two.css"]
    }
  }
}
</pre>

## [grunt-css-combo][9] 我的同事紫英写的css合并的插件，用于css分模块书写时的合并（如果你不使用less、sass、stylus，建议使用这个插件）。 

<pre class='brush: javascript;'>grunt.initConfig({
  css_combo: {
    files: {
      'dest/index.combo.css': ['src/index.css'],
    },
  },
})
</pre> 文件目录的demo请看

[github][10]。 src/index.css的代码如下： <pre class='brush: javascript;'>@import "./mods/mod1.css";
@import "./mods/mod2.css";
#content {}
</pre> 通过

`@import`来合并模块css文件。

 [1]: https://npmjs.org/package/grunt-contrib-concat
 [2]: https://npmjs.org/package/grunt-contrib-copy
 [3]: https://npmjs.org/package/grunt-contrib-clean
 [4]: https://npmjs.org/package/grunt-contrib-compress
 [5]: https://npmjs.org/package/grunt-contrib-jshint
 [6]: https://github.com/gruntjs/grunt-contrib-concat
 [7]: https://npmjs.org/package/grunt-contrib-csslint
 [8]: https://npmjs.org/package/grunt-contrib-mincss
 [9]: https://npmjs.org/package/grunt-css-combo
 [10]: https://github.com/daxingplay/grunt-css-combo/tree/master/test/fixtures

