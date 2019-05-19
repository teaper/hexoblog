---
title: 阿里云ubuntu 16.04.5 server搭建Gitea平台
date: 2019-05-19 22:40:17
categories: [Linux]
tags: [Ubuntu16.04,MySQL8.0,Gitea,Nginx,Docker,SSL]
---
#### 【一】预先准备  
本地系统:Windows Linux  
阿里云购买一个轻量服务器,地域随便,镜像选择系统镜像 -> Ubuntu16.04 <span style="color:#ff0000;">(示例ip:39.108.51.25)</span>  
阿里云购买一个域名 <span style="color:#ff0000;">(示例域名:twtity.com)</span>  
  
安装包:  
[jdk-8u172-linux-x64.tar.gz](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  
[apache-tomcat-9.0.11.tar.gz](https://tomcat.apache.org/download-90.cgi)  
[mysql-apt-config_0.8.10-1_all.deb](https://dev.mysql.com/downloads/repo/apt/)  
[gitea-1.5.0-linux-amd64](https://dl.gitea.io/gitea/)  
  
#### 【二】服务器系统配置
登录阿里云控制台-->轻量应用服务器-->服务器运维-->远程连接  
单击"设置密码"按钮设置一个root密码,确定后重启  
单击"设置秘钥" -> 创建秘钥,输入秘钥名称<span style="color:#ff0000;">(示例:twtity)</span>,单击确定按钮,确定后将会下载秘钥文件  
回到客户端选项卡,单击"《[Putty配置说明](https://help.aliyun.com/document_detail/59083.html?spm=5176.10173289.107.1.6c5d2e77cHI0NI#windows)》",按照说明操作,由于我本机是Ubuntu16.04的系统,并且支持SSH命令,所以接下来的命令是以linux系统为标准的  
找到下载的私钥文件<span style="color:#ff0000;">(示例:twtity.pem)</span>  
  
```bash
sudo chmod 400 twtity.pem  #给私钥加权限400
ssh root@39.108.51.25 -i twtity.pem  #输入root密码登录,输入exit回车退出
```
回到阿里云客户端选项卡,单击远程连接按钮,这时已经自动使用admin用户登录了服务器系统,此时命令行开头为 `admin@iZwz9emoxkbmiopxeez3sfZ:`,其中 `iZwz9emoxkbmiopxeez3sfZ` 为主机名  
```bash
sudo   #测试sudo,由于我是用SSH的root用户登录的,后面有些命令就省略了sudo
```
若出现以下错误信息  
```bash
sudo: unable to resolve host iZwz9emoxkbmiopxeez3sfZ
```
则修改修改hosts文件  
```bash
sudo vim /etc/hosts  #使用vim修改hosts文件,输入i进入install模式
--------------------添加---------------------
127.0.0.1       iZwz9emoxkbmiopxeez3sfZ
---------------------------------------------
```
按Esc键退出INSERT模式,输入:wq,按回车保存退出(提示冒号q不保存退出)  
再次输入sudo测试一下是否正常  
  
##### 允许远程SSH登录  
```bash
sudo vim /etc/ssh/sshd_config  #打开ssh_config文件,提示:服务器重启后可能会变回no
---------------------------修改---------------------------
PasswordAuthentication no  改为 PasswordAuthentication yes
----------------------------------------------------------
sudo service sshd restart  #重启SSH服务
```
  
##### 配置源  
<span style="color:#ff0000;">提示:粘贴之后特别注意前两行是否被修改,有的话重新粘贴前两行</span>  
```bash
-------------------------------粘贴源---------------------------------
deb http://mirrors.aliyun.com/ubuntu/ xenial main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main
 
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main
 
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
 
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security universe
-----------------------------------------------------------------------
sudo apt-get update
sudo apt-get upgrade   #更新时可能出现一个窗口,选择第一个install the package's version,回车
```
关闭浏览器阿里云远程连接窗口,之后我们就用本机的终端进行SSH登录  
```bash
ssh root@39.108.51.25   #SSh登录
```
我们会发现每次登录都要输入IP地址,太麻烦记不住怎么办,这时候我们可以到阿里云控制台,单击"添加域名"按钮,设置一个已注册的域名<span style="color:#ff0000;">(示例域名:twtity.com)</span>  
```bash
ssh root@twtity.com  #登录
```
  
##### 文件上传  
在本机电脑新建一个文件夹(例如:installpackage),将预先准备的安装包放进installpackage文件夹中  
```bash
scp -r installpackage root@twtity.com:/installpackage  #上传目录
------------------------------scp相关补充-----------------------
scp fileName root@IP:fileName  #上传文件
scp -r /opt/folderName root@IP:/opt/folderName  #上传目录
scp root@IP:fileName /dewnloads/fileName   #下载文件到指定目录
---------------------------------------------------------------
 
ssh root@twtity.com  #登录进去看下
cd /installpackage
ls -a
```
  
#### 【三】软件安装  
```bash
apt-get install git   #安装git
 
apt install screen    #安装screen
-----------------补充------------
screen -S name  #新建窗口
screen -ls   #窗口列表
screen -r name  #进入目标窗口
exit #退出并删除窗口
Ctrl+A+D  #退出,但保留窗口继续运行
--------------------------------
```
  
##### 安装MySQL  
我之前在博客"Ubuntu16.04搭建Java开发环境"安装本机Mysql都没有用到安装包,为什么这里又要了呐?  
之前是本机ubuntu的桌面版本,mysql版本为5.7,如果我们使用5.7版本安装方法安装,则会出现mysql不提示设置root密码的情况,那么就无法登录Mysql,所以我们这里安装Mysql8.0,那么Mysql8.0和Mysql5.7之间的加密方式不一样,所以后期需要配置  
```bash
cd /installpackage/     #进入安装包文件夹
dpkg -i mysql-apt-config_0.8.10-1_all.deb  #选择第一项,mysql8.0,最后OK
apt-get update
apt-get install mysql-server   #安装mysql
```
中途需要输入两次root密码,第三次按shift+上选择第二项5.0X回车继续安装   
```bash
---------------------以下命令选用--------------------
sudo service mysql status   #检查MySQL服务器的状态
sudo service mysql stop    #停止MySQL服务器
sudo service mysql start   #重新启动MySQL服务器
--------------------------------------------------
```
```bash
sudo netstat -tap | grep mysql      #查询是否成功
 
--------------------------------成功出现------------------------------
tcp6     0    0 [::]:33060     [::]:*        LISTEN      10170/mysqld
tcp6     0    0 [::]:mysql     [::]:*        LISTEN      10170/mysqld
---------------------------------------------------------------------
```
如果安装成功,我们就可以使用root账号登录MySQL并设置用户了  
```bash
mysql -u root -p  #登录
mysql> select version();#查看mysql版本
+-----------+
| version() |
+-----------+
| 8.0.12    |
+-----------+
1 row in set (0.00 sec)
 
mysql> show variables like 'default_authentication_plugin'; #查看默认身份验证方式
+-------------------------------+-----------------------+
| Variable_name                 | Value                 |
+-------------------------------+-----------------------+
| default_authentication_plugin | mysql_native_password |
+-------------------------------+-----------------------+
1 row in set (0.01 sec)
 
mysql> select host,user,plugin from mysql.user;#查看用户的身份验证方式
+-----------+------------------+-----------------------+
| host      | user             | plugin                |
+-----------+------------------+-----------------------+
| localhost | mysql.infoschema | caching_sha2_password |
| localhost | mysql.session    | caching_sha2_password |
| localhost | mysql.sys        | caching_sha2_password |
| localhost | root             | mysql_native_password |
+-----------+------------------+-----------------------+
4 rows in set (0.00 sec)
```
可以看到MySQL8.0的认证方式有 `caching_sha2_password` 和 `mysql_native_password` ,目前默认的是 `mysql_native_password`;这两种验证方式  
其中 `caching_sha2_password` 是MySQL8.0的, `mysql_native_password` 是Mysql5.0的,至于为什么现在默认是5.0的,是因为我们上面 `apt-get install mysql-server` 时选择了第二项5.X,按照MySQL官方的说法叫做降级  
那么我么为什么不用8.0的加密呢?是因为我们目前用的数据库客户端,比如Navicat最新版还无法识别8.0的加密方式,无法进行远程连接  
  
如果不是旧版本认证方式,解决方案如下:  
```bash
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
 
Database changed
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root' PASSWORD EXPIRE NEVER;  #修改加密规则
Query OK, 0 rows affected (0.05 sec)
说明:两个'root'指查看用户的身份验证方式结果中的user列,localhost指host列
 
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';   #注意:这里root指root用户及密码为root
Query OK, 0 rows affected (0.09 sec)
 
mysql> FLUSH PRIVILEGES; #刷新权限
Query OK, 0 rows affected (0.01 sec)
```
目前我们可以看到root的host为locahost,只支持本地访问,若要配置远程,需要设置为%  
```bash
UPDATE USER SET HOST = "%" WHERE USER = "root";  #设置root支持远程登录
```
当然,为了安全起见,我们一般会设置一个远程用户<span style="color:#ff0000;">(示例:teaper)</span>  
```bash
CREATE USER 'teaper'@'%' IDENTIFIED BY 'password';  #创建teaper用户,密码自定义
GRANT ALL ON *.* TO 'teaper'@'%' WITH GRANT OPTION;  #授权
FLUSH PRIVILEGES;  #刷新权限
```
为了后面gitea的使用,我们需要创建一个远程gitea用户,并且只赋予它执行gitea数据库的权限,对其他数据库没有权限获取,用于存储gitea平台信息  
```bash
CREATE USER 'gitea'@'%' IDENTIFIED BY 'password';           #创建gitea用户,密码自定义
GRANT ALL ON gitea.* TO 'gitea'@'%';        #赋予gitea数据库的增删改查权限
create database gitea;          #创建gitea数据库
show databases;
FLUSH PRIVILEGES;
 
QUIT        #退出数据库
```
由于Mysql使用的是3306的端口,所以我们还需要去阿里云 -> 防火墙自 -> "自定义规则",添加3306端口,这样的话,就可以用Navicat远程Mysql了  
  
##### 安装JDK1.8  
```bash
mkdir /usr/jvm
tar -xzvf jdk-8u172-linux-x64.tar.gz -C /usr/jvm
vim /etc/profile     #配置环境变量
-------------------------------末尾追加---------------------------------
export JAVA_HOME=/usr/jvm/jdk1.8.0_172
export JRE_HOME={JAVA_HOME}/jre
export CLASSPATH=.:{JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=$JAVA_HOME/bin:$PATH
-----------------------------------------------------------------------
update-alternatives  --install /usr/bin/java java /usr/jvm/jdk1.8.0_172/bin/java 300
update-alternatives  --install /usr/bin/javac javac /usr/jvm/jdk1.8.0_172/bin/javac 300
update-alternatives --config java
java -version
```
  
##### 安装Tomcat9  
```bash
tar -xzvf apache-tomcat-9.0.11.tar.gz -C /
vim /etc/profile     #配置环境变量
---------------------末尾追加---------------------
export TOMCAT_HOME=/apache-tomcat-9.0.11
---------------------PATH添加--------------------
export PATH=$JAVA_HOME/bin:$TOMCAT_HOME/bin:$PATH
------------------------------------------------
. /etc/profile          #使环境变量生效
cd /apache-tomcat-9.0.11/
vim conf/server.xml
--------------------------修改----------------------------
#修改端口8080为80
<Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
 
#修改defaultHost为已解析的域名
<Engine name="Catalina" defaultHost="twtity.com">
 
#修改Host name为已解析的域名
<Host name="twtity.com"  appBase="webapps"
        unpackWARs="true" autoDeploy="true">
 
#Host中新增一项
<Context docBase="/项目名称" path="" debug="0" reloadable="true"/>
----------------------------------------------------------
./bin/startup.sh  #启动tomcat
```
  
##### 安装docker和docker-compose  
```bash
docker  #查看是否有安装
-------------------卸载旧版本------------------
apt-get remove docker docker-engine docker.io
---------------------------------------------
#添加Docker软件源
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates  curl software-properties-common
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
 
#安装Docker CE(社区版)
sudo apt-get update -y
sudo apt-get install docker-ce -y
 
#设置开机启动
sudo systemctl enable docker
sudo systemctl start docker
 
#把当前用户加入docker组
sudo groupadd docker
sudo usermod -aG docker $USER
 
#测试 docker
docker run hello-world
vim /etc/docker/daemon.json  #编辑该文件,使用Docker中国官方镜像
---------------daemon.json----------------
{
   "registry-mirrors": [
     "https://registry.docker-cn.com"
   ]
 }
------------------------------------------
 
#重启服务
sudo systemctl daemon-reload
sudo systemctl restart docker
 
#安装 docker composer
sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
 
#安装 docker composer自动补全命令
curl -L https://raw.githubusercontent.com/docker/compose/1.8.0/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```
  
##### 搭建gitea平台  
```bash
adduser git     #服务器新建git用户,输入两次密码,一路回车
id git  #记下git用户UID和GID (示例:UID:1001 GID:1001)
mkdir gitea && cd gitea         #新建并进入gitea文件夹
cp /installpackage/gitea-1.5.0-linux-amd64  /home/git/gitea/
vim docker-compose.yml      #创建docker-compose.yml文件,注意空格缩进,每级别4个空格
-------------docker-compose.yml编辑--------------
version: "2"
networks:
  gitea:
    external: false
services:
  server:
    image: gitea/gitea:latest
    environment:
      - USER_UID=1001     #UID
      - USER_GID=1001       #GID
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
    ports:
      - "3000:3000"        #http 3000端口
      - "10022:22"         #SSH 22端口
    depends_on:
      - db
  db:
    image: mysql:8.0
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword    #自定义修改
      - MYSQL_USER=gitea
      - MYSQL_PASSWORD=giteapassword    #自定义修改
      - MYSQL_DATABASE=gitea
    networks:
      - gitea
    volumes:
      - ./mysql:/var/lib/mysql
--------------------------------------------------
docker-compose up -d  #运行docker-compose.yml文件
```
最后打开[http://twtity.com:3000](http://twtity.com:3000)即可,如果不行,则去阿里云防火墙配置一个3000端口,为了专业一点,我们可以去阿里云 -> 域名,解析一条A记录git,记录值还是(39.108.51.25),再到轻量服务器 ->  域名中添加域名`git.twtity.com `  
  
再次进入[git.twtity.com:3000](git.twtity.com:3000),单击注册按钮,进行配置,记得把所有域名改成`git.twtity.com`,另外如果有没修改的也不用担心,我们可以直接修改[gitea配置](https://docs.gitea.io/zh-cn/config-cheat-sheet/)  
```bash
vim /home/git/gitea/gitea/gitea/conf/app.ini   #修改gitea配置
--------------------------app.ini修改或新增----------------------------
[server]
ROOT_URL         = https://git.twtity.com/    #https路径
DISABLE_SSH      = true                       #禁用SSH
 
[service]
REGISTER_EMAIL_CONFIRM            = true     #启用注册邮件激活
DISABLE_REGISTRATION            =true   #禁用注册，启用后只能用管理员添加用户
REQUIRE_SIGNIN_VIEW             =true   #是否所有页面都必须登录后才可访问
 
[markup.asciidoc]
ENABLED = true     #是否启用markup，默认为false
FILE_EXTENSIONS = .adoc,.asciidoc  #关联的文档的扩展名，多个扩展名用都好分隔
RENDER_COMMAND = "asciidoc --out-file=- -"  #工具的命令行命令及参数
IS_INPUT_FILE = false  #输入方式是最后一个参数为文件路径还是从标准输入读取
---------------------------------------------------------------------
```
登录gitea之后,我们可以新建一个仓库,试着clone一下,不出以外的话,HTTP方式应该是成功的,再去添加下本机公钥,试试SSH方式clone,SSH的话需要输入密码,就算输入root密码都依然无法登录,其原因是我们用容器安装的gitea,所有SSHKey在gitea目录中,并没有同步到主机的.ssh中,所以我们暂时在app.ini中禁用掉SSH  
  
使用Nginx做反向代理  
```bash
apt-get install nginx       #安装nginx
```
使用HTTPS加密  
去阿里云申请免费SSL证书,勾选自动添加解析记录,申请通过后将证书下载到本地,文件夹重命名为cert,里面有两个文件`.pem`和`.key`  
```
scp -r cert root@twtity.com:/etc/nginx/cert   #上传证书
vim /etc/nginx/nginx.conf   #配置nginx
-----------------------------------------------
server {
    listen       80;
    server_name  git.twtity.com;
    charset utf-8;
    rewrite ^ https://git.twtity.com;   #强制使用https
}
 
server {
    listen       443 ssl;       #443端口
    server_name  git.twtity.com;        #域名
    charset utf-8;
    ssl on;
    root html;
    index index.html index.htm;
    ssl_certificate         cert/214981108460483.pem;
    ssl_certificate_key     cert/214981108460483.key;
    ssl_session_timeout     10m;
    ssl_session_cache       shared:SSL:10m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location /{
        proxy_pass  http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }
    location ~ .*\.(js|css|png)$ {
        proxy_pass  http://127.0.0.1:3000;
    }
}
---------------------------------------------------------
nginx -s reload   #重启
```
由于我们nginx配置中需要用到443端口,所以还需要去"轻量服务器"  -> "防火墙"添加443端口  
