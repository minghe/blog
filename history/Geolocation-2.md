# Geolocation和Web Sockets基于NodeJs的实战（2）
=======

<a href="http://www.36ria.com/5914" target="_blank">上一篇教程</a>明河讲解了一些NodeJs的知识点，这一篇教程将给大家讲解前端方面的知识点。 
## Geolocation Geolocation是html5中的主要内容，用于处理客户端的地理信息，特别在手持设备上有广泛的使用场景。 

<a class="btn-view-demo" style="float:none;" href="http://html5demos.com/geo" target="_blank">Geolocation的简单的demo</a> （PS:demo中虽然实现了位置定位，但不能随着你的移动，坐标也发生变化，借助NodeJs的socket.io模块即可实现。） 
### Geolocation的兼容性情况

<a href="http://www.36ria.com/5943/geolocation-browses" rel="attachment wp-att-5944"><img src="http://www.36ria.com/wp-content/uploads/2013/01/Geolocation-browses.jpg" alt="" title="Geolocation-browses" width="745" height="253" class="alignnone size-full wp-image-5944" /></a> Geolocation兼容性还算不错，不算IE9以下，你懂的.... 
### Geolocation的地理信息精度问题 Geolocation的主要定位方式有如下几种： 

*   ip地址：也是web端最常用的方式，精度基本符合日常使用场景，有时可能出现定位到ISP机房的情况
*   GPS :常见于移动设备，最为精确，定位需时较长。
*   WiFi基站的mac地址：适用范围窄
*   GSM或CDMA基站：精度一般 （《html5高级程序设计》中有详细的论述。） 

### Geolocation API的使用 先通过

`navigator.geolocation`的存在性，判断浏览器是否支持Geolocation; <pre class='brush: js; '>/**
     * 浏览器是否支持 geolocation api
     * @return Boolean
     */
    function isSupportGeo(){
        var isSupport = false;
        if (navigator.geolocation) {
            isSupport = true;
        } else {
            isSupport = false;
            console.log('浏览器不支持geolocation');
        }
        return isSupport;
    }
</pre> 如何获取客户端当前地理位置？ 

`navigator.geolocation.getCurrentPosition(successCallback, [errorCallback] , [positionOptions])`，使用`getCurrentPosition()`方法即可。 参数的含义如下： 
*   successCallback：获取定位成功时执行的回调函数
*   errorCallback：可选，定位失败时执行的回调函数
*   positionOptions：可选，拥有三个可配置属性{enableHighAccuracy:boolean , timeout:long , maximumAge:long}，用于控制定位精度，一般不用配置 demo代码如下： 

<pre class='brush: js; '>//获取用户当前位置
	if (isSupportGeo) {
		navigator.geolocation.getCurrentPosition(positionSuccess, positionError, { enableHighAccuracy: true });
	} else {
		$('.map').text('Your browser is out of fashion, there\'s no geolocation!');
	}
</pre> positionSuccess回调带有position数据，核心的数据有longitude（经度）、latitude（维度）、accuracy（精确度）。 来看下positionSuccess这个回调函数代码： 

<pre class='brush: js; '>/**
     * 获取地理信息成功后执行的回调函数
     * @param position 位置信息
     */
     function positionSuccess(position) {
                //纬度
		var lat = position.coords.latitude;
                //经度
	 	var lng = position.coords.longitude;
                 //以米为单位的经度和纬度的精确度
		var acr = position.coords.accuracy;
      }
</pre> 想要把坐标定位到地图上还需要使用

**leaflet**的API（google map也是可以的）。 
## leaflet

<a href="http://leafletjs.com/" target="_blank">leaflet</a>是相当不错的地图库，使用起来非常简单。 
### 在页面中引入leaflet.js

<pre class='brush: xml; '><script src="./js/lib/leaflet.js"></script>
</pre> leaflet会添加

**L**全局变量，包含所有的API。 
### leaflet api的使用

<a href="http://leafletjs.com/reference.html" target="_blank">点此进入API文档</a>。内容很多，本文只用到核心的几个方法。 使用<a href="http://leafletjs.com/reference.html#marker" target="_blank">L.marker()</a>定位坐标 <pre class='brush: js; '>function positionSuccess(position) {
                //纬度
		var lat = position.coords.latitude;
                //经度
	 	var lng = position.coords.longitude;
                 //以米为单位的经度和纬度的精确度
		var acr = position.coords.accuracy;

		// 定位用户坐标
		var userMarker = L.marker([lat, lng], {
			icon: redIcon
		});

		// demo
		// userMarker = L.marker([51.45, 30.050], { icon: redIcon });
      }
</pre>

** L.marker()**第一个参数为坐标，第二个参数可选，demo中，我们自定义了定位坐标时使用的icon样式： <a href="http://www.36ria.com/5943/pos-icon" rel="attachment wp-att-5947"><img src="http://www.36ria.com/wp-content/uploads/2013/01/pos-icon.jpg" alt="" title="pos-icon" width="254" height="120" class="alignnone size-full wp-image-5947" /></a> 定义icon，比较麻烦，需要使用<a href="http://leafletjs.com/reference.html#icon" target="_blank">L.Icon</a>接口。 <pre class='brush: js; '>// 自定义坐标定位样式
	var tinyIcon = L.Icon.extend({
		options: {
			shadowUrl: '../assets/marker-shadow.png',
			iconSize: [25, 39],
			iconAnchor:   [12, 36],
			shadowSize: [41, 41],
			shadowAnchor: [12, 38],
			popupAnchor: [0, -30]
		}
	});
	var redIcon = new tinyIcon({ iconUrl: '../assets/marker-red.png' });
