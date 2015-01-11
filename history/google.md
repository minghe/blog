# google的html/css规范指南
=======

google之前出了javascript规范指南，中文翻译<a href="http://docs.kissyui.com/docs/html/styleguide/google-js-style.html" target="_blank">传送门在此</a>，现在有了html/css规范指南，明河开始翻译时版本是2.1。后续如果google有新的内容补充，明河也会跟进。 
#### 常规样式规则

##### 协议 引入的assets资源文件（js、css、图片文件）

**忽略协议**（**http:, https:**），比如： 不推荐的写法： <pre class='brush: xml;'><script src="http://www.google.com/js/gweb/analytics/autotrack.js"></script>
</pre> 推荐的写法： 

<pre class='brush: xml;'><script src="//www.google.com/js/gweb/analytics/autotrack.js"></script>
</pre> 不推荐的写法： 

<pre class='brush: css;'>.example {
  background: url(http://www.google.com/images/example);
}
</pre> 推荐的写法： 

<pre class='brush: css;'>.example {
  background: url(//www.google.com/images/example);
}
</pre> 关于google的这点建议，明河倒是觉得有待商榷，有兴趣的朋友看

<a href="http://stackoverflow.com/questions/4831741/can-i-change-all-my-http-links-to-just" target="_blank">http://stackoverflow.com/questions/4831741/can-i-change-all-my-http-links-to-just</a>，里面有详细的讨论，根据一位网友的测试，相对url在IE7、IE8下存在二次加载的问题。 
#### 常规格式规则

##### 缩进 使用二个空格缩进（PS：明河一般使用四个空格缩进-_-!） <coolcode lang="html"> 

*   Fantastic
*   Great</coolcode> 

<pre class='brush: css;'>.example {
  color: blue;
}
</pre>

##### 大写 只使用小写。 所有的代码只使用小写字母（PS:淘宝的做法是如果跟js的DOM操作相关，作为钩子使用J_Trigger类似的方式）：包括元素名称、样式名、属性名（除了text/CDATA）。 不推荐的写法： <coolcode lang="html"> 

<A HREF="/">Home</A> </coolcode> 推荐的写法： <pre class='brush: xml;'><img src="google.png" alt="Google" />
</pre>

##### 尾部空白 删掉冗余的行尾空格。 不推荐的写法： 

<pre class='brush: xml;'><p>
  What?_
  </pre>
  推荐的写法：
  <pre class='brush: xml;'>
<p>
  Yes please.
  </pre>
  <h4>
    常规Meta规则
  </h4>
  
  
  <h5>
    编码
  </h5>
  使用
  
  <strong>utf-8</strong>编码。
  指定页面的文档编码为utf-8
  <pre class='brush: xml;'>
<meta charset="utf-8" />
</pre>
  不需要特别指定样式引用的编码为utf-8。
  （ps：关于html编码指定方面的内容，可以看
  
  <a href="http://www.w3.org/International/tutorials/tutorial-char-enc/en/all.html" target="_blank">《 Character Sets & Encodings in XHTML, HTML and CSS》</a>）
  <h5>
    注释
  </h5>
  如果可能，注释还是必不可少的。
  使用注释说明下代码：它包括了什么，它的目的是什么，为什么优先使用它。
  
  
  <h5>
    行动项目
  </h5>
  （ps：推荐使用）
  google建议养成写TODO的习惯，特别是在项目中，记录下一些要改，但来不及修改的地方，或指派其他同事做修改。
  高亮TODO，不同的编辑器有不一样的方式，比如idea是TODO:。
  
  
  <pre class='brush:xml;'>
{# TODO(john.doe): revisit centering #}
<center>
  Test
</center>
</pre>
  
  
  <pre class='brush: xml;'>
<!-- TODO: remove optional tags -->


<ul>
  <li>
    Apples
  </li>
    
  <li>
    Oranges
  </li>
  
</ul>
</pre>
  
  
  <h4>
    常规html设计规则
  </h4>
  
  
  <h5>
    文档类型
  </h5>
  使用html5文档声明：
  <coolcode lang="html">
  
  </coolcode>
  不再使用XHTML（ application/xhtml+xml）。
  
  
  <h5>
    HTML 的正确性
  </h5>
  可以使用一些工具，检验你html的正确性，比如
  
  <a href="http://validator.w3.org/" target="_blank"> W3C HTML validator</a>。
  不推荐的写法：
  <pre class='brush:xml;'>
<title>
  Test
</title>
&lt;article>This is only a test.
</pre>
  推荐的写法：
  <coolcode lang="html">
  
  
  
  <meta charset="utf-8" />
  
  <title>
    Test
  </title>
  <article>This is only a test.</article>
  </coolcode>
  
  
  <h5>
    HTML 的语义性
  </h5>
  使用富含语义性的标签（ps:建议掌握html5新增的部分语义标签）。
  google特别指出了要确保html的可用性，看下面的代码
  不推荐的写法：
  
  
  <pre class='brush:xml;'>
<div onclick="goToRecommendations();">
  All recommendations
</div>
</pre>
  推荐的写法：
  
  
  <pre class='brush:xml;'>
<a href="recommendations/">All recommendations</a>
</pre>
  
  
  <h5>
    多媒体元素降级处理
  </h5>
  给多媒体元素，比如canvas、videos、 images增加alt属性，提高可用性（特别是常用的img标签，尽可量得加上alt属性，提供图片的描述信息）。
  不推荐的写法：
  
  
  <pre class='brush:xml;'>
<img src="spreadsheet.png" />
</pre>
  推荐的写法：
  
  
  <pre class='brush:xml;'>
<img src="spreadsheet.png" alt="Spreadsheet screenshot." />
</pre>
  
  
  <h5>
    html、css、javascript三层分离
  </h5>
  尽可能保持结构（html结构标签）、描述（css）、行为（javascript）的分离，并且让他们尽可能少的交互。确保html文档内容只有html的结构，将css和javascript以资源的方式引入。
  不推荐的写法：
  <coolcode lang="html">
  
  
  
  <title>
    HTML sucks
  </title>
  
  
  <link rel="stylesheet" href="base.css" media="screen" />
  
  <link rel="stylesheet" href="grid.css" media="screen" />
  
  <link rel="stylesheet" href="print.css" media="print" />
  
  <h1 style="font-size: 1em;">
    HTML sucks
  </h1>
  
  
  <p>
    I’ve read about this on a few sites but now I’m sure:
      <u>HTML is stupid!!1</u>
    <center>
      I can’t believe there’s no way to control the styling of
        my website without doing everything all over again!
    </center>
    </coolcode>
    推荐的写法：
    
    <coolcode lang="html">
    
    
    
    <title>
      My first CSS-only redesign
    </title>
    
    
    <link rel="stylesheet" href="default.css" />
    
    <h1>
      My first CSS-only redesign
    </h1>
    
    
    <p>
      I’ve read about this on a few sites but today I’m actually
        doing it: separating concerns and avoiding anything in the HTML of
        my website that is presentational.
      <p>
        It’s awesome!
        </coolcode>
        <h5>
          实体引用
        </h5>
        在html页面中避免使用实体引用。
        如果你的文件是utf-8编码，就不需要使用像
        
        <strong> &mdash;</strong>, <strong>&rdquo;</strong>, or <strong>&#x263a;</strong>的实体引用。
        不推荐的写法：
        <pre class='brush:xml;'>
The currency symbol for the Euro is &ldquo;&eur;&rdquo;.
</pre>
        推荐的写法：
        
        
        <pre class='brush:xml;'>
The currency symbol for the Euro is “€”.
</pre>
        
        
        <h5>
          可选的标签
        </h5>
        忽略一些可选的标签，比如
        不推荐的写法：
        <coolcode lang="html">
        
        
          
          
            
        
        <p>
          Sic.
        </p>
          
        
        </coolcode>
        推荐的写法：
        <coolcode lang="html">
        
        
        
        <title>
          Saving money, saving bytes
        </title>
        
        
        <p>
          Qed.
          </coolcode>
          html5的文档，可以忽略head、body标签。
          所有可忽略的标签，可以看<a href="http://www.whatwg.org/specs/web-apps/current-work/multipage/syntax.html#syntax-tag-omission" target="_blank">《 HTML5 specification 》</a>，
          <h5>
            type属性
          </h5>
          样式和脚本引用可以忽略type属性。
          不推荐的写法：
          
          
          <pre class='brush:xml;'>
&lt;link rel="stylesheet" href="//www.google.com/css/maia.css"
  type="text/css">
</pre>
          推荐的写法：
          
          
          <pre class='brush:xml;'>
<link rel="stylesheet" href="//www.google.com/css/maia.css" />
</pre>
          不推荐的写法：
          
          
          <pre class='brush:xml;'>
&lt;script src="//www.google.com/js/gweb/analytics/autotrack.js"
  type="text/javascript">&lt;/script>
</pre>
          推荐的写法：
          
          
          <pre class='brush:xml;'>
<script src="//www.google.com/js/gweb/analytics/autotrack.js"></script>
</pre>
          
          
          <h4>
            html格式规则
          </h4>
          
          
          <h5>
            常规格式
          </h5>
          每一块、每一列表、每一表格元素都需要另起一行，并缩进每个子元素。
          <coolcode lang="html">
          
          
          <blockquote>
            <p>
              <em>Space</em>, the final frontier.
            </p>
            
          </blockquote>
          </coolcode>
          <coolcode lang="html">
          
          
          <ul>
            <li>
              Moe
                <li>
                Larry
                  <li>
                  Curly
                  </ul>
                  </coolcode>
                  <coolcode lang="html">
                  <table>
                    <thead>
                      <tr>
                        <th scope="col">
                          Income
                        </th>
                              
                        
                        <th scope="col">
                          Taxes
                        </th>
                          
                        
                        <tbody>
                          <tr>
                            <td>
                              $ 5.00
                            </td>
                                  
                            
                            <td>
                              $ 4.50
                            </td>
                            </table>
                            </coolcode>
                            
                            
                            <h4>
                              css样式规则
                            </h4>
                            
                            
                            <h5>
                              css验证
                            </h5>
                            尽可能验证css的合法性，可以使用 
                            
                            <a href="http://jigsaw.w3.org/css-validator/" target="_blank">W3C CSS validator</a>。
                            <h5>
                              id和class名
                            </h5>
                            使用富有含义和通用的id和class名。
                            (ps：明河经常听周围的同事感慨，取好名字，也是个学问，有时候有些命名会让你很纠结，但好的命名的确可以提高可读性和可维护性。)
                            使用功能性和通用性的命名方式减少文档或模板的不必要的改动。
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
/* Not recommended: meaningless */
#yee-1901 {}

/* Not recommended: presentational */
.button-green {}
.clear {}
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
/* Recommended: specific */
#gallery {}
#login {}
.video {}

/* Recommended: generic */
.aux {}
.alt {}
</pre>
                            
                            
                            <h5>
                              id和class的命名风格
                            </h5>
                            id和class的命名在保持语义性的同时尽可能的短。
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
#navigation {}
.atr {}
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
#nav {}
.author {}
</pre>
                            可以缩写单词，但缩写后务必能让人明白其含义。比如author缩写成atr就非常费解。
                            
                            
                            <h5>
                              选择器
                            </h5>
                            避免出现多余的祖先选择器。（存在性能上的差异问题，可以看
                            
                            <a href="http://www.stevesouders.com/blog/2009/06/18/simplifying-css-selectors/" target="_blank"> performance reasons</a>）
                            避免出现元素标签名作为选择器的一部分。
                            不推荐的写法：
                            <pre class='brush:css;'>
ul#example {}
div.error {}
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
#example {}
.error {}
</pre>
                            
                            
                            <h5>
                              简化css属性写法
                            </h5>
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
border-top-style: none;
font-family: palatino, georgia, serif;
font-size: 100%;
line-height: 1.6;
padding-bottom: 2em;
padding-left: 1em;
padding-right: 1em;
padding-top: 0;
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
border-top: 0;
font: 100%/1.6 palatino, georgia, serif;
padding: 0 1em 2em;
</pre>
                            使用简洁的属性写法有利于提高可读性和解析效率。
                            
                            
                            <h5>
                              0和单位
                            </h5>
                            属性值为0时，忽略单位。
                            
                            
                            <pre class='brush:css;'>
margin: 0;
padding: 0;
</pre>
                            
                            
                            <h5>
                              属性值出现小数点忽略0
                            </h5>
                            
                            
                            <pre class='brush:css;'>
font-size: .8em;
</pre>
                            
                            
                            <h5>
                              url的引用
                            </h5>
                            使用url()时忽略刮号中的""。
                            
                            
                            <pre class='brush:css;'>
@import url(//www.google.com/css/go.css);
</pre>
                            
                            
                            <h5>
                              16进制符号
                            </h5>
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
color: #eebbcc;
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
color: #ebc;
</pre>
                            
                            
                            <h5>
                              前缀
                            </h5>
                            给选择器样式名增加前缀（可选）。
                            在大的项目（多人协作）中使用前缀可以减少样式冲突，同时可以明确选择器归属含义。
                            
                            
                            <pre class='brush:css;'>
.adw-help {} /* AdWords */
#maia-note {} /* Maia */
</pre>
                            （PS:一般明河使用前缀来定位样式的归属，比如.nav-item，表明是nav导航下的子元素样式。）
                            
                            
                            <h5>
                              id和class名的分隔符
                            </h5>
                            单词使用“-”来连接。
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
/* Not recommended: does not separate the words “demo” and “image” */
.demoimage {}

/* Not recommended: uses underscore instead of hyphen */
.error_status {}
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
#video-id {}
.ads-sample {}
</pre>
                            
                            
                            <h5>
                              Hacks 
                            </h5>
                            尽可能地避免使用hack的方式解决浏览器样式兼容性问题。
                            （ps：明河觉得这个很难，毕竟IE横在那里。）
                            尽量避免使用CSS filters。
                            
                            
                            <h4>
                              css格式规则
                            </h4>
                            
                            
                            <h5>
                              css属性按字母顺序书写
                            </h5>
                            （PS：排序忽略浏览器前缀，比如-moz-，-webkit-）
                            
                            
                            <pre class='brush:css;'>
background: fuchsia;
border: 1px solid;
-moz-border-radius: 4px;
-webkit-border-radius: 4px;
border-radius: 4px;
color: black;
text-align: center;
text-indent: 2em;
</pre>
                            
                            
                            <h5>
                              块内容缩进
                            </h5>
                            
                            
                            <pre class='brush:css;'>
@media screen, projection {

  html {
    background: #fff;
    color: #444;
  }

}
</pre>
                            缩进所有的
                            
                            <a href="http://www.w3.org/TR/CSS21/syndata.html#block" target="_blank">块状内容</a>。
                            <h5>
                              不可缺少的;
                            </h5>
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
.test {
  display: block;
  height: 100px
}
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
.test {
  display: block;
  height: 100px;
}
</pre>
                            
                            
                            <h5>
                              属性值前增加个空格
                            </h5>
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
h3 {
  font-weight:bold;
}
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
h3 {
  font-weight: bold;
}
</pre>
                            
                            
                            <h5>
                              分隔选择器
                            </h5>
                            不推荐的写法：
                            
                            
                            <pre class='brush:css;'>
a:focus, a:active {
  position: relative; top: 1px;
}
</pre>
                            推荐的写法：
                            
                            
                            <pre class='brush:css;'>
h1,
h2,
h3 {
  font-weight: normal;
  line-height: 1.2;
}
</pre>
                            
                            
<h5>
  1行只有一个css属性，二个规则间有一个空行
</h5>


<pre class='brush:css;'>
html {
  background: #fff;
}

body {
  margin: auto;
  width: 50%;
}
</pre>

