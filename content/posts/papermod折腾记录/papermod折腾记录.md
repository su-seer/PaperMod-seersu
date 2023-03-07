---
title: "PaperMod主题折腾记录" # 日期 #
slug: "papermod" # 链接名称 #
summary: "本篇文章只为记录本站主题样式更改记录" # 文章简介 #
author: ["SeerSu"] # 作者 #
date: 2022-12-25T14:22:44+08:00 # 日期 #
cover:
    image: "https://bu.dusays.com/2022/12/25/63a7fb07a43da.webp" # 图片链接 #
    alt: "papermod" # 图片名称 #
    hidden: true # 文章内不显示/显示 #
categories: [hobby] # 分类 #
tags: [papermod, 折腾] # 标签 #
ShowToc: true #显示toc目录树 #
TocOpen: true # toc目录树展开 #
katex: false # 数学公式 #
mermaid: false # 流程图 #
draft: false # 草稿与否 #
weight: false # 置顶 #
comments: false # 评论打开或关闭
hidemeta: false # 隐藏页面元素如：作者、时间等 #
# description: "Desc Text." # 单页面标题 #
---

<img src="https://bu.dusays.com/2023/01/10/63bd09ab9eb2f.jpg">
<br/>

**<center><font color="red"> 本文旨在记录本站主题样式更改记录 </font></center>**

* 在修改站点样式及其它设置时，使用此命令`hugo server --disableFastRender`实时预览。

### 统计文章篇数和字数

**实现步骤：**

#### 1、新建统计模板

 `./layouts/partials/stat.html`

```html
{{- $scratch := newScratch -}}
{{- $kind := .Kind }}
{{- $pages := where site.RegularPages "Type" "in" site.Params.mainSections -}}

{{- if eq $kind "pages" -}}
{{-   $pages = .pages -}}
{{- else if or (eq $kind `term`) (eq $kind `section`) -}}
{{-   $pages = union .RegularPages .Sections -}}
{{- end -}}

{{- range $pages -}}
{{- $scratch.Add "words" .WordCount -}}
{{- end -}}

{{- $words := $scratch.Get "words" | default 0 }}
{{- printf "%s 篇 %s 字" ((len $pages) | string) ($words | string) -}}
```
#### 2、编辑模板

 `./layouts/_default/list.html`和 `./layouts/_default/archives.html`
```html
<!-- 列表模板-->

  {{- if (.Param "ShowStatInSectionTermList" | default true) }}
  <sup class="archive-count">&nbsp;&nbsp;{{- partial "stat.html" . }}</sup>
  {{- end }}
<!-- h1 这行上面 -->
</h1>
```

```html
<!-- 归档模板-->

  <h2 class="archive-year-header">
    {{- replace .Key "0001" "" }}
    {{- if (site.Params.ShowStatInArchive | default true) }}
    <sup class="archive-count">&nbsp;&nbsp;{{- partial "stat.html" (dict "Kind" "pages" "pages" .Pages) }}</sup>
    {{- else }}
    <sup class="archive-count">&nbsp;&nbsp;{{ len .Pages }}</sup>
    {{- end }}
  </h2>
  ```
#### 3、使用方法

  可直接使用，不需要修改，也可通过以下配置隐藏统计 `./config.yml`
```yml
  params:
    ShowStatInSectionTermList: false
    ShowStatInArchive: false
```

***

### 搜索页展示标签列表

**需求**：在搜索页展示标签列表

**原因**：标签页（/tags）需要一个入口，但是 PaperMod 默认的标签页比较简洁，占用一个菜单感觉有点浪费，而搜索页面内提供标签列表似乎挺合理。

**实现步骤**

#### 1、编辑搜索页面模板

`./layouts/_default/search.html`
```html
{{- if not (.Param "hideTags") }}
{{- $taxonomies := .Site.Taxonomies.tags }}
{{- if gt (len $taxonomies) 0 }}
<h2 style="margin-top: 32px">{{- (.Param "tagsTitle") | default "tags" }}</h2>
<ul class="terms-tags">
    {{- range $name, $value := $taxonomies }}
    {{- $count := .Count }}
    {{- with site.GetPage (printf "/tags/%s" $name) }}
    <li>
        <a href="{{ .Permalink }}">{{ .Name }} <sup><strong><sup>{{ $count }}</sup></strong></sup> </a>
    </li>
    {{- end }}
    {{- end }}
</ul>
{{- end }}
{{- end }}
{{- end }}{{/* end main */}}  <!-- 在最后一行前加入上面的代码 -->
```
#### 2、使用方法

`./config.yml`

