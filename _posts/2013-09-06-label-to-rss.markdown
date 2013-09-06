---
layout: post
title:  "简单将标签转为rss的方法"
date:   2013-09-06 19:41:00
categories: jekyll update
---

GR在的时候，会及时的将看到的好的blog添加进去，但是，目前为止还没发现好的rss阅读器，所以，遇到blog我就会添加为标签，随着添加的blog越来越多，导致查阅非常困难，基本上添加过的blog就会被淹没。

一直都有一个需求就是做一个好用的rss阅读器，不过如果想做成大家用的web版还是比较漫长的，所以，当前的想法是这样的：

- 导出chrome中的所有标签到html文件，chrome自带导出功能
- 写一个抓取脚本，从导出文件中的url中找出rss地址，生成一个rss地址列表
- 将rss地址列表转换成OPML格式的文件
- 利用现有的rss阅读器导入OPML文件

上述的想法通过以下方式实现：

- chrome自带导出标签功能
- python脚本在[这里](https://gist.github.com/jseanj/6462631)
- 通过[OPMLBuilder](http://reader.feedshow.com/goodies/opml/OPMLBuilder-create-opml-from-rss-list.php)将rss地址列表转换成OPML格式的文件
- 我用的是[digg reader](http://digg.com/login)

目前效果还不错，只是不自动化，以后会加入以下几点改进：

- 标签以增量形式加入
- rss转换OPML用脚本实现
- 抓取脚本用多线程改写
- 写一个简单好用的rss阅读器
