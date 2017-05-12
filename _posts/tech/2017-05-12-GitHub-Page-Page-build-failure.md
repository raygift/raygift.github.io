---
layout: post
title: "Page build failure" Email with no details from Github page
category: 技术
tags: GitHub Page,Jekyll,Page build failue
description: 在Github page上传markdown格式的blog文件，无法正常显示页面
---


在Github Page 上搭建的博客，上传markdown格式的[blog](https://raygift.github.io/2017/05/10/UWP-ComboBox-SeletedItem.html)文件后，发现博客列表中找不到该文章，且收到一封GitHub发来的Email，内容如下：
````
Page build failure

The page build failed for the `master` branch with the following error:

Page build failed. For more information, see https://help.github.com/articles/troubleshooting-github-pages-builds/.

For information on troubleshooting Jekyll see:

  https://help.github.com/articles/troubleshooting-jekyll-builds

If you have any questions you can contact us by replying to this email.

````
进入邮件中的链接试图定位问题，由于邮件中没有说明具体问题，因此排查了一遍[Generic Jekyll build failures ](https://help.github.com/articles/generic-jekyll-build-failures/)，没有发现异常

通过搜索发现早年很多人出现过此类问题，并给出了一些可能的原因和解决办法，参考后尝试再本地添加一篇新的blog，在博文目录下启动jekyll，然后在localhost://4000下可以正常阅读，继而试图使用“jekyll --safe”查看错误信息，发现并不可行，提示需使用 jekyll <subcommand> [options]的命令格式。
最终将指令调整为 “jekyll serve --safe” ，终于发现了错误信息，提示 “/_includes/head.html” 中的 “Unknown tag ‘seo’” 和 “ Unknown tag ‘feed_meta’” 无效，将这两个tag删除后重新提交，blog终于可以正常显示。