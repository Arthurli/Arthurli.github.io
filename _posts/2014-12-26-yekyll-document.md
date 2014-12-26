---
layout: post
title: Jekyll的使用
---
# Jekyll
------------------------------------
jekyll 安装  

~~~
$ gem install jekyll
~~~  

jekyll还有两个重要的命令

~~~
$ jekyll build  
# => 根据当前目录里的内容生成静态网页，网页存放到目录 ./_site
$ jekyll serve
# => 启动服务端部署_site内的网站到 http://localhost:4000,你可以在浏览器里输入这个URL来预览你的博客网站的效果
~~~  
之后再说说jeyll的文档结构 
   
~~~
.
├── _config.yml  //jekyll的配置文件，jekyll的很多参数可以在输入命令行时直接输入，也可以写入配置文件，简化繁琐的命令输入过程。
├── _drafts  //这个文件夹里面是用来存放草稿的，里面的文件不需要在文件名中加入固定格式的日期前缀。

|   ├── begin-with-the-crazy-ideas.textile  
|   └── on-simplicity-in-technology.markdown  
├── _includes  //这里面可以放置一些可重用的模块来加速开发，你可以方便的将里面的内容在多个文件里复用。譬如你也可以在多个页面中加入一块固定的模块，这个模块存放在文件_includes/file.ext，只需要在文件中插入liquid标签： % include file.ext %] 。
|   ├── footer.html
|   └── header.html
├── _layouts    //这个目录存放的是一些布局文件，我们的post文章里面可以使用YAML前缀格式来设置我们使用的布局。我们可以使用liquid标签 {{ content }} 在我们的web页中插入内容。
|   ├── default.html
|   └── post.html
├── _posts    //这里是用来放置你的文章的，里面的文件需要加入日期前缀，格式为： YEAR-MONTH-DAY-title.MARKUP 。前面的日期格式是固定的，jekyll可以自动提取出文件名中的日期前缀用来标识文件发布的日期，这个日期可以用来排序，分类，显示等。

|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
|   └── 2009-04-26-barcamp-boston-4-roundup.textile
├── _site    jekyll默认会将jekyll项目中的内容生成静态网页放到这个目录中，这个目录是可以自定义的，可以写到配置文件_config.yml中。默认这个目录会加入.gitignore文件中，里面的内容不会上传到github，github page会自动根据你上传的jekyll项目生成对应的网页，而不会直接展示你自己本地生成的网页。

└── index.html   //默认的主页文件，可以在文件头部加入YAML区域，内容中也可以加入Markdown等格式的内容。这个文件也会被jekyll转换成对应网页，不会直接显示你定义的内容。文件后缀可以是：.html, .markdown, .md, .textile 等。
~~~   
##发布

那么在整个需要post的文档前面 我们一般都是添加 YAML front matter 来进行记录或者预处理一些东西，格式如下  

~~~
---
layout: post
title: Blogging Like a Hacker
---
~~~
在这里我们可以自定义一些属性，如title，但是还是有一些全局的属性


变量              | 描述
:-----------------|:---
layout           |设置这个变量可以指定使用某个指定的布局文件。布局文件不需要加文件名后缀。布局文件需要放到_layouts文件夹中。
permalink        |发布的post的url默认会是/year/month/day/title.html的格式，用户可以设置这个变量来设置静态URL。
published        |post设置这个变量为false，那么这个post在产生网页时不会发布。
category，categories|Instead of placing posts inside of folders, you can specify one or more categories that the post belongs to. When the site is generated the post will act as though it had been set with these categories normally. 类别(Categories)可以使用YAML列表的形式或者使用空格分隔的字符串形式。
tags             |与分类(categories)类似，可以定义一个或多个标签(tags)，同样的也可以用YAML列表形式或空格分隔的字符串形式

说完了post文件的头，那么我们就可以说说我们的博客本身了
首先这类的文件可以分成两种，markdown 和 html， 命名如下

~~~
2011-12-31-new-years-eve-is-awesome.md
2012-09-12-how-to-write-a-blog.textile
~~~
###内容格式  
所有的博客文件必须以YAML front-matter开头。然后剩余部分用户就可以自己选择使用哪种格式了。Jekyll提供了两种流行的内容标记格式：Markdown和Textile。每种格式都有自己的标记内容的方法，你需要熟悉这两种格式并选择你自己需要的格式。

###在文章中包含图片及其他资源  
有时候用户需要在post中添加图片，下载文件或其他内容。因为语法的不同，Markdown和Textlie的链接的表达方式不同，因此存储图片，二进制文件等的位置也有所不同。

由于Jekyll的灵活性，有很多不同的解决方案来解决这个问题。通用的解决方案是在根目录创建一个文件夹，例如assets或者downloads，图片，下载文件和其他资源就可以放到这个目录下。这样，post中就可以以根目录为起始路径来链接到这些资源文件。当然，这要依赖与你的网站的（子）域名和路径的配置。下面给出一些利用site.url变量来实现的链接(使用Markdown)。

