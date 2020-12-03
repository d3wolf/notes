# meta标签分两大部分：HTTP-EQUIV和NAME变量。

## ●HTTP-EQUIV类似于HTTP的头部协议

它回应给浏览器一些有用的信息，以帮助正确和精确地显示网页内容。常用的HTTP-EQUIV类型有：

### 1.expires(期限)
```text
说明：可以用于设定网页的到期时间。一旦网页过期，必须到服务器上重新调阅。
用法：<META HTTP-EQUIV="expires" CONTENT="Wed, 26 Feb 1997 08:21:57 GMT">
注意：必须使用GMT的时间格式。
```

### 2.Pragma(cach模式)
```
说明：禁止浏览器从本地机的缓存中调阅页面内容。
用法：<META HTTP-EQUIV="Pragma" CONTENT="no-cache">
注意：这样设定，访问者将无法脱机浏览。
```

### 3.Refresh(刷新)
```
说明：需要定时让网页自动链接到其它网页的话，就用这句了。
用法：<META HTTP-EQUIV="Refresh" CONTENT="5;URL= http://www.yahoo.com">
注意：其中的5是指停留5秒钟后自动刷新到URL网址。
```

### 4.Set-Cookie(cookie设定)
```
说明：如果网页过期，那么存盘的cookie将被删除。
用法：<META HTTP-EQUIV="Set-Cookie" CONTENT="cookievalue=xxx;expires=Wednesday, 21-Oct-98 16:14:21 GMT; path=/">
注意：必须使用GMT的时间格式。
```

### 5.Window-target(显示窗口的设定)
```
说明：强制页面在当前窗口以独立页面显示。
用法：<META HTTP-EQUIV="Window-target" CONTENT="_top">
注意：用来防止别人在框架里调用你的页面。
```

### 6.Content-Type(显示字符集的设定)
```
说明：设定页面使用的字符集。(我们在前面字体设计中已经介绍过它的作用)
用法：<meta http-equiv="Content-Type" content="text/html; charset=gb2312">
```

## ●meta标签的NAME变量

语法格式是：
`<META NAME="xxx" CONTENT="xxxxxxxxxxxxxxxxxx">`
其中xxx主要有下面几种参数：

### 1.Keywords(关键字)
```
说明：keywords用来告诉搜索引擎你网页的关键字是什么。
举例：<META NAME ="keywords" CONTENT="life, universe, mankind, plants,
relationships, the meaning of life, science">
```

### 2.description(简介)
```
说明：description用来告诉搜索引擎你的网站主要内容。
举例：<META NAME="description" CONTENT="This page is about the meaning oflife, the universe, mankind and plants.">
```

### 3.robots(机器人向导)
```
说明：robots用来告诉搜索机器人哪些页面需要索引，哪些页面不需要索引。
CONTENT的参数有all,none,index,noindex,follow,nofollow。默认是all。
举例：<META NAME="robots" CONTENT="none">
```

### 4.author(作者)
```
说明：标注网页的作者
举例：<META name="AUTHOR" content="ajie,ajie@netease.com">
```

以上是meta标签的一些基本用法，其中最重要的就是:Keywords和description的设定。

为什么呢？道理很简单，这两个语句可以让搜索引擎能准确的发现你，吸引更多的人访问你的站点!

根据现在流行搜索引擎(Lycos，AltaVista等)的工作原理,搜索引擎首先派出机器人自动在www上搜索，当发现新的网站时，便检索页面中的keywords和decription，并将其加入到自己的数据库，然后再根据关键词的密度将网站排序。

也就是说：
1. 如果你的页面中根本没有Keywords和description的meta标签，那么机器人是无法将你的站点加入数据库，网友也就不可能搜索到你的站点。

2. 如果你的关键字选的不好，关键字的密度不高，被排列在几十甚至几百万个站点的后面被点击的可能性也是非常小的。 
