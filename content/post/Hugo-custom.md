---
title: "自定义Hugo主题样式"
date: 2020-07-21T23:16:03+08:00
tags: [Hugo]
categories: [Blog] 
---

参照其他的优秀方案对even主题进行部分自定义修改。

<!-- more -->

# 修改配色方案

even主题的样式文件位于`${Hugo-Site}/themes/even/assets/sass/_variables.scss`

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

# 添加搜索栏

[lryong](https://github.com/lryong)在issues#289中提到添加搜索功能的方案[hugo-search-fuse-js](https://github.com/kaushalmodi/hugo-search-fuse-js).

1. 下载[hugo-search-fuse-js](https://github.com/kaushalmodi/hugo-search-fuse-js)至`theme`目录
2. 添加"hugo-search-fuse-js"至`config.toml`
3. 新建一个`content/search.md`文件，用于配置search页面

上述步骤参照官方的[说明文档](https://github.com/kaushalmodi/hugo-search-fuse-js)进行即可。

由于站点的`layouts`目录优先级高于主题的`layouts`目录。因此，我们可以将自定义的内容放置于站点的`layouts`目录，而不用修改主题的内容，避免主题升级时出现问题。

将`${Hugo-sites}\themes\even\layouts\_default\baseof.html`拷至`${Hugo-sites}\layouts\_default`目录中。然后在合适的位置插入`main`与`footer`block. 修改后的`baseof.html`如下

```html
{{ if ne .Site.Params.version "4.x" -}}
  {{ errorf "\n\nThere are two possible situations that led to this error:\n  1. You haven't copied the config.toml yet. See https://github.com/olOwOlo/hugo-theme-even#installation \n  2. You have an incompatible update. See https://github.com/olOwOlo/hugo-theme-even/blob/master/CHANGELOG.md#400-2018-11-06 \n\n有两种可能的情况会导致这个错误发生:\n  1. 你还没有复制 config.toml 参考 https://github.com/olOwOlo/hugo-theme-even/blob/master/README-zh.md#installation \n  2. 你进行了一次不兼容的更新 参考 https://github.com/olOwOlo/hugo-theme-even/blob/master/CHANGELOG.md#400-2018-11-06 \n" -}}
{{ end -}}
<!DOCTYPE html>
<html lang="{{ .Site.Language }}">
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <title>
    {{- block "title" . -}}
      {{ if .IsPage }}{{ .Title }} - {{ .Site.Title }}{{ else }}{{ .Site.Title }}{{ end }}
    {{- end -}}
  </title>
  {{ partial "head.html" . }}
</head>
<body>
  {{ partial "slideout.html" . }}
  <div class="container" id="mobile-panel">
    {{ if not .Params.hideHeaderAndFooter -}}
    <header id="header" class="header">
        {{ partial "header.html" . }}
    </header>
    {{- end }}

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

    <div class="back-to-top" id="back-to-top">
      <i class="iconfont icon-up"></i>
    </div>
  </div>
  {{ partial "scripts.html" . }}

</body>
</html>
```

在站点的配置文件`config.toml`中添加`Search`菜单

```toml
[[menu.main]]
  name = "搜索"
  weight = 50
  identifier = "search"
  url = "/search/"
```



# 参考来源

http://www.herbert.top:18080/2020/07/09/how_change_hugo_even_font/

https://github.com/olOwOlo/hugo-theme-even/issues/289#issuecomment-657229431