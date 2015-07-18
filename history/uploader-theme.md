# 如何制作属于自己的主题—KF/Uploader快速使用指南
=======

根据这段时间，各个uploader需求者发给明河的设计图来看，uploader的差异性非常大，不是只要定制下皮肤（css）就可以搞定的，有各种差异性的行为。 <a href="http://www.36ria.com/5137/uploader-theme-1" rel="attachment wp-att-5139"><img src="http://www.36ria.com/wp-content/uploads/2012/03/uploader-theme-1.png" alt="" title="uploader-theme-1" width="643" height="176" class="alignnone size-full wp-image-5139" /></a> imageUploader主题最早来源于苏河同学的love.taobao.com（爱逛街）的需求。 <a href="http://www.36ria.com/5137/uploader-theme-2" rel="attachment wp-att-5140"><img src="http://www.36ria.com/wp-content/uploads/2012/03/uploader-theme-2.png" alt="" title="uploader-theme-2" width="371" height="90" class="alignnone size-full wp-image-5140" /></a> default主题来源于明河的refund.taobao.com（退款）的需求。 <a href="http://www.36ria.com/5137/uploader-theme-3" rel="attachment wp-att-5141"><img src="http://www.36ria.com/wp-content/uploads/2012/03/uploader-theme-3.png" alt="" title="uploader-theme-3" width="596" height="207" class="alignnone size-full wp-image-5141" /></a> lineQueue主题来源于紫英同学的ershou.taobao.com（二手市场）的需求 <a href="http://www.36ria.com/5137/uploader-theme-4" rel="attachment wp-att-5142"><img src="http://www.36ria.com/wp-content/uploads/2012/03/uploader-theme-4.jpg" alt="" title="uploader-theme-4" width="566" height="208" class="alignnone size-full wp-image-5142" /></a> 林谦同学正在开发 <a href="http://www.36ria.com/5137/uploader-theme-5" rel="attachment wp-att-5143"><img src="http://www.36ria.com/wp-content/uploads/2012/03/uploader-theme-5.png" alt="" title="uploader-theme-5" width="351" height="160" class="alignnone size-full wp-image-5143" /></a> lemonlwz306正在开发仿微博图片上传的主题。 **这篇教程明河将以ImageUploader的拷贝主题为例讲解如何使用制作属于自己的uploader主题。** 我们将这个本地主题命名为**newImageUploader**。 猛击[download id="229"]下载demo。 **（PS:1.1.2后组件有做了部分修改，文章页修改了部分内容。）** 假设目录结构如下图： <a href="http://www.36ria.com/5137/make-theme-demo" rel="attachment wp-att-5146"><img src="http://www.36ria.com/wp-content/uploads/2012/03/make-theme-demo.png" alt="" title="make-theme-demo" width="170" height="183" class="alignnone size-full wp-image-5146" /></a> 每个主题都必须有个index.js，而style.css主题样式，可以自行通过index.js中的配置更换（满足换肤的需求）。 
#### 主题js模板 （可以打开demo包中的theme-tpl.js文件） 

##### 1.你需要定义下主题模块路径，和模块依赖

<pre class='brush: js; '>KISSY.add('make-theme-demo/newImageUploader/index', function (S, Node, Theme, ProgressBar) {

}, {requires:['node', 'gallery/form/1.1/uploader/theme', 'gallery/form/1.1/uploader/plugins/progressBar/progressBar']});
</pre> 主题模块路径非常重要，要根据你本地的实际情况自行配置。比如示例包中的

**'make-theme-demo/newImageUploader/index'**。 **requires**定义模块依赖，** 'gallery/form/1.1/uploader/theme'**是主题基类，必不可少，定制的主题必须继承这个基类。 <del datetime="2012-04-10T09:28:52+00:00"><strong>'gallery/form/1.1/uploader/plugins/progressBar/progressBar'</strong>是进度条插件路径，留意模块路径起始为gallery，内置主题使用的是'../../'相对路径。</del> V1.1.2更新：无需在requires中引用插件模块，做了简化处理，只要配置下主题的plugins属性，比如： <pre class='brush: js; '>/**
         * 需要加载的插件，需要手动实例化
         * @type Array
         * @default ['preview','progressBar','filedrop'] 图片预览、进度条、文件拖拽
         */
        plugins:{
          value:['preview','progressBar','filedrop']
        }
