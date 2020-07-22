---
title: "自定义Hugo主题样式"
date: 2020-07-21T23:16:03+08:00
tags: [Hugo]
categories: [Blog] 
---

参照其他的优秀方案对even主题进行部分自定义修改。

<!-- more -->

## 修改主题配色

`even`主题的样式文件位于`${Hugo-Site}/themes/even/assets/sass/_variables.scss`

修改其中的`white`与`global-font-color`结果如下

```css
...
// ========== Color ========== //
$black: #002a35 !default;
$white: #fdf6e3 !default;
$light-gray: #e6e6e6 !default;
$gray: #cacaca !default;
$dark-gray: #8a8a8a !default;


// ========== Global ========== //
// Text color of the body.
$global-font-color: #657b83 !default;
...
```

## 添加搜索菜单

[lryong](https://github.com/lryong)在[issues#289](https://github.com/olOwOlo/hugo-theme-even/issues/289)中提到添加搜索功能的方案[hugo-search-fuse-js](https://github.com/kaushalmodi/hugo-search-fuse-js).

1. 下载[hugo-search-fuse-js](https://github.com/kaushalmodi/hugo-search-fuse-js)至`theme`目录
2. 添加"hugo-search-fuse-js"至`config.toml`
3. 新建一个`content/search.md`文件，用于配置search页面

上述步骤参照官方的[说明文档](https://github.com/kaushalmodi/hugo-search-fuse-js)进行即可。

由于站点的`layouts`目录优先级高于主题的`layouts`目录。因此，我们可以将自定义的内容放置于站点的`layouts`目录，而不用修改主题的内容，避免主题升级时出现问题。

将`${Hugo-sites}\themes\even\layouts\_default\baseof.html`拷至`${Hugo-sites}\layouts\_default`目录中。然后在合适的位置插入`main`与`footer`block. 修改后的`baseof.html`如下

```html
...
    <main id="main" class="main">
      <div class="content-wrapper">
        <div id="content" class="content">
            {{ block "main" . }}{{ end }}
			{{ block "content" . }}{{ end }}
        </div>
        {{ partial "comments.html" . }}
      </div>
    </main>

    {{ if not .Params.hideHeaderAndFooter -}}
    <footer id="footer" class="footer">
      {{ partial "footer.html" . }}
      {{ block "footer" . }}{{ end }}
    </footer>
    {{- end }}
...
```

在站点的配置文件`config.toml`中添加`Search`菜单

```toml
[[menu.main]]
  name = "搜索"
  weight = 50
  identifier = "search"
  url = "/search/"
```

## 添加评论区

`even`主题现已加入`utterances`支持，参照[文档](https://utteranc.es/)，其已经设定为GitHub App安装非常方便，步骤如下：

1. 在GitHub中新建一个repo (sr-c/blog-comment)用于存放评论
2. 在GitHub中一键安装utterances app
3. 在设置页面选择 repo: sr-c/blog-comment
4. 在站点`config.toml`中的`[params.utterances]`字段中填入`owner = "sr-c"`与`repo = "blog-comment"`

查看`themes/even/layouts/paritals/comments.html`, 可以看到其中已经集成了`utterances`的script字段，无需再做修改。

```html
  <!-- utterances -->
  {{- if .Site.Params.utterances.owner}}
    <script src="https://utteranc.es/client.js"
            repo="{{ .Site.Params.utterances.owner }}/{{ .Site.Params.utterances.repo }}"
            issue-term="pathname"
            theme="github-light"
            crossorigin="anonymous"
            async>
    </script>
    <noscript>Please enable JavaScript to view the <a href="https://github.com/utterance">comments powered by utterances.</a></noscript>
  {{- end }}
```

## 添加RSS订阅

参考[官方文档](https://www.gohugo.org/doc/templates/rss/)，添加`/layouts/rss.xml`文件

```xml
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>{{ with .Title }}{{.}} on {{ end }}{{ .Site.Title }}</title>
    <link>{{ .Permalink }}</link>
    <description>Recent content {{ with .Title }}in {{.}} {{ end }}on {{ .Site.Title }}</description>
    <generator>Hugo -- gohugo.io</generator>{{ with .Site.LanguageCode }}
    <language>{{.}}</language>{{end}}{{ with .Site.Author.email }}
    <managingEditor>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</managingEditor>{{end}}{{ with .Site.Author.email }}
    <webMaster>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</webMaster>{{end}}{{ with .Site.Copyright }}
    <copyright>{{.}}</copyright>{{end}}{{ if not .Date.IsZero }}
    <lastBuildDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</lastBuildDate>{{ end }}
    <atom:link href="{{.URL}}" rel="self" type="application/rss+xml" />
    {{ range first 15 .Data.Pages }}
    <item>
      <title>{{ .Title }}</title>
      <link>{{ .Permalink }}</link>
      <pubDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</pubDate>
      {{ with .Site.Author.email }}<author>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</author>{{end}}
      <guid>{{ .Permalink }}</guid>
      <description>{{ .Content | html }}</description>
    </item>
    {{ end }}
  </channel>
</rss>
```



## 参考来源

http://www.herbert.top:18080/2020/07/09/how_change_hugo_even_font/

https://github.com/olOwOlo/hugo-theme-even/issues/289#issuecomment-657229431

https://www.dazhuanlan.com/2019/12/05/5de8934e6f081/