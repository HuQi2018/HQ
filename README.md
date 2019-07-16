# HQ  2019网络工作室暑期留校计划

[TOC]

## 要求

### 学习

1. 按时完成每天的学习任务
2. 完成每天的学习笔记，使用git推到github（笔记内容不限，可以是学习记录、心得、奇技淫巧，内容可以少，但是要有价值）
3. 互相交流，有不会的技术点多google，多请教别人
4. 多查看别人github的学习笔记
5. 有自己学习进度的可以根据自己的进度来，不强制要求根据计划进度
6. 996学习制度，早9晚9，周日休息

## 计划

### 7.9

#### 所有选手

1. 注册github，学习git的基本使用(init,add,checkout,commit,push,pull,clone,status)

2. 建立git仓库，名称为姓名首字母，建立完成后将地址填写到[共享文档](https://docs.qq.com/sheet/DYnVsWERNYlFwdXly?ADUIN=29195508&ADSESSION=1562470410&ADTAG=CLIENT.QQ.5603_.0&ADPUBNO=26882&preview_token=&coord=A1%24A1%240%240%240%240&tab=BB08J2)

   **目录结构**：

   ```
   ├─wf                      ---仓库名称（姓名首字母）
   │  ├─0708                 ---日期
   │  │  │  README.md        ---笔记
   │  │  │
   │  │  ├─code              ---长段代码（短的可以直接写到markdown里）
   │  │  └─img               ---图片资源
   │  ├─0709
   │  │  │  README.md
   │  │  │
   │  │  ├─code
   │  │  └─img
   │  └─0710
   │      │  README.md
   │      │
   │      ├─code
   │      └─img
   ```

3. 学习markdown基本语法，用markdown做笔记

#### web选手

#### bin选手



### 7.10

#### 所有选手

1. 学习linux基本操作（推荐ubuntu）
2. 会根据网上教程部署各种服务，会修改配置文件
3. 会安装一些提升效率的插件（eg:zsh,fish,oh-my-zsh,oh-my-fish,i3,htop...）
4. 了解路由表的工作原理，学会windows及linux路由表的多网卡、内外网简单配置

#### web选手

1. 用linux部署lamp（linux+apache+mysql+php）或lnmp（linux+nginx+mysql+php）环境

#### bin选手



### 7.11

#### 所有选手

1. 用linux部署docker
2. 会管理docker镜像、容器
3. 会docker端口映射、目录挂载（有空余时间的可以学习一下dockerfile的编写）

#### web选手

1. 用docker部署lamp（linux+apache+mysql+php）或lnmp（linux+nginx+mysql+php）环境
2. 用docker部署sqli-lab、dvwa、upload-labs

#### bin选手



### 7.12

#### 所有选手

#### web选手

**借助sqli-lab熟练sql注入姿势**

1. 联合查询 union
2. 报错注入，floor、extractvalue、updatexml、geometrycollection、multipoint、polygon、multipolygon、linestring、multilinestring、exp

#### bin选手



### 7.13

#### 所有选手

#### web选手

**盲注**（学会写脚本）

1. 布尔盲注, = != <> > < ~ ^  like regexp rlike pow cot exp substr substring ascii left mid char
2. 时间盲注 sleep benchmark 各种大运算造成的延时

#### bin选手



### 7.14

休息



### 7.15

#### web选手

学会SQL注入防御的绕过

1. 大小写绕过
2. 双写绕过
3. 注释绕过
4. 编码绕过
5. 宽字节绕过
6. 等价函数绕过
7. 特殊符号绕过
8. 其他...



### 7.16

#### web选手

练习upload-labs靶场（部分知识点可能靶机里没有，自行搜索学习）

1. 理解前端校验的危害，懂得绕过
2. 文件名大小写绕过
3. 黑名单机制的情况下，其他扩展名绕过（php5,php7,phtml,phar...）
4. mime-type绕过
5. <?php 关键字绕过



### 7.17

#### web选手

练习upload-labs靶场（部分知识点可能靶机里没有，自行搜索学习）

1. 图片二次渲染绕过
2. getimagesize绕过
3. 目录穿越
4. 文件头绕过（gif89a）
5.  .htaccess的作用和技巧（搭配伪协议）
6. 上传条件竞争
7. 其他...



### 7.18

#### web选手

1. 理解xss形成的原理和危害
2. 反射型xss
3. 存储型xss
4. dom型xss



### 7.19

#### web选手

1. xss各种绕过姿势
2. http-only
3. 同源策略
4. CSP策略



### 7.20

#### web选手

**php调试环境的搭建**

​	**推荐方案：（个人总结）**（可以实现在windows物理机编写和调试代码，兼顾了windows的体验和linux的运行环境）

```
VMware里装ubuntu
创建 物理机-虚拟机 共享文件夹挂载到ubuntu
ubuntu安装docker
docker部署lamp（php5 和 php7的容器各一个，绑定不同端口比如8005 8007）并将 挂载的共享文件夹 挂载到容器的网站根目录
安装xdebug插件
配置phpstorm或者vscode（看个人喜好，phpstorm专业一点、vscode轻量一点）配置的时候路径那有坑点，容易不显示当前执行代码行
```



### 7.21

休息



### 7.22

#### web选手

**用环境调试并学习函数漏洞**

1. extract
2. in_array 
3. intval
4. ereg
5. strcmp
6. md5 sha1
7. strpos
8. unlink
9. 其他...


