# 制作一个类似 Instagram的图片滤镜web App（上）
=======

![][1] 原文看：<http://tutorialzine.com/2013/02/instagram-filter-app/>，明河扩写了下。 这篇教程会非常有意思， Instagram或美图秀秀或camera360的照片滤镜功能想必大家都完成，这篇教程将教大家使用html5的知识，实现类似功能。 <ul class="tow-columns clearfix">
  <li class="l">
    <a target="_blank" class="btn-view-demo" href="http://demo.36ria.com/html5/instagram-filter-app/index.html">点此查看demo</a>
  </li>
  <li class="l">
    [download id="234"]
  </li>
</ul> 源码可以到

<a href="https://github.com/minghe/36ria-demo/tree/master/html5/instagram-filter-app" target="_blank">明河的github上查看</a> 通过这篇教程你将学到什么？ 
*   [Filereader.js][2]的使用，明河也会讲解到html5中 FileReader的API
*   [Caman.js][3]，非常牛逼的 `canvas `库，用于实现图片滤镜效果
*   jquery的使用
*   [jQuery Mousewheel][4]的使用 请不要用过时的浏览器运行这个demo，亲们懂的... 

## 创建一个源图片容器

<pre class='brush: xml; '><div id="photo">
  
</div>
</pre> #photo层用于创建一个拖拽区域和canvas容器。 

<!--设置max-height和max-width，防止容器被大图片撑破。--> 从demo来看，在容器中有"拖拽一个图片到这里"的文案，但html代码中并不存在，这里我们来学习个css知识

`:after`伪类。 <pre class='brush: css; '>#photo:after{
    content: '拖拽一个图片到这里';
    top: 150px;
    z-index: 1;
    color: #ccc;
    display: block;
    font-size: 20px;
    position: absolute;
    width: 100%;
    letter-spacing: 1px;
}
</pre>

`:after`伪类会在容器元素内容的最后面插入指定的内容，`content`属性必须存在！ 插入的为inline行内元素，所以这里使用了`display: block;`。 
## 给容器增加拖拽读取图片逻辑 这里需要用到名为

<a href="http://bgrins.github.com/filereader.js/" target="_blank">FileReader.js</a>的jquery插件，FileReader.js可以实现创建一个拖拽层，并暴露出了方便读取文件数据API。 引入jquery和FileReader插件： <pre class='brush: xml; '><script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
<script src="assets/js/filereader.min.js"></script>
</pre>

### 使用方法：

<pre class='brush: javascript; '>var photo = $('#photo');
    //调用FileReader插件
    photo.fileReaderJS({
        //监听拖拽的各个事件
        on:{

            beforestart: function(e, file) {
                // 如果你想要跳过这个文件直接 return false
            },
            loadstart: function(e, file) {

            },
            progress: function(e, file) {

            },
            load: function(e, file) {

            },
            error: function(e, file) {
            },
            loadend: function(e, file) {
            },
            abort: function(e, file) {
            },
            skip: function(e, file) {
                // 当一个文件被跳过处理时触发
            }
        }
    })
</pre> FileReader的

`on`参数非常重要，内置所有拖拽和读取文件事件，比如常用的`load`事件（读取完文件后触发）。 FileReader支持的事件非常丰富，基本上可以控制整个拖拽读取文件的过程。 接下来我们利用FileReader来实现拖拽读取图片信息的功能。 <pre class='brush: javascript; '>var photo = $('#photo');
    //调用FileReader插件
    photo.fileReaderJS({
        //监听拖拽的各个事件
        on:{

            beforestart: function(e, file) {
                //只接受图片
				return /^image/.test(file.type);
            },
            load: function(e, file) {

            }
        }
    })
</pre> 图片滤镜只能对图片起作用，所以我们需要将图片外的文件过滤掉。 

<pre class='brush: javascript; '>beforestart: function(e, file) {
        //只接受图片，通过过滤file数据中的type属性
        return /^image/.test(file.type);
    }
</pre> 将读取图片并插入到#photo层内 

