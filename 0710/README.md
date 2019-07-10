# 2019.07.10记录

## 安装Ubuntu插件

### 安装zsh插件

1. 安装zsh：sudo apt-get install -y zsh
2. 启动zsh：zsh
3. 安装主题：
	测试特殊字体能否使用：
	echo "\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699"
	（若没有就安装字体库：sudo apt-get install fonts-powerline）

### 安装oh-my-zsh插件
1. 安装oh-my-zsh对其进行美化：sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
2. 设置默认终端shell：chsh -s `which zsh` | chsh -s $(which zsh)
3. 设置zsh参数：ls ~/.oh-my-zsh/
	（plugins 文件夹 为 zsh 的插件， oh my zsh 为用户提供了很多的插件， 自己也可以下载插件，插件的用法下面会提到。themes  文件夹为zsh 的主题，笔者自用为 "ys"）

### 安装htop插件：sudo apt-get install htop

### 安装分屏软件：sudo apt-get install terminator
	操作			效果
	ctrl+shift+E	左右分屏
	ctrl+shift+O	上下分屏
	alt+上下左右方向键	切换当前窗口
	ctrl+shift+W	关闭当前窗口
	ctrl+shift+X	将当前窗口 最大化/恢复

### 安装fish插件：curl -L https://get.oh-my.fish | fish

### 配置omf：
1. 显示当前使用的主体和插件：omf list
2. 列出所有主题和插件：omf theme
3. 安装主题和插件：omf install clearance
4. 搜索主题和插件：omf search
5. 更新omf：omf update omf
6. 对仓库的操作：omf repositories [list|add|remove]
7. 显示帮助：omf -h  |  omf --help
8. 卸载omf：omf destory

### 安装i3：sudo apt-get install -y i3

## LNMP环境搭建

### Nginx搭建：
1. 搭建
	sudo apt-get update
	sudo apt-get install nginx
	/usr/sbin/nginx -v
	sudo service nginx start
	（Nginx的默认网站目录是 /usr/share/nginx/html/，默认Nginx网站配置文件为 /etc/nginx/sites-available/default 。）

2. 配置Nginx：
	vim /etc/nginx/sites-available/default
	添加index.php页面解析：
	index index.php; 
	location ~ \.php$ { 
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

3. 重启Nginx：nginx -s reload

### mysql搭建：
1. 安装mysql：sudo apt install mysql-server mysql-client
2. 更新root密码：
3. update mysql.user set authentication_string=PASSWORD('新密码'), plugin='mysql_native_password' where user='root';
	刷新设置：
	flush privileges;
4. 开启root远程访问：GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
（若远程还不能访问考虑防火墙是否开放，或在配置文件/etc/mysql/my.cnf中bind_address值改成0.0.0.0）
5. 登录mysql：mysql -uroot -p（输密码）
	或root使用管理员登录：mysql

### php搭建：
1. 安装php扩展：
	sudo apt-add-repository ppa:ondrej/php
	sudo apt-get update
	sudo apt-get install php7.0 php7.0-fpm
2. 配置php：
	sudo vim /etc/php/7.0/fpm/php.ini
	按/输入cgi.fix_pathinfo寻找该值 
	解除注释并把值设置为0
	
	然后再修改 PHP-FPM的配置文件 /etc/php/7.0/fpm/pool.d 
	与 Nginx监听同一个 sock
	listen = /run/php/php7.0-fpm.sock
3. 重启php解析：
	sudo service nginx restart
	sudo service php7.0-fpm restart
	
### php网页测试：
创建文件index.php：（测试查看是否能使用）
```
<?php
phpinfo();
?>
```

## 路由

### Windows路由查看：route PRINT

### linux路由查看：route -n

### 路由ip添加：route add -p 网络地址 mask 子网掩码 网关地址
	即可添加一条路由，自己添加的路由在永久路由里（也称静态路由）

### 路由ip删除：rotue delete 网络地址