```yml
# 设置文章分类 https://gohugo.io/content-management/taxonomies
taxonomies:
    tag: tags
    series: series
```

设置文章所属标签，比如本篇是这样：
```md
---
title: "PaperMod 搜索页展示标签列表"
date: 2022-06-09
draft: false
tags: ["hugo", "paper-modx"]
series: ["PaperModx 定制搜索页"]
---
```

可通过以下配置隐藏标签列表

`./content/search.md`

```md
---
title: "搜索"
layout: "search"
# 是否隐藏标签
hideTags: true
tagsTitle: 标签 
---
```

***

### 列表文章标题后添加标识

**需求**：自动为列表中的文章添加对应的标识：`[置顶]`、`[转载]`

1、将`./themes/PaperMod/layouts/_default/list.html`复制到`./layouts/_default/list.html`

```html
  <header class="entry-header">
    <h2>
      {{- .Title }}
      <!-- 加入下面这行，表示 weight 值为 1 的文章标题后添加 [置顶] 标识 -->
      {{- if (eq .Weight 1) }}<sup><span class="x-entry-istop">&nbsp;&nbsp;[置顶]</span></sup>{{- end }}
      <!-- 加入下面这行，表示 outer 值为 true 的文章标题后添加 [转载] 标识 -->
      {{- if (.Param "outer") }}<sup><span class="x-entry-isouter">&nbsp;&nbsp;[转载]</span></sup>{{- end }}
      {{- if .Draft }}<sup><span class="entry-isdraft">&nbsp;&nbsp;[draft]</span></sup>{{- end }}
    </h2>
  </header>
  ```

  2、将`./themes/PaperMod/layouts/_default/archives.html`复制到`./layouts/_default/archives.html`

```html
          <h3 class="archive-entry-title">
          {{- .Title | markdownify }}
          <!-- 归档页面显示 [置顶] 标识怪怪的，所以这里没添加 -->
          <!-- 加入下面这行，表示 outer 值为 true 的文章标题后添加 [转载] 标识 -->
          {{- if (.Param "outer") }}<sup><span class="x-entry-isouter">&nbsp;&nbsp;[转载]</span></sup>{{- end }}
          {{- if .Draft }}<sup><span class="entry-isdraft">&nbsp;&nbsp;[draft]</span></sup>{{- end }}
        </h3>
```

3、修改样式 `./assets/_css/extended/custom.css`
```css
.x-entry-isouter {
  font-size: 14px;
  color: #cd201f;
}

.x-entry-isouter {
  font-size: 14px;
  color: #2db7f5;
}
```

4、使用方法：使用姿势：在文章页面修改配置来开关标识

```md
---
title: "测试"
date: 2022-06-03
draft: false
# 1 表示置顶
weight: 1
# true 表示是转载
outer: true
---
```

***

### 添加文章最后更新时间

1、根目录打开`config.yml`，最后位置添加以下代码

```yml
#显示文章更新时间：依次从文件git提交记录获取；从文件中lastmod字段获取；从文件修改时间获取
frontmatter:
  lastmod: ['lastmod', ':git', ':fileModTime', ':default']
```

2、在`./themes/PaperMod/layouts/_default/`目录下，找到`single.html`在合适的位置插入代码

```html
  {{ if ne (.Lastmod.Format "2022-07-17") (.Date.Format "2022-07-17") }}
  <p style="text-align:right">
  <b> 最后更新于： {{ .Lastmod.Year }} 年 {{ printf "%d" .Lastmod.Month }} 月 {{ .Lastmod.Day }} 日</b>
  </p>
  {{ end }}
```

### 添加下雪效果

