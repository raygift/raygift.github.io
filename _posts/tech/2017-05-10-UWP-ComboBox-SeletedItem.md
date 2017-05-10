---
layout: post
title: UWP ComboBox 取值问题
category: tech
tags: UWP,ComboBox,SeletedItem
description: uwp combox
---



之前通过Jekyll与Github搭建了自己的博客，并试着发了几篇，需要去markdown文件中手动修改一些格式和文字，感觉通过直接本地修改markdown文件的书写方式有些繁琐，正巧之前对UWP感兴趣，因此有了实现一个win10上运行的生成Jekyll博客markdown文件的UWP应用的想法，目前简单的摆了一些基本控件，并实现了保存markdown文件的功能，地址是：https://github.com/raygift/MyBlog_UWP

然而在这过程中发现了一个ComboBox取值的问题，UWP开发中使用ComboBox实现下拉选择的功能，但在cs文件中却无法正确获取被选中项的值
xaml中ComboBox代码如下：
````xaml
<ComboBox Name="typeSelectBox" Width="Auto" VerticalAlignment="Center" HorizontalAlignment="Center">
          <ComboBoxItem Content="tech" IsSelected="True"></ComboBoxItem>
          <ComboBoxItem Content="life"></ComboBoxItem>
          <ComboBoxItem Content="tool"></ComboBoxItem>
</ComboBox>
````

对应cs中，本来按照直觉想使用 typeSelectBox.SelectedItem 获取到选中项的内容，结果得到的为 “Windows.UI.Xaml.Controls.ComboBoxItem”，
````C#
var type=typeSelectBox.SelectedItem.ToString();
//此时 type值为“Windows.UI.Xaml.Controls.ComboBoxItem”
````
是一个ComboBoxItem对象，该对象拥有一个Content属性，其值为期望得到的"tech"

尝试使用 typeSelectBox.SelectedItem.Content，出现错误，错误提示为：“object”未包含"Content"的定义，并且找不到可接受第一个“Object”类型参数的扩展方法“Content”（是否缺少using指令或程序集引用？）

此处百思不得其解，搜索MSDN有关ComboBox的内容没有发现合理解释，继续查找网上有关ComboBox使用方法，发现在WinForm中有人遇到类似问题，最终使用((DropDownStyle)theCombobox.SelectedItem).Id["*"]可正常获取

在此参考下，使用 ((ComboBoxItem)typeSelectBox.SelectedItem).Content ，正确的得到了期望结果
````C#
var type = ((ComboBoxItem)typeSelectBox.SelectedItem).Content.ToString();
//此时得到的type值为“tech”
````

为什么同样是 Windows.UI.Xaml.Controls.ComboBoxItem 类型的object，却无法同样的使用此对象下的属性 Content？