</pre>

##### 2.主题构造函数

<pre class='brush: js; '>/**
     * @name NewImageUploader
     * @class 图片上传本地主题demo
     * @constructor
     * @extends Theme
     * @requires Theme
     * @requires  ProgressBar
     * @author 明河
     */
    function NewImageUploader(config) {
        var self = this;
        //调用父类构造函数
        NewImageUploader.superclass.constructor.call(self, config);
    }

    S.extend(NewImageUploader, Theme, /** @lends NewImageUploader.prototype*/{
  
    }, {ATTRS:/** @lends NewImageUploader.prototype*/{

    }});
    return NewImageUploader;
</pre> 构造函数必须继承Theme类，Theme继承于KISSY的Base，所以使用

**ATTRS**来定义属性。如果你要获取主题属性，就必须使用get()。 
##### 3.主题必备方法

<pre class='brush: js; '>S.extend(NewImageUploader, Theme, /** @lends NewImageUploader.prototype*/{
        /**
         * 在上传组件运行完毕后执行的方法（对上传组件所有的控制都应该在这个函数内）
         * @param {Uploader} uploader
         */
        afterUploaderRender:function (uploader) {

        },
        /**
         * 获取状态容器
         * @param {KISSY.NodeList} target 文件的对应的dom（一般是li元素）
         * @return {KISSY.NodeList}
         */
        _getStatusWrapper:function (target) {

        },
        /**
         * 文件处于等待上传状态时触发
         */
        _waitingHandler:function (ev) {

        },
        /**
         * 文件处于开始上传状态时触发
         */
        _startHandler:function (ev) {

        },
        /**
         * 文件处于正在上传状态时触发
         */
        _progressHandler:function (ev) {

        },
        /**
         * 文件处于上传成功状态时触发
         */
        _successHandler:function (ev) {

        },
         /**
         * 文件处于上传错误状态时触发
         */
        _errorHandler:function (ev) {

        }
    })
</pre>

**afterUploaderRender()**非常重要，由于主题是异步载入的，Uploader实例化时机是有延迟的，当主题加载结束，Uploader实例成功后，组件会自动调用主题的afterUploaderRender方法。 所以对**Uploader**和**Queue**的事件监听，都必须在这个函数。 比如： 监听queue的添加事件 <pre class='brush: js; '>afterUploaderRender:function (uploader) {
            var self = this,
                queue = self.get('queue');
            queue.on('add',self._queueAddHandler,self);
        }
</pre>

**_getStatusWrapper()**是主题用于获取状态容器的定义，所谓状态容器，指的是主题html模板中的状态提示的容器，比如 <pre class='brush: xml; '><div class="status-wrapper J_FileStatus">
  <div class="status waiting-status tips-upload-waiting" style="display: none;">
    <p class="tips-text">
      等待上传，请稍候
    </p>
            
  </div>
          
  
  <div class="status start-status progress-status tips-uploading" style="display: none;">
    &lt;div class="J_ProgressBar_file-94 ks-progress-bar" style="width: 100px;" role="progressbar"
                     aria-valuemin="0" aria-valuemax="100" aria-valuenow="0">
                    <div data-value="0" class="ks-progress-bar-value">
      
    </div>
                
  </div>
          
</div>
        

<div class="status success-status tips-upload-success" style="">
  上传成功！
</div>
        

<div class="status error-status tips-upload-error" >
  &lt;p
                  class="J_ErrorMsg_file-94 tips-text">上传失败，请重试！&lt;/p>
</div>
    &lt;/div>
</pre> class="J_FileStatus"对应的div就是状态容器。 

**waitingHandler/startHandler**等一系列的状态监听器，会在文件改变状态后触发，可以在每个监听器内打个S.log，体会下整个过程。状态监听器的作用在于主题开发者，无需手动监听uploader的各个事件，主题拥有自动触发对应监听器的能力。 
##### 主题属性配置