把代码添加到网站的body标签里或者自定义js里就可以了！[这里](https://hcodes.github.io/demo-snowflakes/)可以自己调整雪花的颜色和速度，大小等。

```html
<script src="https://unpkg.com/magic-snowflakes/dist/snowflakes.min.js"></script>
<script>
    // https://hcodes.github.io/demo-snowflakes/
    var sf = new Snowflakes({
        color: "#cccccc"
    });
</script>
```

### 自定义页面底部信息

1. 将./themes/PaperMod/layouts/partials/footer.html 复制到./layouts/partials/footer.html
2. 新建自定义底部信息模板

```html
./layouts/partials/footer_info.html
<!-- 将 footer.html 中 <footer> 的代码移至 footer_info.html -->
<footer class="footer">
    {{- if site.Copyright }}
    <span>{{ site.Copyright | markdownify }}</span>
    {{- else }}
    <span>&copy; {{ now.Year }} <a href="{{ "" | absLangURL }}">{{ site.Title }}</a></span>
    {{- end }}
    <span>
        Powered by
        <a href="https://gohugo.io/" rel="noopener noreferrer" target="_blank">Hugo</a> &
        <a href="https://github.com/loyayz/hugo-PaperModx/" rel="noopener" target="_blank">PaperModx</a>
    </span>
</footer>
```
3. 编辑模板`./layouts/partials/footer.html`

```html
<!-- 添加引用 footer_info.html -->
{{- if not (.Param "hideFooter") }}
{{- partial "footer_info.html" . }}
{{- end }}
```
和`./layouts/partials/footer_info.html`

```html
<footer class="footer">
    <span>&copy; {{ now.Year }} <a href="/about/">loyayz</a></span>
    <span>
        Powered by
        <a href="https://gohugo.io/" rel="noopener noreferrer" target="_blank">Hugo</a> &
        <a href="https://github.com/loyayz/hugo-PaperModx/" rel="noopener" target="_blank">PaperModx</a>
    </span>
    <span>·&nbsp;<a href="/copyright/">版权说明</a></span>
</footer>
```
**注：可以直接修改./layouts/partials/footer.html，我是为了方便后续修改而这样拆分**

### 文章元数据前添加图标
1. 修改网站配置`./config.yml`

```yml
params:
    hideMeta: false
    ShowReadingTime: true
    ShowWordCount: true
```

2. 新建元数据模板文件`./layouts/partials/post_meta.html`

```html
{{ $scratch := newScratch }}

{{ if not .Date.IsZero }}
{{ $scratch.Add "meta_keys" (slice "date") }}
{{ $scratch.SetInMap "meta_items" "date" (slice "calendar" (.Date | time.Format (default "January 2, 2006" site.Params.DateFormat))) }}
{{ end }}

{{ if (.Param "ShowWordCount") }}
{{ $scratch.Add "meta_keys" (slice "words") }}
{{ $scratch.SetInMap "meta_items" "words" (slice "file-text" (i18n "words" .WordCount | default (printf "%d words" .WordCount))) }}
{{ end }}

{{ if (.Param "ShowReadingTime") }}
{{ $scratch.Add "meta_keys" (slice "read_time") }}
{{ $scratch.SetInMap "meta_items" "read_time" (slice "clock" (i18n "read_time" .ReadingTime | default (printf "%d min" .ReadingTime))) }}
{{ end }}

{{ with (partial "author.html" .) }}
{{ $scratch.Add "meta_keys" (slice "author") }}
{{ $scratch.SetInMap "meta_items" "author" (slice "avatar" .)}}
{{ end }}

{{ $metaItems := $scratch.Get "meta_items" }}
{{ range ($scratch.Get "meta_keys") }}
{{ $icon := (partial "extend_svg.html" (index $metaItems . 0)) }}
{{ $text := (printf "<span>%s</span>" (index $metaItems . 1)) }}
{{ $scratch.Add "meta" (slice (printf "<span class=\"x-post-meta-item\">%s%s</span>" $icon $text )) }}
{{ end }}

{{ with ($scratch.Get "meta") }}
{{ delimit . "&nbsp;·&nbsp;" }}
{{ end }}
```

3. 新建图标模板文件`./layouts/partials/extend_svg.html`

```html
{{ $icon_name := . }}
{{ if (eq $icon_name "") }}
{{ else if (eq $icon_name "calendar") }}
<svg> ... </svg>
{{ else if (eq $icon_name "file-text") }}
<svg> ... </svg>
{{ else if (eq $icon_name "clock") }}
<svg> ... </svg>
{{ else if (eq $icon_name "avatar") }}
<svg> ... </svg>
{{ else if (eq $icon_name "tag") }}
<svg> ... </svg>
{{ else }}
{{ partial "svg.html" (dict "name" $icon_name) }}
{{ end }}
```

5. 添加样式`./assets/css/extended/custom.css`

```html
.x-post-meta-item {
  display: inline-block;
}

.x-post-meta-item svg {
  width: 1em;
  height: 1em;
  display: inline-block;
  vertical-align: -0.15em;
  margin-right: 4px;
}
```

---
1. 点击小圆点，围住小猫。
2. 你点击一次，小猫走一次。
3. 直到你把小猫围住（赢），或者小猫走到边界并逃跑（输）。


<div align="center">
  <div id="catch-the-cat"></div>
</div>

<script src="//cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/js/catch-the-cat/phaser.min.js"></script>
<script src="//cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/js/catch-the-cat/catch-the-cat.js"></script>
<script defer="defer" src="//cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/js/catch-the-cat/game.js"></script>