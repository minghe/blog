# 制作一个类似 Instagram的图片滤镜web App（下）
=======

过意不去，最近忙得跟狗一样，导致下篇到现在才写完... <a href="http://www.36ria.com/6093" target="_blank">上一篇教程</a>明河讲解了如何使用Filereader.js来处理图片信息的读取及背后的原理，下篇教程才是此web app的核心，使用<a href="http://camanjs.com/" target="_blank">Caman.js</a>实现图片滤镜效果。 
## 使用Caman.js实现图片滤镜

### step1：将图片绘制到canvas中 上一篇教程实现了一半功能：用户选择图片后，js读取图片数据，接下来你需要将图片绘制到

`Canvas`对象中，说到点子上了，Caman.js的原理就在于Canvas的利用，Canvas对象是有办法获取到图片的像素数据，这样才能通过算法改变像素数据，形成特定的图片风格。 <pre class='brush: javascript;'>var newHeight,newWidth;
    //图片读取成功后触发，这样才能找到图片原始宽度和高度
    img.load(function(){
        //...这里宽高处理代码省略

        // 创建一个Canvas
        originalCanvas = $('&lt;canvas>');
        var originalContext = originalCanvas[0].getContext('2d');

        // 设置canvas元素的宽度、高度、外边距
        originalCanvas.attr({
        width: newWidth,
        height: newHeight
        }).css({
        marginTop: -newHeight/2,
        marginLeft: -newWidth/2
        });

        // 将图片绘制到canvas元素中
        originalContext.drawImage(this, 0, 0, newWidth, newHeight);
    });
</pre> 来学习下Canvas的二个核心API： 

`getContext('2d')`:创建context对象, 是canvas元素内建的，js通过操作context对象，来实现绘制路径、矩形、圆形、字符以及添加图像的功能。 `drawImage(image,x,y,width,height)`：留意是context对象的方法，用户绘制图片。 
### step2：创建滤镜列表

<pre class='brush: xml;'><div id="filterContainer">
  <ul id="filters">
    <li>
      <a href="#" id="normal">Normal</a> 
    </li>
            
    <li>
      <a href="#" id="vintage">Vintage</a> 
    </li>
            
    <li>
      <a href="#" id="lomo">Lomo</a> 
    </li>
            
    <li>
      <a href="#" id="clarity">Clarity</a> 
    </li>
            
    <li>
      <a href="#" id="sinCity">Sin City</a> 
    </li>
            
    <li>
      <a href="#" id="sunrise">Sunrise</a> 
    </li>
            
    <li>
      <a href="#" id="crossProcess">Cross Process</a> 
    </li>
            
    <li>
      <a href="#" id="orangePeel">Orange Peel</a> 
    </li>
            
    <li>
      <a href="#" id="love">Love</a> 
    </li>
            
    <li>
      <a href="#" id="grungy">Grungy</a> 
    </li>
            
    <li>
      <a href="#" id="jarques">Jarques</a> 
    </li>
            
    <li>
      <a href="#" id="pinhole">Pinhole</a> 
    </li>
            
    <li>
      <a href="#" id="oldBoot">Old Boot</a> 
    </li>
            
    <li>
      <a href="#" id="glowingSun">Glowing Sun</a> 
    </li>
            
    <li>
      <a href="#" id="hazyDays">Hazy Days</a> 
    </li>
            
    <li>
      <a href="#" id="herMajesty">Her Majesty</a> 
    </li>
            
    <li>
      <a href="#" id="nostalgia">Nostalgia</a> 
    </li>
            
    <li>
      <a href="#" id="hemingway">Hemingway</a> 
    </li>
            
    <li>
      <a href="#" id="concentrate">Concentrate</a> 
    </li>
        
  </ul>
  
</div>
</pre> 上面的列表比较长，我们使用mousewheel插件处理下滚动。 

<pre class='brush: javascript;'>//使用mousewheel插件处理下滤镜列表的滚动。
    filterContainer.find('ul').on('mousewheel',function(e, delta){
    this.scrollLeft -= (delta * 50);
    e.preventDefault();
    });
</pre> 留意a元素上的id，每个id都是Caman.js的一种滤镜效果。 Caman.js拥有非常丰富的图片滤镜效果，很强大。 接下来我们给每个li添加

`click`事件，当用户点击时，切换滤镜效果。 <pre class='brush: javascript;'>var filters = $('#filters li a');
	// 点击列表元素，切换滤镜效果
	filters.click(function(e){

		e.preventDefault();

		var f = $(this);
        //如果点击的滤镜没变化，不需要处理
		if(f.is('.active')){
			return false;
		}
		filters.removeClass('active');
		f.addClass('active');

		// 克隆canvas元素
		var clone = originalCanvas.clone();

		// 克隆在canvas中的图片
		clone[0].getContext('2d').drawImage(originalCanvas[0],0,0);

		// 移除源canvas元素，并将克隆的canvas元素插入到图片容器内
		photo.find('canvas').remove().end().append(clone);

        //获取a元素上的id（滤镜名称)
		var effect = $.trim(f[0].id);
        //调用CamanJs的API
		Caman(clone[0], function () {

			// 如果存在该滤镜效果，应用滤镜算法，改变图片风格

			if( effect in this){
                //设置滤镜
				this[effect]();
                //应用到图片上
				this.render();

				// 显示下载按钮
				showDownload(clone[0]);
			}
			else{
                //隐藏下载按钮
				hideDownload();
			}
		});

	});
</pre> 来学习下CamanJs的核心API。 

`Caman（canvasElement,callback）` ：第一个参数为canvas元素，第二个参数为处理滤镜的回调。 `this[effect]()` ：设置滤镜。 `this.render()`：应用滤镜算法到图片上（这是比较耗资源的）。 更详细内容，请看<a href="http://camanjs.com/guides/#BasicUsage" target="_blank">《CamanJs guide》</a> 。 
### step3：下载应用滤镜后的图片

<pre class='brush: javascript;'>//下载按钮
	var downloadImage = $('a.downloadImage');

	function showDownload(canvas){
		downloadImage.off('click').click(function(){
			//获取canvas的图片数据，并添加到a元素按钮的href属性上，这样用户点击后就会调用本地工具下载
			var url = canvas.toDataURL("image/png;base64;");
			downloadImage.attr('href', url);

		}).fadeIn();

	}
	function hideDownload(){
		downloadImage.fadeOut();
	}
</pre> 来看个canvas的另外一个API： 

`toDataURL(type)`：将当前 canvas 里的内容导出成图片数据, 结果是一个以 “data:” 开头的字符串, 实际上是图片的像素数据,把这个贴到 img 标签的 src 里, 就能看见图片了. type 是导出的 mime 类型, 比如 “image/jpeg” , “image/png” , “image/svg+xml”,至于支持那些个 mime 类型跟浏览器有关,默认是 “image/png”。 至此我们的应用已经大功告成了，完整的代码请看<a href="https://github.com/minghe/36ria-demo/blob/master/html5/instagram-filter-app/assets/js/script.js" target="_blank">script.js</a>。

