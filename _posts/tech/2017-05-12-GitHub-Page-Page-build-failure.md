---
layout: post
title: Page build failure with no details from Github page
category: 技术
tags: GitHub Page,Jekyll,Page build failue
description: #在Github page上传markdown格式的blog文件，无法正常显示页面
---

## 问题描述
在Github Page 上搭建的博客，上传markdown格式的[blog](https://raygift.github.io/2017/05/10/UWP-ComboBox-SeletedItem.html)文件后，发现博客列表中找不到该文章，且收到一封GitHub发来的Email，内容如下：
````
Page build failure

The page build failed for the `master` branch with the following error:

Page build failed. For more information, see https://help.github.com/articles/troubleshooting-github-pages-builds/.

For information on troubleshooting Jekyll see:

  https://help.github.com/articles/troubleshooting-jekyll-builds

If you have any questions you can contact us by replying to this email.

````

## 初步解决
进入邮件中的链接试图定位问题，由于邮件中没有说明具体问题，因此排查了一遍[Generic Jekyll build failures ](https://help.github.com/articles/generic-jekyll-build-failures/)，没有发现异常

通过搜索发现早年很多人出现过此类问题，并给出了一些可能的原因和解决办法，参考后尝试再本地添加一篇新的blog，在博文目录下启动jekyll，然后在localhost下可以正常阅读，继而试图使用“jekyll --safe”查看错误信息，发现并不可行，提示需使用 "jekyll <subcommand> [options]"的命令格式。
最终将指令调整为 “jekyll serve --safe” ，终于发现了错误信息，提示 “/_includes/head.html” 中的 “Unknown tag ‘seo’” 和 “ Unknown tag ‘feed_meta’” 无效，将这两个tag删除后重新提交，blog终于可以正常显示。

## 更新
虽然删除seo后可以正常提交，但检查_config.yml的gems下确实包含了 "jekyll-seo-tag" 插件，百思不得其解，最后在[stackoverflow](http://stackoverflow.com/questions/35822896/liquid-syntax-error-for-gist-tag-with-github-pages-gem-for-jekyll)上弄明白了，使用"--safe"会使得插件无效...

重新使用“jekyll serve”命令查看输出，错误信息变成了"Liquid Exception: no implicit conversion of nil into String in /_layout/default.html"

继续使用"jekyll serve --trace"跟踪，发现类型转换错误：“C:/Ruby23-x64/lib/ruby/gems/2.3.0/gems/jekyll-seo-tag-2.2.2/lib/jekyll-seo-tag/drop.rb:45:in `+': no implicit conversion of nil into String (TypeError)”，定位到drop.rb文件，发现45行代码为：
```` 
            site_title + TITLE_SEPARATOR + format_string(site["description"])
````
有一个类型将description转换为string的操作，说明问题可能是由于description为空而导致的，果然在找到最近一次提交成功的blog中，为了seo而设置的description并没有填写任何内容，尝试将内容填写完整，并重新恢复head.html中的“seo”绑定。

## 再次更新
将blog中description补充后，仍然无法正常提交，在drop.rb中尝试使用"p site_title"输出得到的竟然是 _config_yml中site settings中的title，继而发现其中的description后有一个">"，不知什么时候误输入的，删除后再次提交，终于正常

为什么 “>”会报错呢？
在drop.rb中进行测试，发现format_string("<")可以得到"&1t;" 而 format_string(">") 却会得到nil， format_string("/>")得到" / & g t ; "

## 总结
“<” “>”两个符号是常见的特殊符号，例如在xml中就必须使用其转义字符来避免与xml标签的混淆，因此猜测ruby也借鉴了此种方式，将“<”“>”视为特殊符号，导致format_string()无法正确的将其转换为string。
另外_config.yml是YAML格式,YAML格式参考了xml，同样会将“<” “>”视为特殊符号。