<pre class='brush: javascript; '>load: function(e, file) {
        //向拖拽容器添加一个图片元素
        var img = $('<img />').appendTo(photo);
        //图片读取成功后触发，这样才能找到图片原始宽度和高度
        img.load(function(){
            //...
        });
        // 设置图片的src，直接读取二进制图片数据
        // 触发img的load事件
        img.attr('src', e.target.result);
    }
</pre>

`load`事件有二个参数： 
*   e：事件对象，带有文件的二进制数据(base64)，通过`e.target.result`获得；
*   file：文件数据对象 在高级浏览器（IE6之类的滚粗就不说了）下图片的src是可以直接设置为base64编码的二进制数据，格式类似如下： 

<pre class='brush: xml; '><img src="data:image/gif;base64,R0lGODlhAwADAIABAL6+vv///yH5BAEAAAEALAAAAAADAAMAAAIDjA9WADs=" />
</pre> 这是html5实现本地图片预览的核心。 （PS:css中的background-image也是支持base64编码的二进制数据。） 详细内容，明河推荐看

<a href="http://www.zhangxinxu.com/wordpress/?p=2341" target="_blank"></a>《base64:URL背景图片与web页面性能优化》。 img读取图片完毕会触发load事件，这是才能取到图片的原始尺寸！而且load函数请写在`src`设置之前！。 接下来处理下图片尺寸过大的情况： <pre class='brush: javascript; '>//图片宽度和高度
    var imgWidth,imgHeight;
    //图片允许的最大宽度和高度
    var	maxWidth = 500, maxHeight = 500；
    var newHeight,newWidth;
    var ratio;
    //图片读取成功后触发，这样才能找到图片原始宽度和高度
    img.load(function(){
        imgWidth  = this.width;
        imgHeight = this.height;
        // 控制在500*500px内
        if (imgWidth >= maxWidth || imgHeight >= maxHeight) {
            if (imgWidth > imgHeight) {
                //ratio是希望处理图片时，依旧可以保证比例的正确
                ratio = imgWidth / maxWidth;
                newWidth = maxWidth;
                newHeight = imgHeight / ratio;

            } else {
                ratio = imgHeight / maxHeight;
                newHeight = maxHeight;
                newWidth = imgWidth / ratio;
            }

        } else {
            newHeight = imgHeight;
            newWidth = imgWidth;
        }
    });
</pre> 下一篇教程将完成最有意思的部分：实现图片滤镜功能。 这里先放放，来听听明河唠叨下

`FileReaderJs的`的原理。 
## FileReaderJS的原理 FileReaderJS其实是对html5对象

[FileReader][5]的封装。FileReader用于读取文件数据，在html5的异步文件上传中有广泛的应用，所以这个API非常重要，也是为什么明河要唠叨的原因，推荐大家掌握。 典型使用： <pre class='brush: javascript; '>function readFile(){
        //FileList中的一个文件
        var file = this.files[0];
        var reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = function(e){
                result.innerHTML = '<img src="'+this.result+'" alt="" />'
        }
}
</pre>

`readAsDataURL`：核心方法，用于读取文件数据。 `onload`：事件，在读取文件成功后触发，通过读取result属性，可以获取到文件数据(base64编码)。 详细内容推荐大家阅读[《FileReader详解与实例---读取并显示图像文件》][6]。 <a href="http://www.36ria.com/6093/file-api" rel="attachment wp-att-6099"><img src="http://www.36ria.com/wp-content/uploads/2013/03/file-api.png" alt="file api" width="856" height="307" class="alignnone size-full wp-image-6099" /></a>

 [1]: http://s3.36ria.com/201303/4922/34116_o.jpg
 [2]: https://github.com/bgrins/filereader.js
 [3]: http://camanjs.com/
 [4]: https://github.com/brandonaaron/jquery-mousewheel
 [5]: http://dev.w3.org/2006/webapi/FileAPI/#dfn-filereader
 [6]: http://www.jsmix.com/blog/html5/file-reader.html
