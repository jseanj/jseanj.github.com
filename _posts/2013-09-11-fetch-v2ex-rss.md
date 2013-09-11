---
layout: post
title:  "利用v2ex个人博客作为rss源"
date:   2013-09-11 15:09:27
categories: jekyll update
---

v2ex是一个高质量的社区，里面有很多技术大牛，所以，打算把里面成员的个人博客地址取出来作为rss源。

直接用`curl`命令去取成员信息列表，返回的是json串，需要在shell中进行json解析，这里用的是[JSON.sh](https://github.com/dominictarr/JSON.sh)。

{% highlight bash %}
#!/bin/bash
#此脚本取前10000个成员的website，并存入urls.txt。
for i in {1..10000}
do
  url=`curl http://www.v2ex.com/api/members/show.json?id=$i \
		| ./JSON.sh | grep '\["website"\]' | cut -f2`
  if [ x$url != "x\"\"" -a x$url != "x\"None\"" ]; then
    url=${url#\"}
    url=${url%\"}
    if [ ${url:0:4} != "http" ]; then
      url="http://"$url
    fi
    echo $url >> urls.txt
  fi
done
{% endhighlight %}

由于获得的url有很多非个人独立域名，而是weibo或twitter的地址，所以需要过滤一遍。
{% highlight bash %}
sed -i '/weibo/d;/twitter/d;/google/d;/douban/d;/renren/d;/facebook/d' urls.txt
{% endhighlight %}

去掉空行。
{% highlight bash %}
sed -i '/^$/d' urls.txt
{% endhighlight %}

过滤掉非正规url地址，比如http://xxx。
{% highlight bash %}
cat urls.txt | awk -F. '{print $NF}' | grep http | awk -F// '{print $2}' \
			 | sed 's|.*|/&/d|' | sed -f - -i urls.txt
{% endhighlight %}

去掉Unicode编码
{% highlight bash %}
sed -i 's/\\u002D/-/g' urls.txt
sed -i '/\\u/d' urls.txt
{% endhighlight %}

这时得到的url地址趋向于正规，但也可能有很多不能访问，不要紧，在我们获取rss时会过滤掉这部分url。获取rss的python脚本用多线程改写了一版，可以在[这里](https://gist.github.com/6520420)访问，之后添加rss源的方法可以看[前一篇文章](http://codefouce.com/jekyll/update/2013/09/06/label-to-rss.html)

*PS*：v2ex目前注册用户数是45215（可以在[首页](www.v2ex.com)看到），而添加个人博客地址的用户有3500人左右。通过域名统计，发现.me域名有506个，排第二位。这让我越来越喜欢这个社区了。
