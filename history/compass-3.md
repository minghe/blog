# compass实战—sprite
=======


做sprite（雪碧图）是对于前端来说一件痛苦耗时的事，比如2014版淘宝首页做的sprite：<http://gtms03.alicdn.com/tps/i3/T1wTwXFQFXXXcLNDLI-400-500.png>，切这样规整的图（定位时保证偏移都是整数...），我的leader遇春大概花了大半天时间。也许亲会说，傻X，为什么不用compass的sprite工具。实际上项目初始我们是是使用compass的sprite，不过在做retina X2时遇到麻烦，容后再禀。

## 如何使用compass快速生成sprite

compass官网上有篇简单的[教程][1]可以看下，腾讯的同学也写了个[视频教程][2]很不错，也推荐下。

图片目录结构如下：

<a href="http://www.36ria.com/6444/compass-sprite-1" rel="attachment wp-att-6447"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-1.png" alt="compass-sprite-1" width="287" height="181" class="alignnone size-full wp-image-6447" /></a>

s目录下存在我们项目所有需要sprite的图片。

### 新建个sprite.sass:

    @import "s/*.png"
    @include all-s-sprites
    

**@import "s/*.png"**： 引入s目录下的所有png图片，s是目录名，相当于命名空间，在compass称之为 **map**，非常重要。

**@include all-s-sprites**：生成sprite css代码

### grunt配置：

        compass: {
            sprite: {
                options: {
                    sassDir: '<%= srcBase %>',
                    specify: '<%= srcBase %>/sprite.sass',
                    cssDir : '<%= srcBase %>',
                    imagesDir: "<%= srcBase %>/images"
                }
            }
        }
    

跟前面的例子相比，增加了imagesDir的配置，用于指定图片所在的目录，如果不指定，默认使用工程的根目录下的images目录，通常我们希望图片也放在src目录下。

运行 **grunt** 命令后：

<a href="http://www.36ria.com/6444/compass-sprite-2" rel="attachment wp-att-6449"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-2.png" alt="compass-sprite-2" width="402" height="138" class="alignnone size-full wp-image-6449" /></a>

在src/images下生成s-sf7db58bae0.png图片：

<a href="http://www.36ria.com/6444/compass-sprite-3" rel="attachment wp-att-6450"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-3.png" alt="compass-sprite-3" width="158" height="170" class="alignnone size-full wp-image-6450" /></a>

我们看下sprite.css的代码：

        /* line 66, s/*.png */
        .s-sprite, .s-adults, .s-arrow-blue, .s-clock, .s-discover-logo {
          background: url('/src/images/s-sf7db58bae0.png') no-repeat;
        }
    
        .s-adults {
          background-position: 0 -40px;
        }
    
        .s-arrow-blue {
          background-position: 0 -57px;
        }
    
        .s-clock {
          background-position: 0 -72px;
        }
    
        .s-discover-logo {
          background-position: 0 0;
        }
    

compass自动帮我们配置好icon的background-position！so easy！

这是好的开始，不过别急，在实际应用中你会碰到各种问题。

### 图片路径如何使用服务器路径？

项目发布后，图片的路径显然不能是 **/src/images/** 这样的相对路径，我们需要配置下服务器路径。

            sprite: {
                options: {
                    sassDir: '<%= srcBase %>',
                    specify: '<%= srcBase %>/sprite.sass',
                    cssDir : '<%= srcBase %>',
                    imagesDir: "<%= srcBase %>/images",
                    httpPath:"http://www.36ria.com/css"
                }
            }
    

增加 **httpPath** 配置项，用于指定生成的sprite图片在服务器上的路径。比如这里指定 **httpPath:"http://www.36ria.com/css"**，生成的sprite.css中的路径代码也会相应修改：

        .s-sprite, .s-adults, .s-arrow-blue, .s-clock, .s-discover-logo {
          background: url('http://www.36ria.com/css/src/images/s-sf7db58bae0.png') no-repeat;
        }
    

看上去不错，但httpPath的配置又引入一个新的问题。compass生成的sprite图片文件名是随机码，每次生成都是不一样的，为了保证生成后可以访问，我们每次需要提交新的sprite图片到服务器，这样很麻烦，不利于我们开发。所以建议加个开发环境和生线上环境区分配置，开发环境无需配置httpPath。

### 如何控制图标之间的空隙？

默认生成的sprite图片是紧密排列的，这在部分浏览器上可能会有问题，比如IE6，因为怪异的盒模型解析，会有多余的高度，导致会显示多余图标部分，所以我们一般会把图标之间的间隙拉大。

我们在sprite.sass增加一行配置变量：

    $s-spacing: 20px
    

针对map：s，配置图标之间的间隙为20像素：

<a href="http://www.36ria.com/6444/compass-sprite-4" rel="attachment wp-att-6453"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-4.png" alt="compass-sprite-4" width="82" height="142" class="alignnone size-full wp-image-6453" /></a>

### 如何让生成的代码自动带上图标的宽度和高度

一般我们会定义个图标容器：

    <s class="icon s-adults"></s>
    

为了正常显示图标，我们需要做些配置。

将其设置为inline-block：

    .icon
      +inline-block
    .s-adults
      $width: 100px
      $height: 20px
    

给每个图标都配置宽度和高度，是非常麻烦的事，有没有办法自动生成呢？

答案是Yes！

sprite.sass 增加个配置：

    $s-sprite-dimensions: true
    

MAP-sprite-dimensions开启时，会自动给图标选择器加上宽度高度：

    .s-adults {
      background-position: 0 -60px;
      height: 17px;
      width: 80px;
    }
    

### 如何获取图标的宽度和高度？

有时我们希望获取图标的宽度和高度，方便我们做容器大小的计算，如何获取呢？

    .clock-wrapper
      width: image-width("s/clock.png")
      height: image-height("s/clock.png")
    

### hover和active状态需要改变图标，如何处理呢？

比如hover的图标名称叫adults.png，那么hover的图标起名叫adults_hover.png即可。compass会自动处理，生成的css代码如下：

        .s-adults:hover, .s-adults.adults_hover, .s-adults.adults-hover {
          background-position: 0 -97px;
        }
    

active也可以这么处理。

### 如何让图标以Base64编码的方式插入css文件中？

如果你的图标很少，大可将图标直接编入css文件。

    .line-image
          background-image: inline-image("s/clock.png")
    

使用 inline-image()即可，生成的css内容如下：

        .line-image {
             background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAKCAMAAAC67D+PAAAABGdBTUEAAK/INwWK6QAAABl0RVh0U29mdHdhcmUAQWRvYmUgSW1hZ2VSZWFkeXHJZTwAAAAJUExURd7e3jo6Ov///2aV1WYAAAArSURBVHjaYmBihAImBkYGJjAAMhiBFAgz4WUCNUGZTAhRGBNhGMIKgAADACFjAJu95pwTAAAAAElFTkSuQmCC');
         }
    

sprite的基础使用已经讲完，下一篇我们继续学习sprite的应用，比如retina X2的处理。

 [1]: http://compass-style.org/help/tutorials/spriting/
 [2]: http://www.kuaipan.cn/file/id_48820047145120357.htm?source=1
 