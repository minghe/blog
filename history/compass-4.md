# compass实战—retina sprite
=======

## 什么是retina？

推荐一篇很好的科普文：[《走向视网膜（Retina）的Web时代》][2]，这里不再花大量篇幅说明。

### 我们的sprite雪碧图，为了适配 retina 需要做些什么呢？

1.  生成一张二倍大小的sprite雪碧图
2.  编写适配css，当时retina屏幕时使用x2的sprite图片

这二件事看似简单，却又不少门道在里面。

推荐二篇文章：[《Sass Mixins——支持Retina的Icons Sprite》][3]、[《The Ecstasy and Agony of Compass Sprite Generation, Part 2》][4]。

推荐二个compass mixin：[《Retina-Sprites-for-Compass》][5]、[《Retina-mixins-for-Compass》][6]。

## retina的适配方案

目前主流的retina的适配方案有二个：

### 使用媒体查询

[《Retina-Sprites-for-Compass》][5]、[《Retina-mixins-for-Compass》][6]使用的就是该方法，原理是：

对device-pixel-ratio属性进行判断，Retina的device-pixel-ratio值大于1，所以我们可以使用如下媒体查询判断：

    @media (min--moz-device-pixel-ratio: 1.3), (-o-min-device-pixel-ratio: 2.6/2), (-webkit-min-device-pixel-ratio: 1.3), (min-device-pixel-ratio: 1.3), (min-resolution: 1.3dppx){
    
    }
    

详细的compass的mixin代码请看[Retina-Sprites-for-Compass][7]。

媒体查询是不错的方案，但代码会比较冗余，可以看下demo [css代码][8]。

有没有更好的方式呢？

### 使用css4属性：image-set

推荐看一篇文章：[《image-set实现Retina屏幕下图片显示》][9],目前淘宝首页使用的是该方法，轻松可靠。

定义个简单的mixin：

    @mixin image-set($bg, $bg2x)
      background: url($bg)
      background-image: -webkit-image-set(url($bg) 1x, url($bg2x) 2x)
      background-image: -moz-image-set(url($bg) 1x, url($bg2x) 2x)
      background-image: -ms-image-set(url($bg) 1x, url($bg2x) 2x)
      background-image: -o-image-set(url($bg) 1x, url($bg2x) 2x)
    

图标代码：

    .s-adults
      +image-set("http://gtms01.alicdn.com/tps/i1/T1ye7BFUNbXXcLNDLI-400-500.png", "http://gtms03.alicdn.com/tps/i3/T1YqkOFwXiXXcR0pAD-800-1000.png")
    

第一个参数为x1图片地址，第二个参数为x2图片地址。浏览器会自己判断，当为retina屏幕时使用x2图片地址。

生成的css代码如下：

        .s-adults {
          background: url("http://gtms01.alicdn.com/tps/i1/T1ye7BFUNbXXcLNDLI-400-500.png");
          background-image: -webkit-image-set(url("http://gtms01.alicdn.com/tps/i1/T1ye7BFUNbXXcLNDLI-400-500.png") 1x, url("http://gtms03.alicdn.com/tps/i3/T1YqkOFwXiXXcR0pAD-800-1000.png") 2x);
          background-image: -moz-image-set(url("http://gtms01.alicdn.com/tps/i1/T1ye7BFUNbXXcLNDLI-400-500.png") 1x, url("http://gtms03.alicdn.com/tps/i3/T1YqkOFwXiXXcR0pAD-800-1000.png") 2x);
          background-image: -ms-image-set(url("http://gtms01.alicdn.com/tps/i1/T1ye7BFUNbXXcLNDLI-400-500.png") 1x, url("http://gtms03.alicdn.com/tps/i3/T1YqkOFwXiXXcR0pAD-800-1000.png") 2x);
          background-image: -o-image-set(url("http://gtms01.alicdn.com/tps/i1/T1ye7BFUNbXXcLNDLI-400-500.png") 1x, url("http://gtms03.alicdn.com/tps/i3/T1YqkOFwXiXXcR0pAD-800-1000.png") 2x);
        }
    

可以看到比媒体查询代码简练很多，而且很容易理解，不用额外处理背景位置、缩放等问题所以下面使用的是第二种方案。

## retina的适配过程

demo代码请看[github][10]

### x2的图片如何生成？

在[《Sass Mixins——支持Retina的Icons Sprite》][3]中有说到使用[ImageMagick][11]来生成x2和x1的icon图片，明河不推荐这么处理，因为不符合现实的设计流程。设计师做页面设计的时候，不大可能先从x2尺寸设计稿着手，而且icon的尺寸不大可能都是固定尺寸，比如32x32与64x64，所以试图使用ImageMagick来自动裁剪图片，在实际应用不好操作，而且装ImageMagick也是麻烦事。

使用工具批量裁剪也会出现图片质量下降的问题。

所以符合正常工作流的方式，还是先处理x1的icon，然后将这些icon提交给设计师，设计师重新绘制成x2的icon，这样可以保证icon的质量。

看下淘宝首页的[x1图片][12]和[x2图片][13]，x2的icon是经过设计师重新矢量绘制的，保证清晰度。

### 目录调整

