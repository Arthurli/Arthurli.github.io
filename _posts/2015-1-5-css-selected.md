---
layout: post
title: CSS选择器
---
#CSS选择器  
##先简单介绍几种追简单的选择器

~~~
//元素选择器
html {
	color:black;
	}
	
//选择器分组
h1 ,h2 {
  font: 28px Verdana;
  color: blue;
  background: red;
  }

//派生选择器，也可以叫做上下文选择器，下面说的是列表中的strong
li strong {
    font-style: italic;
    font-weight: normal;
  }
  
//id选择器，各种选择器可以组合使用
#red {
	color:red;
	}
#sidebar p {
	font-style: italic;
	text-align: right;
	margin-top: 0.5em;
	}

//类选择器
.center {
	text-align: center
	}
td.fancy {
	color: #f60;
	background: #666;
	}
  
//通配符选择器
* {
  color:red;
  }

//属性选择器
[lang|=en] 
{ 
  color:red; 
}
input[type="text"]
{
  width:150px;
  display:block;
  margin-bottom:10px;
  background-color:yellow;
  font-family: Verdana, Arial;
}
~~~

属性选择器的语法如下

选择器 |描述
------|---
[attribute]|	用于选取带有指定属性的元素。
[attribute=value]|	用于选取带有指定属性和值的元素。
[attribute~=value]|	用于选取属性值中包含指定词汇的元素。
[attribute\|=value]|	用于选取带有以指定值开头的属性值的元素，该值必须是整个单词。
[attribute^=value]|	匹配属性值以指定值开头的每个元素。
[attribute$=value]|	匹配属性值以指定值结尾的每个元素。
[attribute*=value]|	匹配属性值中包含指定值的每个元素。

##高级语法
属性选择器可以同时选择多个属性

~~~  
a[href][title]
{
color:red;
}
~~~  
有一点需要注意的是，派生选择器无法确定几层的子类，也就是说所有包括的子类都将要被选择，所以这里还可以使用子类选择器

~~~
h1 > strong {
	color:red;
	}
~~~
选了子类，那么我们想选择他的邻居怎么办，对使用相邻兄弟选择器（Adjacent sibling selector），可选择紧接在另一元素后的元素，且二者有相同父元素。

~~~
h1 + p {
	margin-top:50px;
	}
~~~
之后要说就是伪类的概念，CSS 伪类用于向某些选择器添加特殊的效果。

~~~
伪类的语法：
selector : pseudo-class {property: value}
CSS 类也可与伪类搭配使用。
selector.class : pseudo-class {property: value}
~~~
属性|描述|CSS
----|----|----
:active|	向被激活的元素添加样式。|	1
:focus|	向拥有键盘输入焦点的元素添加样式。|	2
:hover	|当鼠标悬浮在元素上方时，向元素添加样式。	|1
:link|	向未被访问的链接添加样式。	|1
:visited|	向已被访问的链接添加样式。|	1
:first-child	|向元素的第一个子元素添加样式。|	2
:lang|	向带有指定 lang 属性的元素添加样式。|	2

与伪类相似的还有伪元素，CSS 伪元素用于向某些选择器设置特殊效果。

属性|描述|CSS
-----|-----|-----
:first-letter|	向文本的第一个字母添加特殊样式。	|1
:first-line|	向文本的首行添加特殊样式。|	1
:before|	在元素之前添加内容。|	2
:after|	在元素之后添加内容。|	2

