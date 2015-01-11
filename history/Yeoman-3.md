# 自定义项目工程生成器下—Yeoman入门指南（3）
=======

## 文件模版的处理 上一篇教程，我们使用

`this.copy()`来复制文件，在实际的需求中，可能你还需要修改文件中的文字，举个例子，我们生成了**abc.json**文件，文件的内容可能如下： <pre class='brush: javascript;'>{
    "name": "uploader",
    "version":"1.4",
    "author":{"name":"明河","email":"minghe36@126.com","page":"https://github.com/minghe"}
}
</pre> 如果只是简单的copy，用户生成文件后，还要手动修改abc.json的内容，有没有办法使用模版替换的方式来解决这个问题呢？ yeoman是个好同志，它提供

`template()`方法，可以实现模版的替换。 在app目录下创建个templates目录（所有的模版都要放在该目录下），新建个**abc.json**模版，内容为： <pre class='brush: javascript;'>{
    "name": "&lt;%= componentName %>",
    "version": "&lt;%= version %>",
    "desc": "请修改组件描述",
    "cover": "",
    "author": {
        "name": "&lt;%= author%>",
        "email": "&lt;%= email%>",
        "page": "请修改作者个人首页"
    }
}
</pre>

**<%= version %>**即为模版变量。 使用template()替换模版： <pre class='brush: javascript;'>this.template('abc.json','abc.json');
</pre> 第一个参数是源文件，即

**app/templates/abc.json**； 第二个参数为目标文件，即命令行cd的路径。 **<%= version %>**其实等价于this.version，this指向继承于yeoman.generators.Base的类。 
## 子命令的实现 比如kissy-gallery的工具需要个

**version**的子命令，来实现创建新版本号目录的功能。 <pre class='brush: javascript;'>yo kissy-gallery:version 1.2
</pre> 这是如何实现的呢？ 其实很简单，我们只要在生成器内，新增个version目录（与app目录同级），该目录下同样需要

**index.js**文件和**templates**目录。 ![][1] version/index.js的类与app/index.js类似，也需要继承于yeoman.generators.Base，这里就不再累述了。 
## yoman的常用方法汇总 所有的api请看：

<https://github.com/yeoman/generator/wiki/base>。 
### installDependencies() 自动拉取工程依赖，类似执行

`npm install`效果，前提是你的工程下有package.json。 
### read(source， encoding ) 读取文件内容，默认读出的内容为utf8的编码，如果需要其他编码，请指定第二个参数。 

### write(filepath ， conent) 写入指定内容到文件。 

*   filepath：文件路径
*   conent：文件内容 用法举例： 

<pre class='brush: javascript;'>this.write("abc.json",'{"test":"minghe"}');
</pre>

### directory(source ， destination ) 复制目录到指定路径 

*   source ：目录源路径
*   destination ：目标路径

### remote(user,repo， callback) 非常有意思的方法，用于远程拉取github的库。 

*   user：github的用户名
*   repo：版本库名
*   callback：拉取成功后执行的回调函数

<pre class='brush: javascript;'>this.remote('user', 'repo', function(err, remote) {
  remote.copy('.', 'vendors/user-repo');
});
</pre> 生成器的介绍就到此，更多用法，请大家看yeoman的文档，下一篇教程，明河将讲解grunt，有兴趣的朋友请继续关注

 [1]: http://s2.36ria.com/201306/4922/37522_o.png