<pre class='brush: js; '>{ATTRS:/** @lends NewImageUploader.prototype*/{
        /**
         *  主题名（文件名），此名称跟样式息息相关
         * @type String
         * @default "newImageUploader-queue"
         */
        name:{value:'newImageUploader'},
        /**
         * 是否引用css文件
         * @type Boolean
         * @default true
         */
        isUseCss:{value:true},
        /**
         * css模块路径
         * @type String
         * @default "gallery/form/1.1/uploader/themes/imageUploader/style.css"
         */
        cssUrl:{value:'make-theme-demo/newImageUploader/style.css'},
        /**
         * 队列使用的模板
         * @type String
         * @default ""
         */
        fileTpl:{value:
            '<li id="queue-file-{id}" class="clearfix" data-name="{name}">
  ' +
                  '<div class="tb-pic120">
    ' +
                        '<a href="javascript:void(0);"><img class="J_Pic_{id}" src="" /></a>' +
                    '
  </div>' +
                  '
  
  <div class=" J_Mask_{id} pic-mask">
    
  </div>' +
                  '
  
  <div class="status-wrapper J_FileStatus">
    ' +
                        '<div class="status waiting-status tips-upload-waiting">
      <p class="tips-text">
        等待上传，请稍候
      </p>
    </div>' +
                        '
    
    <div class="status start-status progress-status tips-uploading">
      ' +
                              '<div class="J_ProgressBar_{id}">
        &lt;s class="loading-icon">&lt;/s>上传中...
      </div>' +
                          '
    </div>' +
                        '
    
    <div class="status success-status tips-upload-success">
      ' +
                            '上传成功！' +
                          '
    </div>' +
                        '
    
    <div class="status error-status tips-upload-error">
      ' +
                              '<p class="J_ErrorMsg_{id} tips-text">
        上传失败，请重试！
      </p>
    </div>' +
                    '
  </div>' +
                  '
  
  <a class="J_Del_{id} del-pic" href="#">删除</a>' +
              '
</li>'
        }，
        /**
         * 需要加载的插件，需要手动实例化
         * @type Array
         * @default ['preview','progressBar','filedrop'] 图片预览、进度条、文件拖拽
         */
        plugins:{
            value:['preview','progressBar','filedrop']
        }
    }
</pre>

*   name：主题名，务必设置，组件会自动将此名拼给队列，比如queue会是newImageUploader-queue
*   isUseCss：是否自动加载css样式，为了减少请求数，可以设置为false，然后在项目css中加上上传样式
*   cssUrl：非常重要，css的路径名，留意不需要完整路径
*   fileTpl：主题模板，留意这里{id}的用法，用于取指定节点非常有用
*   **plugins**：1.1.2版新增属性，配置要加载的插件，用户无需手动在**requires**中引用插件路径

#### 主题调用 配置包路径： 

<pre class='brush: js; '>var S = KISSY;
        S.config({
            packages:[
                {
                    name:"gallery",
                    path:"http://a.tbcdn.cn/s/kissy/",
                    charset:"utf-8"
                }
            ]
        });
        S.config({
            packages:[
                {
                    name:"make-theme-demo",
                    path:"http://localhost/",
                    charset:"utf-8"
                }
            ]
        });
</pre> 初始化组件 

<pre class='brush: js; '>S.use('gallery/form/1.1/uploader/index', function (S, RenderUploader) {
            new RenderUploader('#J_UploaderBtn', '#J_UploaderQueue');
        })
</pre> 配置伪属性： 

<pre class='brush: xml; '><div id="J_UploadBg" class="unupload-area">
  &lt;a class="unupload" id="J_UploaderBtn"
                     data-config='{
                               "theme" : "make-theme-demo/newImageUploader",
                               "urlsInputName":"urlsInput",
                               "serverConfig":{"action":"./upload.php","data":{"iid":77010,"fieldId":132338},"dataType" : "json"},
                               "name":"Filedata"
                         }'
                     data-auth='{
                               "require":[true,"必须至少上传一个文件！"],
                                          "maxSize":[500, "文件大小为{size}，文件太大！"],
                                          "allowExts":[
                                              {"desc":"JPG,JPEG,PNG,GIF,BMP", "ext":"*.jpg;*.jpeg;*.png;*.gif;*.bmp"},
                                              "文件格式错误！"],
                                          "max":[5,"最多允许上传五张图片！"]
                         }'
                          >
                      <span class="count"> 还可以上传<em id="J_UploadCount">5</em>图片！ </span>
                      &lt;s class="bg">&lt;/s>
                  &lt;/a>
              
</div>
</pre> 留意

**"theme" : "make-theme-demo/newImageUploader"**。 下一篇教程，明河将深入主题的逻辑，讲解主题和uploader个API的交互。

