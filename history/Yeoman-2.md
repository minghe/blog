# 自定义项目工程生成器上—Yeoman入门指南（2）
=======

《Yeoman入门指南（1）》发表后，没想到反响那么热烈，其实上一篇教程比较水，跟其他介绍Yeoman的文章差不了多少，而这一篇，真正的干货来了！其他网站没讲到的。 这篇教程明河将深入讲解**yo**的用法。 ![][1] 
## 理解yo的命令 上一篇教程，使用yo生成项目工程目录，用的命令如下： 

<pre class='brush: javascript;'>yo webapp

</pre>

**webapp**是什么呢？可以是yo demo或yo minghe吗？ 答应是yes，但是是有前提的。 webapp可以理解为工程模版，这个工程模版中包含了<a href="http://html5boilerplate.com/" target="_blank">html5 Boilerplate</a>、jquery、<a href="http://modernizr.com/" target="_blank">Modernizr</a>、<a href="http://www.bootcss.com/" target="_blank">Bootstrap</a>、<a href="http://requirejs.org/" target="_blank">RequireJS</a>等。 它的本质是个NodeJs模块，模块全称是<a href="https://github.com/yeoman/generator-webapp" target="_blank">generator-webapp</a>，为了通俗一点，我们叫它工程生成器，generator-webapp是yeoman自带的模块，所以你可以直接使用`yo webapp`生成工程目录。 当然除了generator-webapp，还有其他很多基于不同框架的工程生成器，比如<a href="https://github.com/yeoman/generator-backbone" target="_blank">generator-backbone</a>、<a href="https://github.com/yeoman/generator-angular" target="_blank">generator-angular</a>、<a href="https://github.com/yeoman/generator-ember" target="_blank">generator-ember</a>。 backbone、angular、ember这些热门的框架，相信各位都有所耳闻，就不再累述了。 如果您打开这些模块，会发现他们的结构是非常相似的，像下图： ![][2] yeoman的所有的生成器结构都是一样，也就是说你可以使用yo的API和模版来**构建属于自己的工程生成器**。 这种场景非常多，很多公司内部都自己实现了一套框架，完全安装yeoman的默认生成器generator-webapp来，是多余且不现实的，你需要自定义一套适用于自身业务的生成器。 问题来了，那**如何编写一个yeoman的生成器呢？** 接下来明河以<a href="https://github.com/kissygalleryteam/generator-kissy-gallery" target="_blank">generator-kissy-gallery</a>为例讲解下。 
### copy一个demo生成器

<pre class='brush: javascript;'>git clone https://github.com/passy/generator-generator.git

</pre> 将

<a href="https://github.com/passy/generator-generator" target="_blank">generator-generator</a>代码拉到本地（这个demo模版名字很坑爹，名字叫generator，很容易引起混淆）。 然后改下目录名称，比如将其改成generator-kissy-gallery（用于生成kissy组件目录，方便开发者使用）。 
### 理解app目录

**app目录**是核心目录，必须存在。 其下有个**index.js**，也是必须存在的。 还记得前面的命令：`yo webapp`吗？当有了index.js，yo会自动注册一个起始命令，比如demo这里就是`yo kissy-gallery`。 
### 使用npm link链接成全局模块 想要在命令行上正常使用

`yo kissy-gallery`，我们还需要运行`npm link`命令，将**generator-kissy-gallery**链接成全局模块，供开发时使用（将模块发布到npm上后就不需要了）。 既然用到了npm的命令，少不了**package.json**，不是本文重点，不清楚的同学，请看<a href="https://npmjs.org/" target="_blank">npm官网</a>。 
### 继承yeoman.generators.Base

<pre class='brush: javascript;'>var util = require('util');
var path = require('path');
var yeoman = require('yeoman-generator');

module.exports = Gallery;
function Gallery(args, options, config) {
    yeoman.generators.Base.apply(this, arguments);
    console.log("模块初始化完成！");
}

util.inherits(Gallery, yeoman.generators.Base);

</pre> 这是通用模版，

**yeoman-generator**为依赖模块，是我们自定义的生成器的基类。 留意：类必须继承`yeoman.generators.Base`！ `util.inherits()`类的继承方法。 到这里，你可以试着在命令行工具，输入`yo kissy-gallery`，正常的话就会在命令行界面打印出文案。 有了命令只是第一步，我们还需要给命令编写一些逻辑。 
### 处理命令行的参数 我们希望yo kissy-gallery有个参数用于生成版本目录，比如