<a href="http://www.36ria.com/6459/sprite-x2-1" rel="attachment wp-att-6460"><img src="http://www.36ria.com/wp-content/uploads/2014/05/sprite-x2-1.png" alt="sprite-x2-1" width="258" height="103" class="alignnone size-full wp-image-6460" /></a>

demo中x2图片是我用工具强制转换的，所以比较糊，只是用于演示，请无视。

s目录下是x1的icon，s-x2下是x2的icon。

### sprite-map()的使用

上一篇教程中，我们使用compass来自动生成图标代码：

    @import "s/*.png"
    @include all-s-sprites
    

如果要适配retina，我们不能这么做，必须想办法把x1的图片路径和x2的图片路径放入 **image-set()** 中。

所以需要用到compass的方法sprite-map()，改方法不会自动生成图标代码，但会创建图标的引用。

    $sprites: sprite-map("s/*.png",  $layout: diagonal)            // import 1x sprites
    $sprites-x2: sprite-map("s-x2/*.png", $layout: diagonal)   // import 2x sprites
    

$sprites是x1图标集变量，$sprites2x是x2图标集变量。

为什么要设置 **$layout: diagonal** ？

我们来看下没使用该属性生成的sprite图：

x1

<a href="http://www.36ria.com/6459/compass-sprite-2-2" rel="attachment wp-att-6461"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-21.png" alt="compass-sprite-2" width="159" height="202" class="alignnone size-full wp-image-6461" /></a>

x2

<a href="http://www.36ria.com/6459/compass-sprite-3-2" rel="attachment wp-att-6462"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-31.png" alt="compass-sprite-3" width="164" height="201" class="alignnone size-full wp-image-6462" /></a>

留意图标的位置是不对应的！这是compass一个很怪异的地方，x1和x2图片目录下的图片名称是一模一样的，但合并的图片位置却不是对应的！

使用 **$layout: diagonal** 后的sprite图片：

x1

<a href="http://www.36ria.com/6459/compass-sprite-4-2" rel="attachment wp-att-6463"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-41.png" alt="compass-sprite-4" width="230" height="99" class="alignnone size-full wp-image-6463" /></a>

x2

<a href="http://www.36ria.com/6459/compass-sprite-5" rel="attachment wp-att-6464"><img src="http://www.36ria.com/wp-content/uploads/2014/05/compass-sprite-5.png" alt="compass-sprite-5" width="450" height="203" class="alignnone size-full wp-image-6464" /></a>

**$layout: diagonal** 将图标的布局方式改成对角线布局后，x1与x2的图标关系才正常！

### 修改我们的mixin

    @mixin image-set($name)
      display: block
      height: image-height(sprite-file($sprites, $name))
      width: image-width(sprite-file($sprites, $name))
      background-position: sprite-position($sprites, $name)
      background: sprite-url($sprites)
      background-image: -webkit-image-set(sprite-url($sprites) 1x, sprite-url($sprites-x2) 2x)
      background-image: -moz-image-set(sprite-url($sprites) 1x, sprite-url($sprites-x2) 2x)
      background-image: -ms-image-set(sprite-url($sprites) 1x, sprite-url($sprites-x2) 2x)
    

name为图标文件名。

手动编写icon代码：

    .s-adults
      +image-set(adults)
    

生成的css代码如下：

        .s-adults {
          display: block;
          height: 17px;
          width: 80px;
          background-position: 0 -82px;
          background: url('/src/images/s-sda1755b835.png');
          background-image: -webkit-image-set(url('/src/images/s-sda1755b835.png') 1x, url('/src/images/s-x2-s1d399be72c.png') 2x);
          background-image: -moz-image-set(url('/src/images/s-sda1755b835.png') 1x, url('/src/images/s-x2-s1d399be72c.png') 2x);
          background-image: -ms-image-set(url('/src/images/s-sda1755b835.png') 1x, url('/src/images/s-x2-s1d399be72c.png') 2x);
        }
    

亲可以在retina屏幕下试试，浏览器（chrome）会自动使用x2 的sprite！

 [1]: http://www.36ria.com/6444
 [2]: http://www.w3cplus.com/css/towards-retina-web.html
 [3]: http://www.w3cplus.com/preprocessor/retina-icon-sprites.html
 [4]: http://blog.teamtreehouse.com/the-ecstasy-and-agony-of-compass-sprite-generation-part-2
 [5]: https://github.com/AdamBrodzinski/Retina-Sprites-for-Compass
 [6]: https://github.com/novascreen/Retina-mixins-for-Compass
 [7]: https://github.com/AdamBrodzinski/Retina-Sprites-for-Compass/blob/master/_retina-sprites.scss
 [8]: https://github.com/AdamBrodzinski/Retina-Sprites-for-Compass/blob/master/demo/css/main.css
 [9]: http://www.w3cplus.com/css/safari-6-and-chrome-21-add-image-set-to-support-retina-images.html
 [10]: https://github.com/minghe/compass-demo/blob/master/src/sprite-x2.sass
 [11]: http://imagemagick.org/script/index.php
 [12]: http://gtms01.alicdn.com/tps/i1/T1ye7BFUNbXXcLNDLI-400-500.png
 [13]: http://gtms03.alicdn.com/tps/i3/T1YqkOFwXiXXcR0pAD-800-1000.png
