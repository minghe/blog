# 一些精巧的HOVER效果
======

本文介绍通过应用CSS的一些新特性，如3D变换和伪元素来创造一些精巧的hover效果。

![][1]

现在很常看到好的设计有着优美的线条、留白，简洁的布局和精妙的效果。这里来分享如何创建这些巧妙的hover效果。

这里的hover效果应用3D转换和伪元素。  
**注意：这些新特性需要浏览器支持，你懂得……字体变形在firefox上不是很流畅。**

demo图片来至<a href="http://unsplash.com/" target="_blank">Unsplash</a>， 所用的icon来至<a href="https://gumroad.com/l/feather" target="_blank">Feather icon set</a>作者<a href="http://twitter.com/colebemis" target="_blank"> Cole Bemis</a>

结构如下：

<pre class='brush: html; '><div class="grid">
  &lt;figure class="effect-lily">
                  <img src="img/1.jpg" alt="img01" />
                  &lt;figcaption>
                      <h2>
    Nice <span>Lily</span>
          
      
  </h2>
                      
      
      
    
    
  
  <p>
    Lily likes to play with crayons and pencils
          
      
  </p>
                      
      
      
    
    
  
  <a href="#">View more</a>
                  &lt;/figcaption>         
              &lt;/figure>
           
              <!-- ... -->
               
          
    
  
</div>
</pre>

除了demo通用样式外，每个hover效果有对应的样式。

下面以Sadie效果为例：应用了伪元素+线性渐变+3D变换从底部滑出显示文字，标题应用了颜色的转变。

<pre class='brush: css; '>figure.effect-sadie figcaption::before {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        <!-- 线性渐变 -->
        background: linear-gradient(to bottom, rgba(72,76,97,0) 0%, rgba(72,76,97,0.8) 75%);
        content: '';
        opacity: 0;
        





<!-- 3D变换 -->
        transform: translate3d(0,50%,0);
    }
     
    figure.effect-sadie p {
        position: absolute;
        bottom: 0;
        left: 0;
        padding: 2em;
        width: 100%;
        opacity: 0;
        





<!-- 3D变换 -->
        transform: translate3d(0,10px,0);
    }
     
    figure.effect-sadie figcaption::before,
    figure.effect-sadie p {
        





<!-- 透明度和3D变换转变设置 -->
        transition: opacity 0.35s, transform 0.35s;
    }
     
    figure.effect-sadie h2 {
        position: absolute;
        top: 50%;
        left: 0;
        width: 100%;
        color: #484c61;
        





<!-- 3D变换 -->
        transform: translate3d(0,-50%,0);
        





<!-- 3D变换和颜色转变设置 -->
        transition: transform 0.35s, color 0.35s;
    }
     
    figure.effect-sadie:hover h2 {
        color: #fff;
        





<!-- 3D变换 -->
        transform: translate3d(0,-50%,0) translate3d(0,-40px,0);
    }
     
    figure.effect-sadie:hover figcaption::before ,
    figure.effect-sadie:hover p {
        opacity: 1;
        





<!-- 3D变换 -->
        transform: translate3d(0,0,0);
    }
</pre>

以上样式为了简洁将浏览器前缀去掉了。完整的样式表请查看demo或下载源代码。

更多hover效果请查看demo或下载demo源代码，也可以去<a href="https://github.com/codrops/HoverEffectIdeas" target="_blank">Github</a>

<ul class="tow-columns clearfix">
  <li class="l">
    <a href="http://tympanus.net/Development/HoverEffectIdeas/" class="btn-view-demo" target="_blank">查看demo</a>
  </li>
  <li class="l">
    <a href="http://tympanus.net/Development/HoverEffectIdeas/HoverEffectIdeas.zip" class="btn-download" target="_blank">下载源代码</a>
  </li>
</ul>

文章翻译自 [Ideas for Subtle Hover Effects][2]

 [1]: http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2014/06/HoverEffectIdeas.jpg
 [2]: http://tympanus.net/codrops/2014/06/19/ideas-for-subtle-hover-effects/ "Ideas for Subtle Hover Effects"