`yo kissy-gallery 1.1`，工具自动生成一个**1.1**目录。 <pre class='brush: javascript;'>module.exports = Gallery;
function Gallery(args, options, config) {
    yeoman.generators.Base.apply(this, arguments);
    this.version = args[0] || '1.0';
}

util.inherits(Gallery, yeoman.generators.Base);

</pre> 构造函数Gallery的第一个参数args，就是用户命令行中输入的值！

` args[0] `，即获取第一个参数。 
### 获取生成器的环境变量 构造函数Gallery的第二次参数options，是生成器的配置信息，拥有非常丰富的属性，建议大家使用

`console.log(options);` ![][3] 接下来我们利用options，来获取用户当前目录，设置为组件名称，比如cd的当前目录是c:\demo，那么我们截取demo作为组件名 <pre class='brush: javascript;'>module.exports = Gallery;
function Gallery(args, options, config) {
    yeoman.generators.Base.apply(this, arguments);
    this.cwd = options.env.cwd;
    this.componentName = path.basename(this.cwd);    
}

util.inherits(Gallery, yeoman.generators.Base);

</pre>

`options.env.cwd`值为用户目录路径。 
### 定义些问询，让用户配置参数 当用户运行

`yo kissy-gallery`，我希望用户输入组件作者名称和email，这样我就可以把这二个信息写到一个json文件内。 <pre class='brush: javascript;'>prt.askAuthor = function(){
    var cb = this.async();
    var prompts = [{
        name: 'author',
        message: 'author of component:',
        default: 'kissy-team'
    },{
        name: 'email',
        message: 'email of author:',
        default: 'kissy-team@gmail.com'
    }];

    this.prompt(prompts, function (err, props) {
        if (err) {
            return this.emit('error', err);
        }
        this.author = props.author;
        this.email = props.email;
        cb();
    }.bind(this));
}
</pre> 给Gallery原型对象添加个

`askAuthor`方法。 （PS:当运行命令时，yo会自动运行Gallery下的所有方法，你不需要手动调用，执行的顺序，取决于你定义的顺序。） 由于咨询用户是一个异步的过程，会卡住命令逻辑的运行，所以需要调用yo的异步方法：`var cb = this.async();`。 接下来定义问题，比如demo中，明河咨询用户：“author of component:”（请不要使用中文，命令行显示会有问题）。 <pre class='brush: javascript;'>var prompts = [{
        name: 'author',
        message: 'author of component:'
    }];
</pre>

`name`字段是必须的，获取用户答案时需要用到， `message`字段也是必须的，您需要用户回答的问题， 除此之外还有`default:'css/less/sass'`，用于提示用户输入。 你可以咨询用户多个问题。 接下来就是注册问题： <pre class='brush: javascript;'>this.prompt(prompts, function (err, props) {
        if (err) {
            return this.emit('error', err);
        }
        this.author = props.author;

        cb();
    }.bind(this));
</pre>

`prompt()`是yo内置方法，第一个参数为问题数组，第二个参数为用户输入完后触发的回调。 回调函数的第二个参数`props`，保存着用户的回答，为了方面其他逻辑调用，demo中赋值`this.author` <pre class='brush: javascript;'>this.author = props.author;
</pre> 接下来调用

`cb();`，执行接下来的逻辑。 
### 生成一个目录 demo中需要生成一个组件目录，目录名为组件名： 

<pre class='brush: javascript;'>//组件名称
        this.componentName = props.componentName;
        this.mkdir(this.componentName);
</pre>

`mkdir()`是被继承的父类方法，用于生成一个目录，参数为目录路径。 
### 复制文件到目录内 比如demo中需要生成个Gruntfile.js，用于grunt打包。 在app目录下，定义个templates目录，创建个

**Gruntfile.js**文件（内容根据自己业务需求而定）。 <pre class='brush: javascript;'>prt.gruntFile = function(){
    this.copy('Gruntfile.js',this.componentName + '/Gruntfile.js');
}
</pre>

`copy()`方法用于复制文件到指定目录下，第一个参数为源文件，第二个参数为目标路径。 我们的任务到此才完成了一半，处理yo kissy-gallery命令，还需要个命令，来生成组件的版本目录，目录类似： ![][4] 下篇继续讲解。

 [1]: http://s0.36ria.com/201304/4922/35252_o.png
 [2]: http://s1.36ria.com/201304/4922/35253_o.png
 [3]: http://s2.36ria.com/201305/4922/35651_o.png
 [4]: http://s0.36ria.com/201304/4922/35254_o.png



