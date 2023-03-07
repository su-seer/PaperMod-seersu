---
title: Hugo shortcodes短代码
slug: hugo-shortcodes
summary: 本站所有实现的一些功能的测试，以及后续会增加实现的方法。
author: ["SeerSu"]
date: 2022-10-26T16:02:31+08:00
cover:
    image: "https://bu.dusays.com/2022/11/19/6378864d9f03c.webp"
    alt: "hugo"
    hidden: true # 文章内显示/不显示 #
categories: [hobby]
tags: [hugo, 折腾]
TocOpen: true # toc目录树展开 #
katex: false # 数学公式 #
mermaid: false # 流程图 #
draft: false # 草稿与否 #
comments: false # 评论打开或关闭
hidemeta: false # 隐藏页面元素如：作者、时间等 #
# description: "Desc Text." # 单页面标题 #
---
主要是将本站使用的一些shortcodes留存标记下，使用起来方便，也算是给来访的朋友的一些小经验

### 文章实现图片轮播
来自[小球飞鱼](https://mantyke.icu/posts/2021/cf2cf0fb/)

随便放几张图看下效果
{{< imgloop "https://bu.dusays.com/2022/07/17/62d41bcfeb7d6.jpg,https://bu.dusays.com/2022/07/17/62d41ba0f3035.png,https://bu.dusays.com/2022/07/31/62e679354b58b.jpg,https://bu.dusays.com/2022/07/17/62d41c4448cff.jpg,https://bu.dusays.com/2022/07/17/62d4191b6bcb0.jpg" >}}

加入的功能分别有：图片无限循环、鼠标滚轮 / 键盘方向键切换、懒加载和自动调节高度。

**步骤1：创建短代码模板**

在`layou/shortcodes`文件夹中创建`imgloop.html`短代码模板，内容如下，相关功能加入参考 [Swiper3.x](https://3.swiper.com.cn/api/start/2014/1218/140.html) 文档。
{{< admonition type=tip title="模板代码" open=false >}}
```html
{{ if .Site.Params.enableimgloop }}
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Swiper/3.4.2/css/swiper.min.css">
    <!-- Swiper -->
    <div class="swiper-container">
        <div class="swiper-wrapper">
            {{$itItems := split (.Get 0) ","}}
            {{range $itItems }}
            <div class="swiper-slide">
                <img src="{{.}}" alt="">
            </div>
            {{end}}
        </div>
        <!-- Add Pagination -->
        <div class="swiper-pagination"></div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/Swiper/3.4.2/js/swiper.min.js"></script>
     <!-- Initialize Swiper -->
     <script>
        var swiper = new Swiper('.swiper-container', {
            pagination: '.swiper-pagination',
            paginationClickable: true,
        //自动调节高度
        autoHeight: true,
        //键盘左右方向键控制
        keyboardControl : true,
        //鼠标滑轮控制
        mousewheelControl : true,
        //自动切换
        //autoplay : 5000,
        //懒加载
        lazyLoading : true,
		lazyLoadingInPrevNext : true,
		//无限循环
		loop : true,
        });
        </script>
{{ end }}
```
**步骤2：加入css**

在`assets/scss/cutom.css`中加入如下代码：
```css
.swiper-container {
    max-width: 820px;
    margin: 2em auto;

}
.swiper-slide {
    text-align: center;
    font-size: 18px;
    background-color: #fff;
    /* Center slide text vertically */
    display: flex;
    justify-content: center;
    align-items: center;
    img {
        margin: 0 !important;
    }
}
```
{{< /admonition >}}

限制了最大宽度（820 是试出来的值），原因是如果按照文章原本的写法，会出现一个很奇妙的情况：刚载入时一切正常，刷新后图片突然放大，溢出显示区域，但只要稍微拉一下浏览器窗口大小就又恢复正常。

研究半晌实在百思不得其解，不像是 CSS 失效，滑动正常肯定不是 JS 失效，稍微挤压一下中间文章区域就正常了到底是个什么毛病…… 还好搞不清楚是什么情况不要紧，限制一下最大宽度就能解决这件事，傻人有傻福。

**最后在配置文件`config.yaml`中的`params`下加入`enableimgloop: true`**

***

### Twikoo评论回复邮件模板
来自[张洪Heo](https://blog.zhheo.com/p/169a1abb.html)

**废话不多说直接上代码：**
```html
<div class="page flex-col"><div class="box_3 flex-col" style="  display: flex;  position: relative;  width: 100%;  height: 206px;  background: #ef859d2e;  top: 0;  left: 0;  justify-content: center;"><div class="section_1 flex-col" style="  background-image: url(&quot;这里更改为你的网站图标&quot;);  position: absolute;  width: 152px;  height: 152px;  display: flex;  top: 130px;  background-size: cover;"></div></div><div class="box_4 flex-col" style="  margin-top: 92px;  display: flex;  flex-direction: column;  align-items: center;"><div class="text-group_5 flex-col justify-between" style="  display: flex;  flex-direction: column;  align-items: center;  margin: 0 20px;"><span class="text_1" style="  font-size: 26px;  font-family: PingFang-SC-Bold, PingFang-SC;  font-weight: bold;  color: #000000;  line-height: 37px;  text-align: center;">嘿！你在&nbsp;${SITE_NAME}&nbsp;博客中收到一条新回复。</span><span class="text_2" style="  font-size: 16px;  font-family: PingFang-SC-Bold, PingFang-SC;  font-weight: bold;  color: #00000030;  line-height: 22px;  margin-top: 21px;  text-align: center;">你之前的评论&nbsp;在&nbsp;${SITE_NAME} 博客中收到来自&nbsp;${NICK}&nbsp;的回复</span></div><div class="box_2 flex-row" style="  margin: 0 20px;  min-height: 128px;  background: #F7F7F7;  border-radius: 12px;  margin-top: 34px;  display: flex;  flex-direction: column;  align-items: flex-start;  padding: 32px 16px;  width: calc(100% - 40px);"><div class="text-wrapper_4 flex-col justify-between" style="  display: flex;  flex-direction: column;  margin-left: 30px;  margin-bottom: 16px;"><span class="text_3" style="  height: 22px;  font-size: 16px;  font-family: PingFang-SC-Bold, PingFang-SC;  font-weight: bold;  color: #C5343E;  line-height: 22px;">${PARENT_NICK}</span><span class="text_4" style="  margin-top: 6px;  margin-right: 22px;  font-size: 16px;  font-family: PingFangSC-Regular, PingFang SC;  font-weight: 400;  color: #000000;  line-height: 22px;">${PARENT_COMMENT}</span></div><hr style="    display: flex;    position: relative;    border: 1px dashed #ef859d2e;    box-sizing: content-box;    height: 0px;    overflow: visible;    width: 100%;"><div class="text-wrapper_4 flex-col justify-between" style="  display: flex;  flex-direction: column;  margin-left: 30px;"><hr><span class="text_3" style="  height: 22px;  font-size: 16px;  font-family: PingFang-SC-Bold, PingFang-SC;  font-weight: bold;  color: #C5343E;  line-height: 22px;">${NICK}</span><span class="text_4" style="  margin-top: 6px;  margin-right: 22px;  font-size: 16px;  font-family: PingFangSC-Regular, PingFang SC;  font-weight: 400;  color: #000000;  line-height: 22px;">${COMMENT}</span></div><a class="text-wrapper_2 flex-col" style="  min-width: 106px;  height: 38px;  background: #ef859d38;  border-radius: 32px;  display: flex;  align-items: center;  justify-content: center;  text-decoration: none;  margin: auto;  margin-top: 32px;" href="${POST_URL}"><span class="text_5" style="  color: #DB214B;">查看详情</span></a></div><div class="text-group_6 flex-col justify-between" style="  display: flex;  flex-direction: column;  align-items: center;  margin-top: 34px;"><span class="text_6" style="  height: 17px;  font-size: 12px;  font-family: PingFangSC-Regular, PingFang SC;  font-weight: 400;  color: #00000045;  line-height: 17px;">此邮件由评论服务自动发出，直接回复无效。</span><a class="text_7" style="  height: 17px;  font-size: 12px;  font-family: PingFangSC-Regular, PingFang SC;  font-weight: 400;  color: #DB214B;  line-height: 17px;  margin-top: 6px;  text-decoration: none;" href="${SITE_URL}">前往博客</a></div></div></div>
```
将 `这里更改为你的网站图标` 更改为网站图标图片的url地址。

**安装方法：**
进入twikoo管理后台选择邮件通知将代码黏贴到 `MAIL_TEMPLATE` 中

**Twikoo邮件自定义字段：**
Twikoo文档不提供邮件模板的参数含义，这里附上，方便魔改。[@Leonus](https://blog.leonus.cn/) 和 @小香猪 提供。

| 参数                 | 含义         |
| :-----------:       | :-----------: |
| ${SITE_URL}         | 网站链接      |
| ${SITE_NAME}        | 网站名字       |
| ${PARENT_NICK}      | 被回复人昵称      |
| ${PARENT_COMMENT}   | 被回复人的评论内容 |
| ${NICK}             | 回复人昵称        |
| ${COMMENT}          | 回复人评论内容    |
| ${POST_URL}         | 文章链接       |
| ${IMG}              | 回复人头像        |
| ${PARENT_IMG}       | 被回复人头像       |
| ${MAIL}             | 回复人邮件     |
| ${IP}               | 回复人 IP 地址      |

{{< admonition type=tip title="原始代码" open=false >}}
```html
<div class="page flex-col">
  <div class="box_3 flex-col" style="
  display: flex;
  position: relative;
  width: 100%;
  height: 206px;
  background: #ef859d2e;
  top: 0;
  left: 0;
  justify-content: center;
"><div class="section_1 flex-col" style="
  background-image: url(&quot;这里更改为你的网站图标&quot;);
  position: absolute;
  width: 152px;
  height: 152px;
  display: flex;
  top: 130px;
  background-size: cover;
"></div></div>
  <div class="box_4 flex-col" style="
  margin-top: 92px;
  display: flex;
  flex-direction: column;
  align-items: center;
">
    <div class="text-group_5 flex-col justify-between" style="
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0 20px;
">
      <span class="text_1" style="
  font-size: 26px;
  font-family: PingFang-SC-Bold, PingFang-SC;
  font-weight: bold;
  color: #000000;
  line-height: 37px;
  text-align: center;
">嘿！你在&nbsp;${SITE_NAME}&nbsp;博客中收到一条新回复。</span>
      <span class="text_2" style="
  font-size: 16px;
  font-family: PingFang-SC-Bold, PingFang-SC;
  font-weight: bold;
  color: #00000030;
  line-height: 22px;
  margin-top: 21px;
  text-align: center;
">你之前的评论&nbsp;在&nbsp;${SITE_NAME} 博客中收到来自&nbsp;${NICK}&nbsp;的回复</span>
    </div>
    <div class="box_2 flex-row" style="
  margin: 0 20px;
  min-height: 128px;
  background: #F7F7F7;
  border-radius: 12px;
  margin-top: 34px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  padding: 32px 16px;
  width: calc(100% - 40px);
">
      
      <div class="text-wrapper_4 flex-col justify-between" style="
  display: flex;
  flex-direction: column;
  margin-left: 30px;
  margin-bottom: 16px;
">
        <span class="text_3" style="
  height: 22px;
  font-size: 16px;
  font-family: PingFang-SC-Bold, PingFang-SC;
  font-weight: bold;
  color: #C5343E;
  line-height: 22px;
">${PARENT_NICK}</span>
        <span class="text_4" style="
  margin-top: 6px;
  margin-right: 22px;
  font-size: 16px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: #000000;
  line-height: 22px;
">${PARENT_COMMENT}</span>
      </div><hr style="
    display: flex;
    position: relative;
    border: 1px dashed #ef859d2e;
    box-sizing: content-box;
    height: 0px;
    overflow: visible;
    width: 100%;
"><div class="text-wrapper_4 flex-col justify-between" style="
  display: flex;
  flex-direction: column;
  margin-left: 30px;
">
<hr>
        <span class="text_3" style="
  height: 22px;
  font-size: 16px;
  font-family: PingFang-SC-Bold, PingFang-SC;
  font-weight: bold;
  color: #C5343E;
  line-height: 22px;
">${NICK}</span>
        <span class="text_4" style="
  margin-top: 6px;
  margin-right: 22px;
  font-size: 16px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: #000000;
  line-height: 22px;
">${COMMENT}</span>
      </div>
      
      <a class="text-wrapper_2 flex-col" style="
  min-width: 106px;
  height: 38px;
  background: #ef859d38;
  border-radius: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  text-decoration: none;
  margin: auto;
  margin-top: 32px;
" href="${POST_URL}">
        <span class="text_5" style="
  color: #DB214B;
">查看详情</span>
      </a>
    </div>
    <div class="text-group_6 flex-col justify-between" style="
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 34px;
">
      <span class="text_6" style="
  height: 17px;
  font-size: 12px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: #00000045;
  line-height: 17px;
">此邮件由评论服务自动发出，直接回复无效。</span>
      <a class="text_7" style="
  height: 17px;
  font-size: 12px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: #DB214B;
  line-height: 17px;
  margin-top: 6px;
  text-decoration: none;
" href="${SITE_URL}">前往博客</a>
    </div>
  </div>
</div>
```
{{< /admonition >}}

***
### ~~插入音乐测试~~ （已失效）
**先看演示**
{{< music id="139730" type="song" server="netease" >}}

基于 [MetingJS](https://github.com/metowolf/MetingJS) 制作.

**安装方法：**
将以下代码放入 `./layouts/shortcodes/music.html`

```html
{{ if .IsNamedParams }}
    <!-- require APlayer -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css">
    <script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
    <!-- require MetingJS -->
    <script src="https://cdn.jsdelivr.net/npm/meting@2/dist/Meting.min.js"></script>
  
    <meting-js
      id="{{ .Get "id" }}"
      server="{{ .Get "server" }}"
      type="{{ .Get "type" }}"
      fixed="{{ if .Get "fixed" }}{{ .Get "fixed" }}{{ else }}false{{ end }}"
      mini="{{ if .Get "mini" }}{{ .Get "mini" }}{{ else }}false{{ end }}"
      autoplay="{{ if .Get "autoplay" }}{{ .Get "autoplay" }}{{ else }}false{{ end }}"
      loop="{{ if .Get "loop" }}{{ .Get "loop" }}{{ else }}none{{ end }}"
      theme="{{ if .Get "autoplay" }}{{ .Get "autoplay" }}{{ else }}#255579{{ end }}"
      volume="{{ if .Get "volume" }}{{ .Get "volume" }}{{ else }}0.6{{ end }}"
      prelosd="{{ if .Get "prelosd" }}{{ .Get "prelosd" }}{{ else }}auto{{ end }}"
      mutex="{{ if .Get "mutex" }}{{ .Get "mutex" }}{{ else }}true{{ end }}"
      list-folded="{{ if .Get "list-folded" }}{{ .Get "list-folded" }}{{ else }}true{{ end }}">
    </meting-js>
  
{{ end }}
```

**参数表格**

|option	|default	|description
|:-----:|:-----:|-----:|
|id	|require|	song id / playlist id / album id / search keyword
|server|	require|	music platform: netease, tencent, kugou, xiami, baidu
|type	|require|	song, playlist, album, search, artist

**使用示例**

` {< music id="569200212" type="song" server="netease" >}}`  ,具体使用还需要再前面再加一个 `{`

***
### ~~文章中引入豆瓣读书和豆瓣电影~~（失效中）
来自[木木木木木](https://immmmm.com/hugo-shortcodes-douban/)或者[su lv'sblog](https://www.sulvblog.cn/posts/blog/shortcodes/)

**先看效果**
{/{< douban "https://movie.douban.com/subject/35267208/" >}/}
{/{< douban "https://book.douban.com/subject/35496106/" >}/}

**安装方法**

定位到 `layouts/shortcodes目录`，新建一个文件叫 `douban.html`，放入如下代码：

{{< admonition type=tip title="豆瓣书影代码" open=false >}}
```html
<!DOCTYPE HTML>
<html lang="en">
<head>
    <title></title>
    <style>
        .post-preview {
            max-width: 780px;
            height: 200px;
            margin: 1em auto;
            position: relative;
            display: flex;
            /*background: #eee;*/
            background: var(--entry);
            border-radius: 15px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, .25), 0 0 2px rgba(0, 0, 0, .25);
        }

        .dark .post-preview {
            /*background: #383838;*/
            background: var(--entry);
            box-shadow: 0 2px 4px rgba(0, 0, 0, .5), 0 0 2px rgba(0, 0, 0, .5);
        }

        .post-preview--meta {
            width: 80%;
            padding: 23px;
            overflow: hidden;
        }

        .post-preview--middle {
            line-height: 28px;
        }

        .post-preview--title {
            font-size: 22px;
            margin: 0 !important;
        }

        .post-preview--title a {
            text-decoration: none;
        }

        .post-preview--date {
            font-size: 14px;
            color: #999;
        }

        .post-preview--excerpt {
            font-size: 14px;
            line-height: 1.825;
        }

        .post-preview--excerpt p {
            display: inline;
            margin: 0;
        }

        .post-preview--image {
            height: 200px !important;
            width: 25%;
            float: right;
            border-radius: 0 15px 15px 0;
        }

        .post-preview img {
            margin: unset;
            width: 20%;
            border-radius: 0 15px 15px 0;
        }

        @media (max-width: 550px) {
            .post-preview {
                width: 95%;
            }

            .post-preview--excerpt {
                display: none;
            }

            .post-preview--middle {
                line-height: 19px;
            }
        }

        .rating {
            display: block;
            line-height: 15px;
        }

        .rating-star {
            display: inline-block;
            width: 75px;
            height: 15px;
            background-repeat: no-repeat;
            background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEsAAAClCAYAAAAUAAAYAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA5xJREFUeNrs3T9rFEEcxvG7qEQIglaCICKkin9AUEtBKxU7wS61VlYivgWj70TtNFj5BqzE7qxEWwsxKIoYn4UtluFmbm8nczvzm+/BjxyuDwNzu3uXD0+46f7LC5PA45Hm+WTYw1x2LRDc0jzTXB+wqMlsaLPutz8fDFjYZHYauAz3NBvt83XNnyUWNpn1nVm3OsHmsb3EomazzZnVXKMPNcc0xzUnNKc0Rzv/77fms+Z7O3vt9b1eU7bZrNN68l5zcolX4ofmsuZXTdnmMvyi2dR86Bmcac62P6vKrnVubpc0bxYE32nOab45N8YqsvPeDfcD4SOav4HjprPuu+H5BTt9LXDMfNbdLPfT678Fx6vKupt1o/O8+R3pkOaJ5/iktqx7z/qp+aq5q/nY+fczmheaK03Gs7D5rLtZdzSvA6/Ebc2u55j57HQB0TzW7AzkjiKzny6+2hlKNE8juMNcFqKBaIZndRlCNBBNgmx7ZkE0fbLtZkE0EA1EM17WuQwhGogGooFoss6296y52cNO+J6HLJoPaFdbsvA9zGerIxrPh85eWYgGooFoDiQbuAxp0UA0EVmdWbRo+ma1WbRoIBqIZtzsnHdDWjQQDUQD0WSbde5ZS2UhmtqJJtSEiVkXooFoIJre2VATJmZdiKZ2ogk1YSb8oVMvDeUPnSAaiCaPJkzMuhANRAPRQDQpsqEmTMy6EI11oolpwkA0EA1EcyDZmCYMRAPR+LMxTZjqiCamCQPRQDQQzehNGIgGooFoIJpVZ2OaMBBN7USTqgkD0UA0EE3vbKomDERTO9GkasKYJJpUTRiIBqKBaEZvwkA0EA1EA9GkyKZqwkA01olmrCYMRAPRQDR9LkO+0QmiKbAJUyTRjNWEgWggGohm9CYMRAPRQDQQzZDsWE0YiMYC0eTYhIFoIJrKiCbHJgxEY4FocmzCZEs0OTZhIBqIpjKiybEJA9FANBANROPL5tiEgWhKIJoSmzAQDURjjGhKbMJANCUQTYlNmNGIpsQmDEQD0RgjmhKbMBANRAPR1Es0JTZhIJpciMZaEwaigWgKJBprTRiIJheisdaESUo01powEA1EUyDRWGvCQDQQDURjm2isNWEgmlURzWw2q4pZIBqIJkOiCVyGJpkFolkV0ejMMvel28mIRptl7ku3IRqIpjCimfNuaJpZIBqIBqIpm2ice5Z5ZonJupvVkMRu4JW4qXnrOWY++1+AAQBw9BJSCTeN9wAAAABJRU5ErkJggg==);
            overflow: hidden;
        }

        .allstar10 {
            background-position: 0px 0px;
        }

        .allstar9 {
            background-position: 0px -15px;
        }

        .allstar8 {
            background-position: 0px -30px;
        }

        .allstar7 {
            background-position: 0px -45px;
        }

        .allstar6 {
            background-position: 0px -60px;
        }

        .allstar5 {
            background-position: 0px -75px;
        }

        .allstar4 {
            background-position: 0px -90px;
        }

        .allstar3 {
            background-position: 0px -105px;
        }

        .allstar2 {
            background-position: 0px -120px;
        }

        .allstar1 {
            background-position: 0px -135px;
        }

        .allstar0 {
            background-position: 0px -150px;
        }


        .rating-average {
            color: #777;
            display: inline-block;
            font-size: 13px;
            margin-left: 10px;
        }
    </style>
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"
            integrity="sha256-ZosEbRLbNQzLpnKIkEdrPv7lOy9C27hHQ+Xp8a4MxAQ=" crossorigin="anonymous"></script>
    <script type="text/javascript">
        $(document).ready(function () {
            $('.douban_item').each(function () {
                var _this = $(this);
                var strs = _this.attr('urlstring').toString();
                var db_reg = /^https\:\/\/(movie|book)\.douban\.com\/subject\/([0-9]+)\/?/;
                if (db_reg.test(strs)) {
                    var db_type = strs.replace(db_reg, "$1");
                    var db_id = strs.replace(db_reg, "$2").toString();
                    var db_api = "https://douban.edui.fun/";
                    if (db_type === 'movie') {
                        var ls_item = 'movie' + db_id;
                        var url = db_api + "movies/" + db_id;
                        if (localStorage.getItem(ls_item) == null || localStorage.getItem(ls_item) === 'undefined') {
                            $.ajax({
                                url: url, type: 'GET', dataType: "json", success: function (data) {
                                    localStorage.setItem(ls_item, JSON.stringify(data));
                                    movieShow(_this, ls_item, strs)
                                }
                            })
                        } else {
                            movieShow(_this, ls_item, strs)
                        }
                    } else if (db_type === 'book') {
                        var ls_item = 'book' + db_id;
                        var url = db_api + "v2/book/id/" + db_id;
                        if (localStorage.getItem(ls_item) == null || localStorage.getItem(ls_item) === 'undefined') {
                            $.ajax({
                                url: url, type: 'GET', dataType: 'json', success: function (data) {
                                    localStorage.setItem('book' + db_id, JSON.stringify(data));
                                    bookShow(_this, ls_item, strs)
                                }
                            })
                        } else {
                            bookShow(_this, ls_item, strs)
                        }
                    }
                }
            });
        });

        function movieShow(_this, ls_item, str) {
            var storage = localStorage.getItem(ls_item);
            var data = JSON.parse(storage);
            var db_star = Math.ceil(data.rating);
            $("<div class='post-preview'><div class='post-preview--meta'><div class='post-preview--middle'><div class='post-preview--title'><a target='_blank' style='box-shadow: none; font-weight: bolder;' href='" + str + "'>" + data.name + "</a></div><div class='rating'><div class='rating-star allstar" + db_star + "'></div><div class='rating-average'>" + data.rating + "</div></div><time class='post-preview--date'>导演：" + data.director + " / 类型：" + data.genre + " / " + data.year + "</time><section style='max-height:75px;overflow:hidden;' class='post-preview--excerpt'>" + data.intro.replace(/\s*/g, "") + "</section></div></div><img referrerpolicy='no-referrer' loading='lazy' class='post-preview--image' src=" + data.img + "></div>").replaceAll(_this)
        }

        function bookShow(_this, ls_item, str) {
            var storage = localStorage.getItem(ls_item);
            var data = JSON.parse(storage);
            var db_star = Math.ceil(data.rating.average);
            $("<div class='post-preview'><div class='post-preview--meta'><div class='post-preview--middle'><div class='post-preview--title'><a target='_blank' style='box-shadow: none; font-weight: bolder;' href='" + str + "'>" + data.title + "</a></div><div class='rating'><div class='rating-star allstar" + db_star + "'></div><div class='rating-average'>" + data.rating.average + "</div></div><time class='post-preview--date'>作者：" + data.author + " / 出版："+ data.pubdate +" / "+ data.publisher +" </time><section style='max-height:75px;overflow:hidden;' class='post-preview--excerpt'>" + data.summary.replace(/\s*/g, "") + "</section></div></div><img referrerpolicy='no-referrer' loading='lazy' class='post-preview--image' src=" + data.images.medium + "></div>").replaceAll(_this)
        }
    </script>
</head>
<body>
<div class="douban_show">
    <div id="db{{ .Get "src" }}" urlstring="{{ .Get "src" }}" class="douban_item post-preview"></div>
</div>
</body>
</html>
```
{{< /admonition >}}

**使用方法**

`{< douban src="直接放网址如：https://book.douban.com/subject/20394150/" >}}`  ,具体使用还需要再前面再加一个 `{`

### 折叠代码

先看示例

{{< admonition type=note title="note" open=false >}}
An **note** banner
{{< /admonition >}}

{{< admonition type=abstract title="abstract" open=false >}}
An **abstract** banner
{{< /admonition >}}

{{< admonition type=info title="info" open=false >}}
A **info** banner
{{< /admonition >}}

{{< admonition type=tip title="tip" open=false >}}
A **tip** banner
{{< /admonition >}}

{{< admonition type=success title="success" open=false >}}
A **success** banner
{{< /admonition >}}

{{< admonition type=question title="question" open=false >}}
A **question** banner
{{< /admonition >}}

{{< admonition type=warning title="warning" open=false >}}
A **warning** banner
{{< /admonition >}}

{{< admonition type=failure title="failure" open=false >}}
A **failure** banner
{{< /admonition >}}

{{< admonition type=danger title="ndanger" open=false >}}
A **danger** banner
{{< /admonition >}}

{{< admonition type=bug title="bug" open=false >}}
A **bug** banner
{{< /admonition >}}

{{< admonition type=example title="example" open=false >}}
An **example** banner
{{< /admonition >}}

{{< admonition type=quote title="quote" open=false >}}
A **quote** banner
{{< /admonition >}}

### 页脚添加音乐播放器

安装 `APlayer`
我直接在把主题内的 `layouts\partials\footer` 文件夹全部在根目录下了😂。

在 `partials` 文件夹内新建 `music.html` 文件，然后填写：
```html
<!DOCTYPE html>
<html>
<head>
    <!-- require APlayer -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css">
    <script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
    <!-- require MetingJS -->
    <script src="https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js"></script>
</head>
<body>
    <div class="demo">
        <div id="player1">
        </div>
    </div>
    <script>
        var ap = new APlayer
            ({
                element: document.getElementById('player1'),
                fixed: true,
                autoplay: true,
                mini: true,
                theme: '#f8f4fc',
                loop: 'all',
                order: 'random',
                preload: 'auto',
                volume: 0.5,
                mutex: true,
                listFolded: false,
                listMaxHeight: 90,
                lrcType: 0,
                music:[{
                name:'音乐名',url:'音乐链接',artists:'音乐作者',cover:'封面链接'}]
        });
        //ap.init();
    </script>
</body>
```
之后新建 `custom.html` 文件，添加 `{{ partial "music" . }}` 然后保存。

`PaperMod` 中在 `/layouts/baseof.html` 中加入上句代码即可

如果想要修改播放器的各项设置可以在文档中查看各项参数的配置。

打开 `hugo` 本地监听的端口，发现左下角出现播放器！

***
### 测试bilibili
{{< bilibili BV18N4y1c7Mp >}}


### 下划线

{{< underline color="#ffdd00" content="谁在用琵琶弹奏一曲东风破" >}}
<br/>
{{< underline color="#ff2200" content="岁月在墙上剥落看见小时候" >}}


### 画廊测试

| ![美女](https://bu.dusays.com/2022/07/07/62c6f5cb1cbe3.jpg) | ![风景](https://bu.dusays.com/2022/07/31/62e679354b58b.jpg) | ![美女](https://bu.dusays.com/2022/07/07/62c6f5da0e58f.jpg) | ![美女](https://bu.dusays.com/2022/11/24/637f8eb9b09dc.jpg) |
|:-|:-|:-|:-|

### 注释

<ruby> 强化学习<rt>reinforcement learning</rt> </ruby> 有近几十年的研究历史，是 <ruby> 人工智能<rt>artificial intelligence</rt> </ruby> 的一个研究方向

### 脚注

这是一个数字脚注[^1].
这是一个带标签的脚注[^label]

[^1]: 这是一个数字脚注
[^label]: 这是一个带标签的脚注
