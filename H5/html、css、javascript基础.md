html、css、javascript基础
===

###（一）Html 基础
####（1）概念
HTML（Hyper Text Markup Language），超文本标记语言，用来描述网页。
####（2）原理
Web 浏览器读取 HTML文档，使用标签来解释页面的内容并显示出来。
####（3）发展
Html->XHtml->Html5，XHtml是更严谨纯粹的Html版本，旧版Html语法不严谨可以允许标签不配对使用，XHtml则不行，以便实现更好的代码规范和更简单的终端解析处理。  
语法部分查看[HTML基础教程](http://www.w3school.com.cn/html/index.asp)

###（二）Css 基础
####（1）概念
CSS（Cascading Style Sheets）， 指层叠样式表，用来定义如何显示 HTML 元素。
####（2）作用
解决内容与显示的分离问题，还能统一设置，提高工作效率。
####（3）使用
1. 基本语法  
selector {property: value}，如h1 {color:red; font-size:14px;}
2. 选择器的使用  
元素选择器、选择器分组、类选择器、id 选择器等，选择器用来标识修改的对象。  
语法部分查看[CSS 基础教程](http://www.w3school.com.cn/css/index.asp)

（三）Javascript基础
####（1）概念
Javascript是一种动态类型、弱类型的解释型脚本语言，主要用来向HTML（标准通用标记语言下的一个应用）页面添加交互行为。
####（2）组成部分
![Alt text](https://github.com/wangpeifeng669/DevelopStudy/blob/master/H5/js%E7%BB%84%E6%88%90%E9%83%A8%E5%88%86.png)  
ECMAScript，描述了该语言的语法和基本对象。
文档对象模型（DOM），描述处理网页内容的方法和接口。
浏览器对象模型（BOM），描述与浏览器进行交互的方法和接口。
####（3）js 库
JavaScript 高级程序设计（特别是对浏览器差异的复杂处理），通常很困难也很耗时。
为了应对这些调整，许多的 JavaScript (helper) 库应运而生。
这些 JavaScript 库常被称为 JavaScript 框架。
如jQuery、Prototype、MooTools等。  
语法部分查看 [JavaScript教程](http://www.w3school.com.cn/js/index.asp)

附加说明：  
* 动态类型语言：在运行期进行类型检查的语言，也就是在编写代码的时候可以不指定变量的数据类型，比如Python和Ruby
* 静态类型语言：它的数据类型是在编译期进行检查的，也就是说变量在使用前要声明变量的数据类型，这样的好处是把类型检查放在编译期，提前检查可能出现的类型错误，典型代表C/C++和Java
* 强类型语言，一个变量不经过强制转换，它永远是这个数据类型，不允许隐式的类型转换。举个例子：如果你定义了一个double类型变量a,不经过强制类型转换那么程序int b = a无法通过编译。典型代表是Java。
* 弱类型语言：它与强类型语言定义相反,允许编译器进行隐式的类型转换，典型代表C/C++。

###（四）浏览器加载原理简介
####（1）组件
渲染引擎、网络请求模块、JS 解释器、数据存储等。
####（2）加载原理
1. 网络请求 html 数据
2. 渲染引擎解析 html 及 css
获取到 html 数据解析成 DOM 树，解析过程中碰到 css 引用及图片等数据，同时下载其他数据，进行多次渲染生成网页。
3. js 解释器解释执行 js 代码
4. 进行必要的数据存储
