# html5本地存储localStorage实战（1）
=======

<a href="http://www.36ria.com/4247/localstorage" rel="attachment wp-att-4251"><img src="http://www.36ria.com/wp-content/uploads/2011/05/localStorage.png" alt="" title="localStorage" width="680" height="200" class="alignnone size-full wp-image-4251" /></a> 之前有朋友给明河发邮件，问道新版的RIA之家的“发表评论”和“浏览历史”这二块的记忆是通过cookies实现的吗？如果你使用IE（IE8以下版本）打开RIA之家，就会发现记忆功能失效了！如果是采用cookies一般不存在浏览器差异，显然明河使用了其他方案，这这个方案就是今天明河着重跟大家介绍的：**localStorage**（html5本地存储方案）。 <ul class="tow-columns clearfix">
  <li class="l">
    <a href="http://www.36ria.com/demo/html5/localStorage/comment.html" class="btn-view-demo" target="_blank">点此查看demo</a>
  </li>
  <li class="l">
    [download id="215"]
  </li>
</ul>

#### web本地存储方案总结 本地存储解决方案很多，比如Flash SharedObject、Google Gears、Cookie、DOM Storage、User Data、window.name、Silverlight、Open Database等。 借用网上的一张图来看下目前主流的本地存储方案： 

<a href="http://www.36ria.com/4247/attachment/11332182562383641082" rel="attachment wp-att-4248"><img src="http://www.36ria.com/wp-content/uploads/2011/05/11332182562383641082.jpg" alt="" title="本地存储方案" width="600" height="202" class="alignnone size-full wp-image-4248" /></a> **Cookie**在web中得到广泛应用，但局限性非常明显，容量太小，有些站点会因为出于安全的考虑而禁用cookie，cookie没有想象中的那么安全。 **Flash SharedObject**之前明河有过介绍，有兴趣的朋友可以看<a href="http://www.36ria.com/3931" target="_blank">《如何使用kissy的flash本地存储》</a>，使用的是kissy的store模块来调用Flash SharedObject。Flash SharedObject的优点是容量适中，基本上不存在兼容性问题，缺点是要在页面中引入特定的swf和js文件，增加额外负担，处理繁琐；还是有部分机子没有flash运行环境。 **Google Gears**Google的离线方案，已经停止更新，官方推荐使用html5的localStorage方案。 **User Data**IE的早期版本的本地存储方案，看上去有点怪异，有兴趣的朋友可以看<a href="http://www.planabc.net/2008/08/05/userdata_behavior/" target="_blank">《跨浏览器的本地存储方案一》</a> 
#### localStorage 相对于上述本地存储方案，localStorage有自身的优点：容量大、易用、强大、原生支持； 缺点是兼容性差些（主要是IE8以下版本不支持）、安全性也差些（所以请勿使用localStorage保存敏感信息）。 

##### localStorage的API 非常通俗易懂的接口 

*   localStorage.getItem(key):获取指定key本地存储的值
*   localStorage.setItem(key,value)：将value存储到key字段
*   localStorage.removeItem(key):删除指定key本地存储的值 留意localStorage存储的值都是字符串类型，在处理复杂的数据时，比如json数据时，需要借助JSON类，将json字符串转换成真正可用的json格式，localStorage第二个实战教程会重点演示相关功能。 localStorage还提供了一个

**storage**事件，在存储的值改变后触发，比如下面代码： <coolcode lang="javascript"> if (window.addEventListener) { window.addEventListener("storage", storageHandler, false); } else if (window.attachEvent) { window.attachEvent("onstorage", storageHandler); } function storageHandler(ev){ if(!ev){ev=window.event;} console.log('改变的字段是'+ev.key); console.log('旧的值是'+ev.oldValue); console.log('新的值是'+ev.newValue); } </coolcode> 
#### 明河整理的LS类 明河对localStorage进行了封装，写成LS，目前只是显示基础功能，日后会强化这个类，希望可以成为一个通用解决方案。 <coolcode lang="javascript"> /** * @fileOverview html5本地存储(日后有可能会发布兼容IE8以下版本的IE的本地存储类) * @author 明河<minghe36@126.com> * @version 1.0 * @example * LS.item("key","value");//设置key字段为value * LS.item("key");//设置key字段的值 */ var LS = { /** * 获取/设置存储字段 * @param {String} name 字段名称 * @param {String} value 值 * @return {String} */ item : function(name,value){ var val = null; if(LS.isSupportLocalStorage()){ if(value){ localStorage.setItem(name,value); val = value; }else{ val = localStorage.getItem(name); } }else{ console.log('浏览器不支持html5的本地存储！'); } return val; }, /** * 移除指定name的存储 * @param {String} name 字段名称 * @return {Boolean} */ removeItem : function(name){ if(LS.isSupportLocalStorage()){ localStorage.removeItem(name); }else{ console.log('浏览器不支持html5的本地存储！'); return false; } return true; }, /** * 判断浏览器是否直接html5本地存储 */ isSupportLocalStorage : function(){ var ls = LS,is = ls.IS_HAS_LOCAL_STORAGE; if(is == null){ if(window.localStorage){ is = ls.IS_HAS_LOCAL_STORAGE = true; } } return is; }, IS_HAS_LOCAL_STORAGE : null }; </coolcode> 

#### 如何调用？ 我们先来创建个评论表单： <coolcode lang="html"> </coolcode> 接下来我们希望入框失去焦点后记忆输入框的值：（记忆三个字段：用户名、邮箱、网站，这样用户下次进入博客评论时无需重复输入这三个表单域） <coolcode lang="javascript"> $(function(){ var val; //表单内的输入框失去焦点后将数据写入本地存储（字段为输入框id名） $('.form-field').bind('blur',function(){ LS.item($(this).attr('id'),$(this).val()); }) //遍历输入框，获取本地存储的相应字段的值，写入到表单输入框内 .each(function(){ val = LS.item($(this).attr('id')); if(val != null) $(this).val(val); }); }) </coolcode> 监听输入框的blur事件，获取该输入框的id作为存储的key名，输入框的值自然是需要存储的值。 在页面结束后，我们还需要遍历下输入框，看看该id存不存在本地储存的值，如果有，将其写入到输入框。 

#### 明河结语 这是比较简单的localStorage使用场景，下一篇教程会增加难度，用localStorage来处理网站的“浏览历史”。