</pre> 通过

`iconUrl`配置icon图片的位置。 接下来加载leaflet的地图，使用<a href="http://leafletjs.com/reference.html#map-usage" target="_blank">map()</a>方法。 <pre class='brush: js; '>// 加载地图
		map = L.map('map');
</pre> 第一个参数为地图容器id，对应： 

<pre class='brush: xml; '><div id="map">
  
</div>
</pre> map()是最核心的方法，拥有丰富的配置项，详细可以看

<a href="http://leafletjs.com/reference.html#map-usage" target="_blank">文档</a>，比如： <pre class='brush: js; '>var map = L.map('map', {
    //地图中心点配置
    center: [51.505, -0.09],
    //放大倍数
    zoom: 13
});
</pre> 使用

`L.tileLayer()`配置地图瓦片图层的图片地址。 一张地图并不是完整的一张图片，而是有多张图片拼接而成的，我们将这些小图片称为瓦片图层，即**tileLayer**。 <pre class='brush: js; '>L.tileLayer('http://{s}.tile.cloudmade.com/BC9A493B41014CAABB98F0471D759707/997/256/{z}/{x}/{y}.png', { maxZoom: 18, detectRetina: true }).addTo(map);
</pre> 重点解释下

`http://{s}.tile.cloudmade.com/BC9A493B41014CAABB98F0471D759707/997/256/{z}/{x}/{y}.png`，这个路径其实是图片路径模版，可以通过调试工具看下地图图层的路径： <a href="http://www.36ria.com/5943/tilelayer" rel="attachment wp-att-5948"><img src="http://www.36ria.com/wp-content/uploads/2013/01/tileLayer.png" alt="" title="tileLayer" width="641" height="132" class="alignnone size-full wp-image-5948" /></a> leaflet会自动将`{s}`替换成a、b或c等组成2级域名，`{z}`为放大等级，`{x}`和`{y}`为坐标。 接下来配置地图容器填充满整个世界。（容器内显示世界地图） <pre class='brush: js; '>map.fitWorld();
</pre> 将定位坐标添加到地图内： 

<pre class='brush: js; '>userMarker.addTo(map);
</pre> leaflet可以给定位坐标icon添加个弹出层，显示指定的内容： 

<pre class='brush: js; '>userMarker.bindPopup('你在这里，你的id是 ' + userId).openPopup();
</pre> userId为随机id。 

## 完整的地图定位代码 html部分代码如下： 

<pre class='brush: xml; '><link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet-0.4/leaflet.css" />

<div id="map">
  
</div>

    

<script src="./js/lib/leaflet.js"></script>
</pre> js部分代码如下： 

<pre class='brush: js; '>// 自定义坐标定位样式
	var tinyIcon = L.Icon.extend({
		options: {
			shadowUrl: '../assets/marker-shadow.png',
			iconSize: [25, 39],
			iconAnchor:   [12, 36],
			shadowSize: [41, 41],
			shadowAnchor: [12, 38],
			popupAnchor: [0, -30]
		}
	});
	var redIcon = new tinyIcon({ iconUrl: '../assets/marker-red.png' });
    /**
     * 浏览器是否支持 geolocation api
     * @return Boolean
     */
    function isSupportGeo(){
        var isSupport = false;
        if (navigator.geolocation) {
            isSupport = true;
        } else {
            isSupport = false;
            console.log('浏览器不支持geolocation');
        }
        return isSupport;
    }
    //获取用户当前位置
	if (isSupportGeo) {
		navigator.geolocation.getCurrentPosition(positionSuccess, positionError, { enableHighAccuracy: true });
	} else {
		$('.map').text('Your browser is out of fashion, there\'s no geolocation!');
	}
    /**
     * 获取地理信息成功后执行的回调函数
     * @param position 位置信息
     */
	function positionSuccess(position) {
        //纬度
		var lat = position.coords.latitude;
        //经度
		var lng = position.coords.longitude;
        //以米为单位的经度和纬度的精确度
		var acr = position.coords.accuracy;

		// 定位用户坐标
		var userMarker = L.marker([lat, lng], {
			icon: redIcon
		});
		// demo
		// userMarker = L.marker([51.45, 30.050], { icon: redIcon });

		// 加载地图
		map = L.map('map');

		// 配置地图瓦片图层的图片地址
		L.tileLayer('http://{s}.tile.cloudmade.com/BC9A493B41014CAABB98F0471D759707/997/256/{z}/{x}/{y}.png', { maxZoom: 18, detectRetina: true }).addTo(map);

		// 配置地图容器填充满整个世界
		map.fitWorld();

		userMarker.addTo(map);
		userMarker.bindPopup('你在这里，你的id是 ' + userId).openPopup();
	}
</pre>

