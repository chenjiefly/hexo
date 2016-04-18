---
title: 搭建hexo + github 个人博客
date: 2016-04-14 13:44:29
tags: 
- hexo
categories:
- 博客
---

## 搭建hexo个人博客

### 一、官网

官网地址：[https://hexo.io/](https://hexo.io/)

### 二、本地环境
1、安装基本环境
* 安装git、node.js

* 安装hexo
```
npm install -g hexo
```

2、创建博客项目
* 使用hexo提供的脚手架创建
```
hexo init
```

* package.json
```json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": "3.2.0"
  },
  "dependencies": {
    "hexo": "^3.2.0",
    "hexo-deployer-git": "^0.1.0",
    "hexo-generator-archive": "^0.1.4",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.0",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.2.0",
    "hexo-renderer-marked": "^0.2.10",
    "hexo-renderer-stylus": "^0.3.1",
    "hexo-server": "^0.2.0"
  }
}
```

* 安装依赖包
```
npm install
```

* 本地启动博客服务
```
hexo server  
hexo s  # 简写命令
```
  打开[http://localhost:4000/](http://localhost:4000/)即可观察到博客欢迎页面


### 三、撰写博客
1、新建文章
* 执行下面命令可以在source/_posts下新建文章
```
hexo new "文章名"
hexo n  # 简写命令
```

2、生成静态网页
* 清除缓存后执行生成静态资源命令
```
# 执行下面的命令会在public目录下生成一系列html、css和js等文件
hexo clean  # 先清除缓存
hexo generate
hexo g  # 简写命令
```

### 四、部署到github
* 配置_config.yml中的github相关信息
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy: 
  type: git
  repo: git@github.com:chenjiefly/chenjiefly.github.io.git
  branch: master
```

* 执行部署命令
```
hexo deploy
hexo d  # 简写命令
```

* *注意*
  * hexo会自动部署到github.io仓库中
  * 博客项目应该托管在另外一个项目中，如my-blog.git，而不是github.io项目
  * 如果博客项目也托管在github.io仓库中
    * 那么当文章内容有修改执行git push和hexo deploy后很有可能会发生冲突
    * 应尽量避免这种冲突情况的发生，所以博客项目单独托管在其他仓库中

## hexo 主题

### 官方主题集

* 主题集地址：[https://github.com/hexojs/hexo/wiki/Themes](https://github.com/hexojs/hexo/wiki/Themes)

### 本博客使用的主题
* hexo-theme-transparent


## 添加文章目录

### 一、添加目录的模板
1、找到布局模板路径
* 文章目录只能添加到layout的post布局上，因为还有详情页上才需要看目录而且能够拿到标题列表
* 找到主题目录下的`layout/_partial/post/`目录

2、添加目录模板文件
* 在该目录下新建`toc.ejs`文件，内容如下
```html
<div id="toc" class="toc-article">
    <div class="toc-title">本文目录</div>
    <%- toc(item.content, {list_number: false}) %>
</div>
```
* Hexo提供了toc()帮助函数，用来获取文章的标题列表
```
<%- toc(str, [options]) %>
```

str是文章内容，options有两个参数，一个是class，也就是html标签的class值，默认为toc；一个是list_number，是否显示列表编号，默认值是true。

3、插入toc.ejs模板文件到布局`_partial/article.ejs`中
```
......
    <div class="entry">
      <% if (item.excerpt && index){ %>
        <%- item.excerpt %>
      <% } else { if (item.toc !== false) {%>
        <%- partial('post/toc') %>
      <% } %>
        <%- item.content %>
      <% } %>
    </div>
......
```

### 二、添加目录样式
* 由于布局限制，文章页面右侧为搜索、分类和标签，因此将目录放在文章右上角，并提供hover展开功能
* 样式代码请参考
    * [https://github.com/chenjiefly/my-blog/blob/master/themes/hexo-theme-transparent/source/css/_partial/toc.styl](https://github.com/chenjiefly/my-blog/blob/master/themes/hexo-theme-transparent/source/css/_partial/toc.styl)


文章目录相关代码参考自[http://www.ituring.com.cn/article/199624](http://www.ituring.com.cn/article/199624)


## 添加评论系统
### 一、注册Disqus
* 在Disqus上注册账号
* 注册完成后，选择universal code创建新站点后，会获得一串脚本代码，参考如下
```
<div id="disqus_thread"></div>
<script>
/**
* RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
* LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL; // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');

s.src = '//your_short_name.disqus.com/embed.js';

s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
```

### 二、添加评论
* 找到`layout/_partial/comment.js`目录
* 将上述代码添加到comment.js尾部即可
