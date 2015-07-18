# kissy1.3的Promise精讲
=======

promise规范（留意明河这里用的是规范一词，也就是说Promise模块不止是kissy有，像jquery、dojo等著名js库都有，都是规范的具体实现）在现代javascript编程具有非常大的现实意义，特别是在NodeJs中，建议大家阅读《JavaScript异步编程》1-3章的内容，如果亲懒得买书，明河推荐阅读二篇经典文章：<a href="http://www.ruanyifeng.com/blog/2012/12/asynchronous＿javascript.html" target="_blank">《Javascript异步编程的4种方法》</a>和<a href="http://www.infoq.com/cn/news/2011/09/js-promise" target="_blank">《JavaScript异步编程的Promise模式》</a>。 promise是commonJs的规范中的内容，目的是利用更为简洁良好的AP让异步处理过程更为通俗易懂，同时更为易用。 明河对promise价值理解是：promise消除反人类的多层回调嵌套，提高代码的可阅读性和可维护性。 
## promise典型场景应用 场景：需要发送三个异步请求，且三个异步请求存在依赖，写出来的代码可能如下： 

<pre class='brush: javascript;'>KISSY.use('ajax',function(S,io){
    io.get('1.json',function(result1){
        io.get('2.json',function(result2){
            io.get('3.json',function(result3){

            })
        })
    })
})
</pre> “一不小心”亲就会写出这样的代码，然后请在深夜感受维护者的怨念.... 多层嵌套回调是可维护性的大敌，反人类反自然，拿起promise，代表月亮消灭它们吧，少年~ 来看下基于promise的改造。 

<pre class='brush: javascript;'>KISSY.use('ajax',function(S,io){
    io.get('1.json').then(function(result1){
        //callback1
        return io.get('2.json');
    }).then(function(result2){
        //callback2
        return io.get('3.json');
    }).then(function(result3){
        //callback3
    })
})
</pre> 通过代码的比较，你就会发现，基于Promise的代码，回调的多层嵌套已经消失，代码顿时清爽了很多，这一切多亏了

`then()`，代码更具有逻辑性，自然且方便理解。 （KISSY1.3的ajax模块继承于Promise.Defer()。） 再来看个[jsonp调用github api的demo][1]，感受下Promise的用法。 
## Promise用法精讲 承玉曾写过一篇文章

