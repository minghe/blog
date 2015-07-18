# Auth—最强大的表单校验组件
=======

明河最近各种忙，其中一个工作就是完成表单校验组件，现在终于beta发布了！欢迎大家试用。 Auth的1.5前的版本由我的同事张挺完成，1.5明河做了重构，现在的Auth无与伦比的强大！（自夸下，O(∩_∩)O哈！） 
## Auth档案：

*   版本：1.5
*   文档：<a href="http://gallery.kissyui.com/auth/1.5/guide/index.html" target="_blank">http://gallery.kissyui.com/auth/1.5/guide/index.html</a>
*   demo：<a href="http://gallery.kissyui.com/auth/1.5/demo/index.html?spm=0.0.0.0.bgVbOk" target="_blank">http://gallery.kissyui.com/auth/1.5/demo/index.html?spm=0.0.0.0.bgVbOk</a>
*   作者：张挺（V1.4）| 明河（V1.5+）
*   基于kissy1.3

### Auth的特性

*   自定义校验规则
*   支持异步校验
*   支持异步上传组件校验
*   支持html5的规则配置方式
*   内置常用的校验规则
*   可以自由控制字段配置
*   自由的规则定义方式
*   自带校验消息展示
*   简单的调用和配置方式
*   ... Auth可以满足大部分的表单校验需求，欢迎大家试用。 

## Auth的简单使用

### 引用

<pre class='brush: xml;'><script src="http://g.tbcdn.cn/kissy/k/1.3.0/kissy-min.js" charset="utf-8"></script>
<link rel="stylesheet" href="http://g.tbcdn.cn/kissy/k/1.3.0/??css/dpl/base-min.css,css/dpl/forms-min.css,button/assets/dpl-min.css" />
</pre>

### HTML 表单的结构如下： 

<pre class='brush: xml;'></pre>

### 初始化组件

<pre class='brush: javascript;'>S.use('gallery/auth/1.5/,gallery/auth/1.5/lib/msg/style.css', function (S, Auth) {
    var auth = new Auth('#J_Auth');
    auth.render();
})
</pre> 详细的使用说明请看

<a href="http://gallery.kissyui.com/auth/1.5/demo/index.html?spm=0.0.0.0.bgVbOk" title="文档" target="_blank">文档</a>。 
## Auth的设计

<a href="http://www.36ria.com/6247/%e8%ae%be%e8%ae%a1" rel="attachment wp-att-6248"><img src="http://www.36ria.com/wp-content/uploads/2013/09/设计.png" alt="设计" width="630" height="263" class="alignnone size-full wp-image-6248" /></a> 
## Auth 独门秘籍

### 自由的规则定义方式 业界大部分的规则都是做成了Object配置形式，比如： 

<pre class='brush: javascript;'>demo.addRule([{
        ele:".inputxt:eq(0)",
        datatype:"zh2-4"
    },
    {
        ele:".inputxt:eq(1)",
        datatype:"*6-20"
    }]);
</pre> 这种结构化的方式配置起来很简单，但遗憾的是只能满足70%的普通需求，表单的业务场景是非常复杂的，ajax异步校验，加个"callback"字段，如果要适用异步上传的校验呢？如果需要延迟3秒校验呢？再加个字段？ 结构化的设计在表单校验中是错误的，低估了表单业务复杂性。 所以Auth使用Function的方式，用户可以在规则函数中写任何校验逻辑，甚至异步校验逻辑，只要保证函数的返回值符合auth要求即可。 比如下面的代码： 

<pre class='brush: javascript;'>auth.register('later-max',function(value,attr,defer){
        var self = this;
        S.later(function(){
            if(value &lt; attr){
                defer.resolve(self);
            }else{
                defer.reject(self);
            }
        },3000);
        return defer.promise;
    })
</pre> 比如随意设置错误消息： 

<pre class='brush: javascript;'>auth.register('min',function(value,attr,defer){
        if(!this.msg('error')) this.msg('error','消息随便设置');
        return Number(value) > 0;
    })
</pre> 更强大的是，你可以通过rule回溯到Field和Auth。 比如当你校验这个规则时，希望校验另外一个规则： 

<pre class='brush: javascript;'>auth.register('min',function(value,attr,defer){
        return Number(value) > 0;
    })

    auth.register('len',function(value,attr,defer){
        var field = this.get('field');
        field.test('min');
        return Number(value) > 3;
    })

</pre>

### 异步校验的支持 Auth的校验基于promise模式，所以非常友好的支持异步校验。 看个

<a href="http://gallery.kissyui.com/auth/1.5/demo/asyn_test.html" target="_blank">异步校验的demo</a>。 <pre class='brush: javascript;'>auth.register('user', function (value,attr,defer,field) {
        var self = this;
        io.get("./user_success.json",'json').then(function(result){
            var data = result[0];
            //用户名
            var name = data.name;
            if(name == 'minghe'){
                //验证成功，传送数据
                defer.resolve(self);
            }else{
                //验证失败，一样传送数据
                defer.reject(self);
            }
        });
        //特别注意：不同于同步校验，这里返回promise
        return defer.promise;
    });
</pre>

`ruleFunction`有第三个参数：<a href="http://docs.kissyui.com/docs/html/api/component/promise/defer.html" target="_blank">defer</a>，在ruleFunction中，你可以自由的写异步处理逻辑，但需要注意二点： 
*   返回值必须是`defer.promise`
*   异步加载成功后，如果校验成功调用下`defer.resolve(self)`，校验失败调用下`defer.reject(self)`

### 可以配合异步上传组件使用 请看

<a href="http://gallery.kissyui.com/auth/1.5/demo/rule_msg.html" target="_blank">demo</a> Uploader（异步上传组件），需要个hidden来存储服务器返回的url，Auth进行校验时只要对这个hidden进行校验即可。 Uploader自带了格式、数量等验证，一般只需验证required，有特殊验证需求可以注册个自定义规则。 <pre class='brush: xml;'><input type="file" class="g-u" id="J_UploaderBtn" value="上传文件" name="Filedata" />
    <input type="hidden" id="J_Urls" name="urls" value="http://test.jpg" required required-msg="必须上传一个文件！" />
</pre> 来看个场景：必须上传二个文件 

<pre class='brush: javascript;'>auth.register('uploader-limit',function(value,attr){
        var limit = Number(attr);
        var queue = uploader.get('queue');
        var count = queue.get('files').length;
        return count == limit;
    })
</pre> （ps：uploader为Uploader实例） 验证配置如下： 

<pre class='brush: xml;'><input type="hidden" id="J_Urls" name="urls" value="http://test.jpg" uploader-limit="2" uploader-limit-msg="必须只能上传二个文件！" />
</pre> 还必须监听Uploader的事件，触发验证。 

<pre class='brush: javascript;'>uploader.on("success add",function(){
        //触发hidden的验证
        var urlHidden = auth.getField('J_Urls');
        urlHidden.test();
    })
</pre> 除了上述功能，明河还做了非常多的优化，详细请看文档和

<a href="https://github.com/kissygalleryteam/auth" target="_blank">changelog</a>。

