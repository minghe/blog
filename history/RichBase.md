# kissy1.3的RichBase精讲
=======

<a href="http://docs.kissyui.com/docs/html/api/component/rich-base/index.html" target="_blank">RichBase</a>是kissy1.3新增的模块，文档的说明简单的令人发指，RichBase在复杂组件中用途不小，所以明河花一个篇幅讲解下RichBase的适用场景和使用方法。 
## 什么是RichBase？ RichBase是kissy1.3的模块，是组件的抽象积累，是对

<a href="http://docs.kissyui.com/docs/html/api/core/base/index.html" target="_blank">Base</a>的增强，RichBase继承于Base，所以拥有Base的所有方法和属性。 （PS：kissy的大多数组件都继承于Base,而Base定义了属性的统一的描述方式，比如使用get('pro')获取对象属性，set('pro','value')设置对象属性，带有getter方法和setter方法等。） RichBase定义了一套统一且完整的组件生命周期，包括组件的配置、构造、调用、注入插件、析构。 Base已经可以满足至少70%的需求，那为什么还需要RichBase基类呢？RichBase究竟相对于Base增强了哪些内容？这些就是本文要讨论的内容。 
## 为什么你需要RichBase？ 如果你开发的组件需要

`插件机制`，明河建议继承RichBase，那么哪种类型的组件需要插件机制呢？ (PS:明河对插件的理解是非组件核心模块、功能逻辑独立、甚至可以脱离宿主运行的模块。) 
*   1.组件的适用场景非常复杂（这是最重要的理由，业务需求才是组件设计根本），不同场景需要不同逻辑处理模块，而这些模块是互斥的，一窝端的打包和引入，显得很笨拙
*   2.抽离组件非核心模块，转成插件，减少组件js大小，同时实现使用者的按需加载
*   3.更好的组织非核心模块，一样的加载、注入方式、获取方式、销毁方式
*   4.解耦组件子模块
*   5.你的组件希望更具有定制性，第三方开发者可以方便的扩展 （PS:如果你开发的组件很简单，适用的业务场景比较单一，请勿使用RichBase。） 接下来明河以Uploder(异步文件上传组件)为例，讲解下RichBase的使用。 

## 如何使用RichBase？

### STEP1:入口核心模块（一般是index.js）继承RichBase

<pre class='brush: javascript;'>KISSY.add(function (S,RichBase) {
    var Uploader = RichBase.extend([],{
        constructor:function (target, config) {

        }
    }, {ATTRS:{
        target:{
            value:EMPTY,
            getter:function (v) {
                return $(v);
            }
        }
    }}, 'Uploader');
    return Uploader;
}, {requires:['rich-base']});
</pre> 完整代码请看

<a href="https://github.com/kissygalleryteam/uploader/blob/master/1.4/index.js" target="_blank">index.js</a>。 RichBase的模块名称为`rich-base`。 `RichBase.extend(extensions，px，sx,name)`是其核心方法，用于创建继承于RichBase的组件核心类。 
*   extensions:这个参数不是必须的，如果你的类，除了继承于RichBase，还继承其他类，请配置此参数，留意是一个数组，比如`[UploaderCore]`
*   px:给类注入prototype属性和方法
*   sx:给类注入静态属性和方法，比如`ATTRS`（用于设置类的配置属性）
*   name:类构造函数名称，RichBase内部会动态的构建一个function作为类的构造函数，并且名字是"RichBaseDerived",要显示的指定名称，需要配置上这个参数

### STEP2:定义类的构造函数

<pre class='brush: javascript;'>var Uploader = RichBase.extend({
        /**
         *构造函数
        */
        constructor:function (target, config) {
            var self = this;
            //调用父类的构造函数，必须存在！
            Uploader.superclass.constructor.apply(self, config);

            self.set('target', target);
            self._init();
        },
        _init:function(){

        }
    }, {ATTRS:{ }}, 'Uploader');
</pre> 定义

`constructor`创建一个构造函数（RichBase内部调用）。 `Uploader.superclass.constructor.apply(self, config);`该语法非常重要，留意将config（组件配置）传给父类，这样才有办法处理`ATTRS`定义的属性。 `self.set('target', target);`设置Uploader的target属性，举例用法，非必须。 
### STEP3:用户调用组件 组件的调用跟其他组件一样。 

<pre class='brush: javascript;'>var uploader = new Uploader('#J_UploaderBtn',{
    action:"upload.php"
});
S.log(uploader.get('target'));
</pre>

### STEP4:定义类的属性和配置参数 前面已经提到过了，这里再强调，如果你使用过Base，请跳过，因为RichBase的处理方式是一样的。 

