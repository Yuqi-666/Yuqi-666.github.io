---
title: 使用hugo及hugo blox搭建个人博客
summary: 个人对hugo及hugo blox关于搭建个人博客的一些见解
date: 2025-09-24

authors: 
    - admin
tags: 
    - hugo
    - hugo blox
categories:
    - web
---

{{< toc mobile_only=true is_open=true >}}

## 引言

对于能读到这篇文章的人，我默认你有一定技术基础，动手能力，已配置代理。

如果你需要搭建个人博客，我推荐你使用hugo。

hugo是一种静态网站生成器，它能将markdown文件转换成网站页面，这意味着你不需要面对html，css文本，手动管理庞大的资源，而是抛之脑后，更自然的写作。

hugo很快，由go编写，使用多线程。

对于你的网站，你并非只能更改你的内容页面，如果你想get your hands dirty，你想进去控制所有你网站的东西，在网站融入你的风格，你也可以做到，通过改变html，css之类的。

事实上，在一开始你就需要准备一个[**theme**](#theme)（一个文件包），theme决定了网站样式，提供了hugo构建网站所需的几乎所有部件，包括**template**和**layout**等等，theme本身就是个迷你hugo项目。

对于只想写博客的人，你可以在网上下载[现成的theme](https://themes.gohugo.io/)，对于有定制化需求的人，你可以更改网上的theme或者干脆自己写一个。

因为theme本身就是个迷你hugo项目，你可以直接在上面构建你的网站了，当然你可以将它归纳进hugo项目中的themes文件夹，这暗示着你的网站可以拥有**多主题**

hugo对[**content**](#content)中文档的组织有基本的形式，但是，很多theme有自己的个性，正如前面所说，theme极大扩展hugo创建网站的形式，文档的组织方式也会有增加。

比如我们之后要说的[**hugo blox**](#hugo-blox)，原hugo academic theme，后来持续增长，建立了自己的社区，其特性blox会着重讲解。

因此你需要参考相应theme的教程，组织自己的博客文档，不过基础的组织形式任然适用。

在hugo中，对于所有md文件，都需要加上[**frontmatter**](#frontmatter)，它是一种YAML格式的元数据，被上下两行`---`包裹，它用来存储相应md文件的基础数据，比如说标题、日期、作者、标签之类的，它决定了该页面将如何被检索，筛选以及渲染等等。

但在每次创建md文件时加上frontmatter实在是太麻烦了，使用命令`hugo new your_blog.md`是更好的选择，因为hugo会自动生成带frontmatter的md文件，自动生成什么样的frontmatter是可以定义的，在[**Archetypes**](#archetypes)中你可以定义这些原型md文件。
f
在编写md正文时，如果你不想让内容只有纯文字，需要加入一些效果，或者你想嵌入一些图片，bilbili中的视频等，你大可以直接向其中添加html代码，就像iframe那样，它们能被正确渲染出来，这没问题，但如果你想保持md内容的整洁美观，你可以尝试使用[**shortcodes**](#shortcodes)，将起作用的html代码以html文件形式放入shortcodes文件夹中，并用shortcodes的形式来调用它们，hugo自带部分shortcodes，许多theme也为你准备了一些。

你可以为文章加标签并分类，hugo在frontmatter中提供了tags和categories来归档博客。在[**Taxonomies**](#taxonomies)了解如何自定义归档分类法。

以上足够一个普通blogger流畅地使用hugo来构建博客了。

如果你希望创造一个独一无二的theme，查看[**extend**](#extend)

## Theme

本质是一套预定义的 **模板文件（HTML/CSS/JS）**、**配置示例**和**静态资源**，存放在站点根目录的 themes/ 文件夹中。每个主题通过独立的文件夹区分（如 themes/even/、themes/loveit/）。

Hugo 站点默认读取 一个主题 的模板，但通过配置和模板优先级规则，可以实现 “多主题” 的灵活应用
## frontmatter
## shortcodes
短代码由包裹，名称使用相应html文件名，可以填入参数
例如`{{</* youtube 2xkNJL4gJ9E */>}}`，2xkNJL4gJ9E为youtube视频id
{{< youtube 2xkNJL4gJ9E >}}
## Archetypes
在 Hugo 中，Archetypes（原型）是用于创建新内容时的模板文件，它们定义了新内容文件的默认元数据（front matter）和初始结构。使用 Archetypes 可以确保网站内容格式的一致性，提高创作效率。

`hugo new`时会将一级文件夹名称与Archetypes文件夹中md文件名称比对，已确定该使用哪种原型文件

例如，当你要求`hugo new posts/tutorials/index.md`时，hugo会在archetypes文件夹中寻找名为posts.md的原型文件，若无，则寻找单数形式的名为post.md的原型文件，若无，则使用default.md创建。

## hugo blox
## Taxonomies
hugo自带tags和categories分类法

例如
```yaml
tags: 
    - hugo
    - hugo blox
categories:
    - Web
或

tags: ["hugo","hugo blox"]
categories: ["Web"]
```

hugo会自动生成路径为/tags/和/categories/的标签展示网页，以及路径为具体分类的路径为/tags/your_tag的list网页。

但如果你希望添加新的分类，比如说按心情分类归档，你就需要在配置文件中配置Taxonomies，**配置后需要重新启动hugo server才能生效**。

对于hugo.yaml文件
```yaml
taxonomies:
  author: authors
  tag: tags
  category: categories
  mood: moods
```

对于config.toml文件
```toml
[taxonomies]
    tag = "tags"
    category = "categories"
    mood = "moods"

```
注意：左侧是单数，右侧是复数，tag和category必须要添加

## content

hugo将页面分成了single 和 list 两种。

hugo会自动为md文件创建single页面。

自动为content（基于content目录创建的为首页）及其子一级目录创建list页面，以陈列该目录下的所有页面选项。你也可以通过创建_index.md来创建list页面，这个页面会取代基于_index.md所在目录生成的list页面。

所以如果你想让list页面不只是单调的陈列页面选项的话，你应该在相应目录下创建_index.md，并增加自定义元素。

还有一种创建list页面的方式会在[**hugo blox**](#hugo-blox)中讲到。


## Extend

为了能让我们自己修改theme有关的文件，我们需要先探索下theme中有什么，以及对应的作用是什么。

以下是theme文件夹中的结构
```markmap {height="800px"}
- theme
    - archetypes
    - assets
        - css
        - dist
        - js
        - media
    - content
    - data
    - images
    - scripts
    - static
    - layouts
        - _default
            - _markup
            - baseof.html
            - graph.html
            - graph.json
            - home.html
            - list.html
            - list.json
            - single.html
            - taxonomy.html
        - notes
            - list.html
        - partials
            - functions
            - head
            - notes
            - page
            - posts
            - svg
            - footer.html
            - graph.html
            - head.html
            - header.html
            - menu.html
            - pagination.html
            - terms.html
        - posts
            - list.html
        - shortcodes
    
```

这里我们只关注layouts和assets/css,是它们影响了网站的布局和样式

我们可以发现single和list的字眼，结合html可知，他们提供了构建single页面和list页面的布局，因为以它们构建的页面除了页面内容不同外其他都一样，像承载内容的容器，所以叫做template。

### template

