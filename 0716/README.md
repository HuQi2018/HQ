# 2019.07.16记录

[TOC]

## Upload-labs实验1

### 1. js检查：
	这种直接禁用JS，或者burp改包等等都可以。

### 2. 只验证Content-type（mime-type绕过）：
	用burp改包即可以。

### 3. 黑名单绕过：
	首先上传“.htaccess”文件，内容为：“SetHandler application/x-httpd-php”，即可将所有文件当php解析。
	然后再上传任何其他类型扩展的文件即可访问。
	
### 4. 文件名大小写绕过：（win可以，linux不行）：
	即将文件后缀命名为如：“.Php”、“.phP”等

### 5. 空格绕过、点绕过、::$DATA绕过、.空格.绕过（仅限win）：
	“.php ”、“.php.”、“.php::$DATA”、“.php/. .”

### 6. 双写绕过：
	“.pphphp”

### 7. 截断绕过：
	“.php%00”