<pre class='brush: javascript;'>var Uploader = RichBase.extend({
        constructor:function () {

        }
    }, {ATTRS:{
        target:{
            value:EMPTY,
            getter:function (v) {
                return $(v);
            }
        },
        multiple:{
            value:false,
            setter:function (v) {
                var self = this, button = self.get('button');
                if (!S.isEmptyObject(button) && S.isBoolean(v)) {
                    button.set('multiple', v);
                }
                return v;
            }
        },
        autoUpload:{value:true}
    }}, 'Uploader');
</pre> Base相对于传统的获取/设置属性的优势是你可以配置getter方法和setter方法，比如uploader.get('target')，那么自动会调用getter方法，uploader.set('multiple',true)，会自动调用setter方法。 也就是说，用户只要get/set就可以完成对应的逻辑，不用额外定义multiple方法（uploader.multiple(true)），简化了组件API。 当然Base的好处不止如此，详细请看

<a href="http://docs.kissyui.com/docs/html/api/core/base/index.html" target="_blank">文档</a>。 
### STEP5:定义组件插件 我们一般把组件放在

`plugin`目录下。 一个<a href="https://github.com/kissygalleryteam/uploader/blob/master/1.4/plugins/imageZoom/imageZoom.js" target="_blank">典型的插件代码</a>： <pre class='brush: javascript;'>KISSY.add('gallery/uploader/1.4/plugins/imageZoom/imageZoom',function(S, Node, Base) {
    var EMPTY = '';
    var $ = Node.all;

    function ImageZoom(config) {
        var self = this;
        //调用父类构造函数
        ImageZoom.superclass.constructor.call(self, config);
    }
    S.extend(ImageZoom, Base, /** @lends ImageZoom.prototype*/{
        /**
         * 插件初始化
         */
        pluginInitializer : function(uploader) {
            if(!uploader) return false;

        },
        /**
         * 析构方法，可以不存在
         */
        pluginDestructor:function(){

        }
    }, {ATTRS : /** @lends ImageZoom*/{
        /**
         * 插件名称
         * @type String
         * @default urlsInput
         */
        pluginId:{
            value:'imageZoom'
        }
    }});
    return ImageZoom;
}, {requires : ['node','base']});
</pre> 插件功能比较简单，继承Base即可。 

`pluginInitializer`是内部方法关键字，宿主为自动调用该方法，只有一个参数（宿主类的实例）。 `pluginId`为插件名称，必须存在！用户获取插件时根据这个名称来。 
### STEP6:插件注入到宿主类

<pre class='brush: javascript;'>S.use('gallery/uploader/1.4/index', function (S, Uploader) {
        S.use('gallery/uploader/1.4/plugins/imageZoom/imageZoom',function(S,ImageZoom){
            var uploader = new Uploader('#J_UploaderBtn');
            //初始化插件
            uploader.plug(new ImageZoom()) ;
        });
    })

</pre>

`plug`方法，将在宿主实例与插件实例（或方法）之间建立联系。所有的插件都会放在`plugins`数组内。 如果插件很多，这样一个一个处理很麻烦，可以直接配置`plugins`属性。 <pre class='brush: javascript;'>S.use('gallery/uploader/1.4/index', function (S, Uploader) {
        S.use('gallery/uploader/1.4/plugins/imageZoom/imageZoom',function(S,ImageZoom){
            var uploader = new Uploader('#J_UploaderBtn',{
                plugins:[new ImageZoom()]
            });
        });
    })
</pre>

### STEP7:获取/销毁插件 使用

`getPlugin(id)`方法来获取插件实例，id为插件定义的`pluginId`。 <pre class='brush: javascript;'>var uploader = new Uploader('#J_UploaderBtn',{
        plugins:[new ImageZoom()]
    });

    var imageZoom = uploader.getPlugin('imageZoom');
</pre> 使用

`unplug()`销毁插件，会调用插件的pluginDestructor析构函数。 
## RichBase的额外特性

### listeners参数用于批量监听组件事件

<pre class='brush: javascript;'>var uploader = new Uploader('#J_UploaderBtn',{
        listeners:{
            select:function(ev){

            },
            success:function(ev){

            }
    });
</pre> 如何指定事件的上下文呢？ 

<pre class='brush: javascript;'>var uploader = new Uploader('#J_UploaderBtn',{
        listeners:{
            error:{
                fn:function(ev) {

                },
                context:uploader
            }
    });
</pre>

### 析构函数

<pre class='brush: javascript;'>var Uploader = RichBase.extend({
        constructor:function (target, config) {

        },
        destructor:function(){

        }
    }, {ATTRS:{ }}, 'Uploader');
</pre>

## 总结 RichBase的介绍就到这里，有疑问的同学可以给明河留言，使用RichBase务必考虑业务场景哦。