~~~
… which is shown in the screenshot below:
![My helpful screenshot]({{ site.url }}/assets/screenshot.jpg)
… you can [get the PDF]({{ site.url }}/assets/mydoc.pdf) directly.
~~~
post还可以使用一些属性 url,title,excerpt

###代码高亮

~~~
{% highlight ruby %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}
~~~
##网站的组织形式  
很多网站默认启动服务会找HTML文件index.html作为网站的首页来展示。Jekyll也一样，除非你自己在web服务器上配置了其他文件，否则Jekyll也会默认将index.html文件作为首页。

在首页中使用布局layouts
Jekyll项目中的任何HTML文件都可以使用布局(layouts)和包含文件(includes)，包括首页。一些通用的元素，例如头部和尾部，可以抽取出来做到布局中，这样方便开发，网站的风格也能统一。

###额外的页面放置在哪？

在哪里放置HTML页面文件取决与你想让你的页面如何工作。有两种主要的创建页面的方式：  
放置HTML文件到网站根目录，每个页面单独命名。
在网站根目录为每个页面创建一个单独命名的文件夹，每个文件夹里放置一个名为index.html的文件  
这两种方式都能很好的工作，也可以混合使用，唯一的不同就是最终访问路径URL的不同。

~~~
.
|-- _config.yml
|-- _includes/
|-- _layouts/
|-- _posts/
|-- _site/
|-- about.html    # => http://yoursite.com/about.html
|-- index.html    # => http://yoursite.com/
└── contact.html  # => http://yoursite.com/contact.html

.
├── _config.yml
├── _includes/
├── _layouts/
├── _posts/
├── _site/
├── about/
|   └── index.html  # => http://yoursite.com/about/
├── contact/
|   └── index.html  # => http://yoursite.com/contact/
└── index.html      # => http://yoursite.com/
~~~

##变量
全局变量

变量  |说明
:-------|:-------
site   |_config.yml文件中配置的全网站范围的信息和配置项。更详细的内容参考下面。
page   |page专用的信息以及YAML Front Matter。用户在YAML front matter中设置的自定义变量也可以在这里使用。更详细的内容参考下面。
content|在布局文件中，post和page的内容会被封装起来。这个变量在post和page文件中没有定义。
paginator|这个变量在变量paginate变量被设置后才产生作用。详细内容参考Pagination

Site变量 
 
变量        |说明
:------------|:------
site.time   | 当前时间(当你运行jekyll时)
site.pages  |所有pages的列表
site.posts  |所有posts的列表，按时间顺序倒序排列。
site.related_posts  |如果当前处理的页面是post，则这个变量包含了至多十个的相关的posts的列表。 By default, these are low quality but fast to compute. For high quality but slow to compute results, run the jekyll command with the --lsi (latent semantic indexing) option.
site.categories.CATEGORY  |所有指定分类CATEGORY中的posts的列表。
site.tags.TAG  |所有包含标签TAG的posts的列表。
site.[CONFIGURATION_DATA]  |所有通过命令行以及通过_config.yml设置的变量都可以通过变量site来访问。例如，在配置文件中设置了url:http://mysite.com，那么在posts和pages中可以通过变量site.url来使用设置的变量值。在watch模式下，Jekyll并不会去解析_config.yml文件，只在Jeykll服务启动的时候才会解析，因此如果你改变了配置项的值，你需要重新启动Jekyll来使改变生效。

Page变量

变量         |说明
:--------------|:--------
page.content  |The un-rendered content of the Page.
page.title    |page的标题
page.excerpt  |The un-rendered excerpt of the Page.
page.url      |post的URL，不包含域名，以"/"开头。例如：/2008/12/14/my-post.html
page.date     |指定的日期。这个变量可以在post的front matter中指定一个新的日期/时间来覆盖已有的日期，格式为：YYYY-MM-DD HH:MM:SS(假定是UTC世界标准时间)，或者：YYYY-MM-DD HH:MM:SS +/-TTTT(使用一个相对UTC的偏移量来指定时区，例如：2008-12-14 10:30:00 +0900）。
page.id       |post的唯一标识ID(对RSS feeds有用)。例如：/2008/12/14/my-post。
page.categories  |post所属的类别的列表。类别(categories)派生于_posts之上的目录结构。例如，一个设置page.categories变量为['work, 'code']的post将位于/work/code/_posts/2008-12-24-closures.md。这些可以在YAML Front Matter中进行设置。
page.tags  |post所属的tags的列表。可以在YAML Front Matter中进行设置。
page.path  |原生post或者page文件的路径。用法实例：链接到github上的page或者post的源文件。可以在YAML Front Matter中覆盖这个变量。

Paginator

|变量    |说明   |
|:----------|:------|
|paginagor.per_page  |每个page页面包含的posts数目。|
|paginator.posts  |page页面可用的posts |
|paginator.total_posts  |posts的总数 |
|paginator.total_pages  |pages的总数 |
|paginator.page  |当前page页面的编号 |
|paginator.previous_page  |前一个page页面的编号 |
|paginator.previous_page_path  |前一个page页面的path路径 |
|paginator.next_page  |后一个page页面的编号 |
|paginator.next_page_path  |后一个page页面的path路径 |