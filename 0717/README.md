# 2019.07.17记录

[TOC]

## Upload-labs实验2

### 1. getimagesize()、exif_imagetype()绕过
	选取一张图片，后用记事本打开，在结尾处加入PHP代码，保存后上传。

### 2. 二次渲染绕过：[参考](https://xz.aliyun.com/t/2657)
	首先上传结尾带有php代码的图片文件，然后上传，再将上传的下载，可以发现,我们在图片末端添加的php代码已经被去除。关于绕过gif的二次渲染,我们只需要找到渲染前后没有变化的位置,然后将php代码写进去,就可以成功上传带有php代码的图片了。

### 3. 条件竞争：（多线程并发问题）
	适用于文件上传成功后对文件进行黑名单文件类型过滤删除。
	
	写脚本文件或使用burp进行并发上传并访问。

### 4. 目录穿越：[参考](https://blog.csdn.net/jishuzhain/article/details/84791532)
漏洞利用
```
|--var
    |--www
    |--flag.txt
        |--html
            |--index.php
            |--test.txt
通过访问http://127.0.0.1/test/test.txt
可以成功访问到test的内容如下(/test/test.txt路由请求，经处理后转换成：/var/www/html/test.txt)
    this is a test
修改请求为http://127.0.0.1/test…/flag.txt，可以成功跳到上一层目录下，读取到flag.txt的内容(/test…/flag.txt路由请求，经处理后转换成：/var/www/flag.txt)
    you get me, hahaha
```

### 5. .htaccess：

#### .htaccess的基本作用
	URL重写、自定义错误页面
	MIME类型配置
	访问权限控制等
	主要体现在伪静态的应用
	图片防盗链
	自定义404错误页面
	阻止/允许特定IP/IP段
	目录浏览与主页
	禁止访问指定文件类型
	文件密码保护

#### .htaccess的规则
	RewriteEngine on
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^article-(.*)$ index.php/Home/Article/detail/id/$1 [L,NC]
	RewriteRule ^index-(.*)-(.*).html$ index.php/Home/Article/index/pid/$1/cid/$2 [L,NC]
	RewriteRule ^index-(.*).html$ index.php/Home/Article/index/cid/$1 [L,NC]
	RewriteRule ^page-(.*).html$ index.php/Home/Article/page/cid/$1 [L,NC]
	RewriteRule ^Article/(.*)$ index.php/Article/$1 [L,NC]
	RewriteRule ^Admin$ index.php?m=Admin&c=Public&a=index [L,NC]

Rewrite标志
```
	R[=code](force redirect) 强制外部重定向
	G(force URL to be gone) 强制URL为GONE，返回410HTTP状态码。
	P(force proxy) 强制使用代理转发。
	L(last rule) 表明当前规则是最后一条规则，停止分析以后规则的重写。
	N(next round) 重新从第一条规则开始运行重写过程。
	C(chained with next rule) 与下一条规则关联
	如果规则匹配则正常处理，该标志无效，如果不匹配，那么下面所有关联的规则都跳过
	T=MIME-type(force MIME type) 强制MIME类型
	NS (used only if no internal sub-request) 只用于不是内部子请求
	NC(no case) 不区分大小写
	QSA(query string append) 追加请求字符串
	NE(no URI escaping of output) 不在输出转义特殊字符
	例如：
	RewriteRule /foo/(.*) /bar?arg=P1%3d$1 [R,NE] 将能正确的将/foo/zoo转换成/bar?arg=P1=zed
	PT(pass through to next handler) 传递给下一个处理
	例如：
	RewriteRule ^/abc(.*) /def$1 [PT] # 将会交给/def规则处理
	Alias /def /ghi
	S=num(skip next rule(s)) 跳过num条规则
	E=VAR:VAL(set environment variable) 设置环境变量
```

RewriteCond标志符
```
	'nocase|NC'(no case)忽略大小
	'ornext|OR' (or next condition)逻辑或，可以同时匹配多个RewriteCond条件RewriteRule适用的标志符
	'redirect|R [=code]' (force redirect)强迫重写为基于http开头的外部转向(注意URL的变化) 如：[R=301,L]
	'forbidden|F' (force URL to be forbidden)重写为禁止访问
	'proxy|P' (force proxy)重写为通过代理访问的http路径
	'last|L' (last rule)最后的重写规则标志，如果匹配，不再执行以后的规则
	'next|N' (next round)循环同一个规则，直到不能满足匹配
	'chain|C' (chained with next rule)如果匹配该规则，则继续下面的有Chain标志的规则。
	'type|T=MIME-type' (force MIME type)指定MIME类型
	'nosubreq|NS' (used only if no internal sub-request)如果是内部子请求则跳过
	'nocase|NC' (no case)忽略大小
	'qsappend|QSA' (query string append)附加查询字符串
	'noescape|NE' (no URI escaping of output)禁止URL中的字符自动转义成%[0-9]+的形式。
	'passthrough|PT' (pass through to next handler)将重写结果运用于mod_alias
	'skip|S=num' (skip next rule(s))跳过下面几个规则
	'env|E=VAR:VAL' (set environment variable)添加环境变量
```

Rewrite规则表达式的说明
```
	. 匹配任何单字符
    [chars] 匹配字符串:chars
    [^chars] 不匹配字符串:chars
    text1|text2 可选择的字符串:text1或text2
    ? 匹配0到1个字符
    * 匹配0到多个字符
    + 匹配1到多个字符
    ^ 字符串开始标志
   	$ 字符串结束标志
    n 转义符标志
    反向引用 $N 用于 RewriteRule 中匹配的变量调用(0 <= N <= 9)
    反向引用 %N 用于 RewriteCond 中最后一个匹配的变量调用(1 <= N <= 9)
```