[promise api 与应用场景][2]讲解了Promise的API设计。 明河这里主要讲解Promise的用法，相关API可以看[文档][3]。 Promise模块共有二个类：[Promise][4]和[Promise.Defer][5]。 假如Promise实例是岛屿的话，Defer实例就是岛屿间的吊桥，决定了Promise之间的链接和传递，来看个[最简单的demo][6]。 <pre class='brush: javascript;'>KISSY.use('node,promise',function(S,Node,Promise){
    var $ = Node.all;
    //实例化一个Defer，不用实例化Promise
    //Defer用于控制对应promise传递的成功和失败
    var d = new Promise.Defer();
    //promise是Defer的核心属性， 用于外部监听，传递成功/失败的函数
    var promise = d.promise;
    //then方法第一个参数监听异步链成功流转到此的值，返回新的promise，并向下传递
    promise.then(function (v) {
        return v + 1;
    }).then(function (v) {
        $('body').append('<p>
  值为：<span style="color:red">'+v+'</span>，第二个then，值为第一个then中success回调的返回值。
</p>');
    });
    //向异步链传值，并运转异步链
    d.resolve(1);
});
</pre> 明河给代码加了详细的注释，请认真感受下promise api的使用。 （异步链是明河YY的名词，感觉比较切合promise的流转过程。） 首先创建Promise.Defer的实例： 

<pre class='brush: javascript;'>var d = new Promise.Defer();
</pre> 获取promise属性（为一个Promise实例）。 

<pre class='brush: javascript;'>var promise = d.promise;
</pre> Promise有个核心方法：

`then(successCallback,failCallback)`，有二个参数： 
*   successCallback:监听异步链成功流转到此的值，返回新的promise，并向下传递
*   failCallback:监听异步链失败流转到此的值，返回新的promise，并向下传递 大家想象下kissy中ajax的回调： 

<pre class='brush: javascript;'>var ajax = io({
    type: 'post',
    url:url,
    dataType:'json'
})
ajax.then(function(result){
    S.log(result[0]);
},function(){
    S.log('服务器故障，请求失败！');
})
</pre> then()起到承上启下的作用，接收刘转过来的数据，同时向下传递数据。 回到demo： 

<pre class='brush: javascript;'>promise.then(function (v) {
    return v + 1;
}).then(function (v) {
    $('body').append('<p>
  值为：<span style="color:red">'+v+'</span>，第二个then，值为第一个then中success回调的返回值。
</p>');
});
</pre> 留意then回调的参数数据。 promise定义好了，并不执行，还需要Defer实例触发。 Defer有个resolve方法用于传递数据同时流转promise。 

<pre class='brush: javascript;'>d.resolve(1);
</pre>

#### promise打破多层回调嵌套的用法 先看下

<a href="http://demo.36ria.com/kissy/promise/simple.html" target="_blank">多个setTimeout的demo</a>，感受下。 假设setTimeout嵌套逻辑如下： <pre class='brush: javascript;'>setTimeout(function(){
    setTimeout(function(){
        setTimeout(function(){

        })
    })
})
</pre> 有人写过这么变态的代码么... 现在，我们需要基于Promise进行改造。 

<pre class='brush: javascript;'>KISSY.use('promise', function (S, Promise) {
        var d = new Promise.Defer();
        //向异步链传值，并运转异步链
        d.resolve(1);
        var promise = d.promise;
        //在setTimeout外包裹个promise
        promise.then(function (v) {
            var d = new Promise.Defer();
            setTimeout(function () {
                d.resolve(v + 1);
            }, 1000);
            return d.promise;
        })
        //特别留意这里，success的回调会等到上一个promise成功传递值到这里时触发
        .then(function (v) {
            alert('第二个then执行，异步链传到这里的值为'+v);
        });
    });
</pre> 这个demo会比上一个复杂。 关键的代码是： 

<pre class='brush: javascript;'>promise.then(function (v) {
    var d = new Promise.Defer();
    setTimeout(function () {
        d.resolve(v + 1);
    }, 1000);
    return d.promise;
})
</pre> then()可以返回个其他定义的Promise实例，一样可以保证异步链的流转，当内部定义的Defer，执行resolve()，后续的异步逻辑才往下执行。 

#### 组件继承于Promise Promise切实地改变了异步组件的API，让用户更方便的demo。 典型的异步组件，像ajax和uploader。 严重建议大家，如果你写的组件是有异步逻辑的，请继承Promise，像下面的代码： 

<pre class='brush: javascript;'>KISSY.add(function(S,Promise){
     function IO(c) {
        var self = this;
        Promise.call(self);
     }
     S.mix(IO,{
        _defer: new Promise.Defer(this)
     })
     S.extend(IO, Promise,{

          _ioReady:function(){
                //成功获取数据后
                var defer = self._defer;
                defer[isSuccess ? 'resolve' : 'reject']([self.responseData, statusText, self]);
          }
     });
    return IO;
 },requires:['promise'])
</pre>

## 总结 promise，是前端必须掌握的特性，严重推荐在日常编码中使用起来，改造下你的ajax代码，免得被人骂土鳖哦。

 [1]: http://demo.36ria.com/kissy/promise/github-api.html
 [2]: http://yiminghe.iteye.com/blog/1396751
 [3]: http://docs.kissyui.com/docs/html/api/component/promise/index.html
 [4]: http://docs.kissyui.com/docs/html/api/component/promise/promise.html
 [5]: http://docs.kissyui.com/docs/html/api/component/promise/defer.html
 [6]: http://demo.36ria.com/kissy/promise/simple.html