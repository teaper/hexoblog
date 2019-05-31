---
title: 阿里云ubuntu 16.04.5 server建站日志
date: 2019-05-19 22:40:17
categories: [Linux]
tags: [Ubuntu16.04,MySQL8.0,Gitea,Nginx,Docker,SSL]
---
#### 【一】预先准备  
本地系统：**Arch Linux**  
阿里云购买一个轻量服务器，地域随便,镜像选择`Ubuntu16.04`系统镜像 <span style="color:#ff0000;">(示例ip:39.108.51.25)</span>  
阿里云购买一个域名 <span style="color:#ff0000;">(示例域名:teaper.dev)</span>  
  
准备好以下安装包：  
> [jdk-8u172-linux-x64.tar.gz](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  
> [apache-tomcat-9.0.11.tar.gz](https://tomcat.apache.org/download-90.cgi)  
> [mysql-apt-config_0.8.10-1_all.deb](https://dev.mysql.com/downloads/repo/apt/)  
> [gitea-1.5.0-linux-amd64](https://dl.gitea.io/gitea/)  
  
#### 【二】服务器系统配置
登录阿里云控制台 -> 轻量应用服务器 -> 服务器运维 -> 远程连接  
单击"秘钥管理" -> 创建秘钥,选择第二项**导入已有秘钥**，然后将你本机SSH生成的`id_rsa.pub`的密文粘贴上去即可  
<details>
<summary style="color:#ff0000;">本机没有 id_rsa.pub 秘钥文件</summary>

```bash
sudo pacman -S openssh   #ArchLinux/Manjaro安装ssh服务，Ubuntu/deepin/Kali/Debian请将pacman改成apt
ssh-keygen -t rsa -C "youremail@example.com"    #输入常用邮箱，一路按Enter
cd ~/.ssh   #进入.ssh隐藏文件夹
gedit id_rsa.pub       #打开公钥并,复制id_rsa.pub文件中的密文
```
</details>
在“远程连接”选项卡，点击“设置密码”，初始化一个服务器root密码  
  
回到客户端选项卡，单击“[《Putty配置说明》](https://help.aliyun.com/document_detail/59083.html?spm=5176.10173289.107.1.6c5d2e77cHI0NI#windows)”，按照说明操作，由于我本机是Arch Linux的系统，并且支持SSH命令，所以接下来的命令是以linux系统为标准的  
```bash
ssh root@39.108.51.25  #无需密码，直接登录成功
```
这时已经自动使用`root`用户登录了服务器系统，此时命令行开头为 `root@iZwz9emoxkbmiopxeez3sfZ:`,其中 `iZwz9emoxkbmiopxeez3sfZ` 为主机名  
```bash
sudo   #测试sudo,由于我是用SSH的root用户登录的,后面有些命令就省略了sudo
```
若第一行出现以下错误信息  
```bash
sudo: unable to resolve host iZwz9emoxkbmiopxeez3sfZ
```
则修改修改hosts文件  
```bash
vim /etc/hosts  #使用vim修改hosts文件,输入i进入install模式
--------------------添加---------------------
127.0.0.1       iZwz9emoxkbmiopxeez3sfZ
---------------------------------------------
```
按Esc键退出INSERT模式,输入:wq,按回车保存退出(提示冒号q不保存退出)  
再次输入sudo测试一下是否正常  
  
##### 配置源  
<span style="color:#ff0000;">提示:粘贴之后特别注意前两行是否被修改,有的话重新粘贴前两行</span>  
```bash
vim /etc/apt/sources.list
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
apt-get update
apt-get upgrade   #更新时可能出现一个窗口,选择第一个install the package's version,回车
```
由于我们每次使用SSH登录都要输入IP地址，IP地址太麻烦记不住怎么办，这时候我们可以到阿里云控制台 -> 轻量应用服务器 -> 站点设置 -> 域名，单击"添加域名"按钮，设置一个已注册的域名<span style="color:#ff0000;">(示例域名:git.teaper.dev)</span>  
添加成功之后，解析状态会显示**未解析**，是因为`git.teaper.dev`是`teaper.dev`的一个子域名，所以需要去你的域名服务商官网，DNS解析一条**A记录**，样式如下：  

| 记录类型 | 主机记录 | 解析线路 | 记录值 | MX优先级 | TTL |
| -------- | ------------- | -------- | ------ | -------- | --- |
| A | git | 默认 | 39.108.51.25 | -- | 10分钟 |
  
下次使用ssh登录，就可以写成
```bash
ssh root@git.teaper.dev  #登录
```
  
##### 文件上传  
在本机电脑新建一个文件夹<span style="color:#ff0000;">(例如:pkg)</span>,将预先准备的安装包放进`pkg`文件夹中  
```bash
scp -r pkg root@git.teaper.dev:/pkg  #上传目录
------------------------------scp相关补充-----------------------
scp fileName root@IP:fileName  #上传文件
scp -r /opt/folderName root@IP:/opt/folderName  #上传目录
scp root@IP:fileName /dewnloads/fileName   #下载文件到指定目录
---------------------------------------------------------------
 
ssh root@git.teaper.dev  #登录进去看下
cd /pkg
ls -a
```
  
#### 【三】软件安装  
服务器没有图形界面，一个`bash`界面不够用，可以安装此工具辅助
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
<span style="color:#ff0000;">注意：到这里，基本系统配置已经完成，最好在阿里云控制台 -> 轻量应用服务器 -> 服务器运维 -> 快照，创建一个快照，方便下次恢复</span>
  
##### 安装MySQL  
我之前在博客[Ubuntu16.04搭建Java开发环境](https://www.teaper.dev/2019/05/21/ubuntu1604-install-java/)中安装本机 `MySQL` 都没有用到安装包，为什么这里又要了呐?  
之前本机是`Ubuntu`的桌面版本,`MySQL`版本为 5.7，如果我们使用 5.7 版本安装方法安装，则会出现 `MySQL` 不提示设置root密码的情况，那么就无法登录 `MySQL`，所以我们这里安装`MySQL8.0`，那么 `MySQL8.0` 和 `MySQL5.7` 之间的加密方式不一样，所以后期需要配置  
```bash
cd /pkg/     #进入安装包文件夹
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
如果安装成功，我们就可以使用root账号登录MySQL并设置用户了  
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
可以看到MySQL8.0的认证方式有 `caching_sha2_password` 和 `mysql_native_password` ，目前默认的是 `mysql_native_password`；这两种验证方式  
其中 `caching_sha2_password` 是MySQL8.0的， `mysql_native_password` 是Mysql5.0的，至于为什么现在默认是5.0的，是因为我们上面 `apt-get install mysql-server` 时选择了第二项5.X，按照MySQL官方的说法叫做降级  
那么我么为什么不用8.0的加密呢?是因为我们目前用的数据库客户端，比如Navicat最新版还无法识别8.0的加密方式，无法进行远程连接  
  
<details>
<summary style="color:#ff0000;">如果本地root用户不是 mysql_native_password 认证方式,解决方案如下: </summary>

```bash
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
 
Database changed
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root' PASSWORD EXPIRE NEVER;  #修改加密规则
Query OK, 0 rows affected (0.05 sec)
#说明:两个'root'指查看用户的身份验证方式结果中的user列,localhost指host列
 
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Root Password';   #注意:这里root指本地root用户
Query OK, 0 rows affected (0.09 sec)
 
mysql> FLUSH PRIVILEGES; #刷新权限
Query OK, 0 rows affected (0.01 sec)
```
</details>
  
目前我们可以看到root的host为locahost，只支持本地访问，若要配置远程，需要设置为%  
```bash
UPDATE USER SET HOST = "%" WHERE USER = "root";  #设置root支持远程登录
```
当然，**为了安全起见**，我们一般会设置一个远程用户<span style="color:#ff0000;">(示例:teaper)</span>  
```bash
CREATE USER 'teaper'@'%' IDENTIFIED BY 'password';  #创建teaper用户,密码自定义
GRANT ALL ON *.* TO 'teaper'@'%' WITH GRANT OPTION;  #授权
FLUSH PRIVILEGES;  #刷新权限
```
为了后面 gitea 的使用，我们需要创建一个远程 gitea 用户，并且只赋予它执行 gitea 数据库的权限，对其他数据库没有权限获取，用于存储 gitea 平台信息  
```bash
CREATE USER 'gitea'@'%' IDENTIFIED BY 'password';           #创建gitea用户,密码自定义
GRANT ALL ON gitea.* TO 'gitea'@'%';        #赋予gitea数据库的增删改查权限
CREATE DATABASE gitea;          #创建gitea数据库
SHOW DATABASES;
FLUSH PRIVILEGES;
 
QUIT        #退出数据库
```
由于 MySQL 使用的是 3306 的端口，所以我们还需要去阿里云控制台 -> 轻量应用服务器 -> 安全 -> 防火墙 -> 添加规则，添加 3306 端口  

| 应用类型 | 协议 | 端口范围 |
| -------- | ---- | -------- |
| MYSQL	| TCP |	3306 |
  
这样的话，就可以用 Navicat 远程 MySQL 了  
  
##### 安装JDK1.8  
```bash
mkdir /usr/jvm      #创建jvm文件夹
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
tar -xzvf apache-tomcat-9.0.11.tar.gz -C /usr/local/    #解压到/usr/local/文件夹
vim /etc/profile     #配置环境变量
---------------------末尾追加---------------------
export TOMCAT_HOME=/usr/local/apache-tomcat-9.0.11
---------------------PATH添加--------------------
export PATH=$JAVA_HOME/bin:$TOMCAT_HOME/bin:$PATH
------------------------------------------------
sh /etc/profile          #使环境变量生效
cd /usr/local/apache-tomcat-9.0.11/
vim conf/server.xml
--------------------------修改----------------------------
#修改端口8080为80
<Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
 
#修改defaultHost为已解析的域名
<Engine name="Catalina" defaultHost="git.teaper.dev">
 
#修改Host name为已解析的域名
<Host name="git.teaper.dev"  appBase="webapps"
        unpackWARs="true" autoDeploy="true">
 
#<Host>标签中新增一项
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
apt-get update
apt-get install apt-transport-https ca-certificates  curl software-properties-common
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
 
#安装Docker CE(社区版)
apt-get update -y
apt-get install docker-ce -y
 
#设置开机启动
systemctl enable docker
systemctl start docker
 
#把当前用户加入docker组
groupadd docker
usermod -aG docker $USER
 
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
systemctl daemon-reload
systemctl restart docker
 
#安装 docker composer
curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
 
#安装 docker composer自动补全命令
curl -L https://raw.githubusercontent.com/docker/compose/1.8.0/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```
  
##### 搭建gitea平台  
```bash
adduser git     #服务器新建git用户,输入两次密码,一路回车
id git  #记下git用户UID和GID (示例:UID:1001 GID:1001)
mkdir /usr/local/gitea && cd /usr/local/gitea         #新建并进入gitea文件夹
cp /pkg/gitea-1.5.0-linux-amd64  /usr/local/gitea
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
最后打开[http://git.teaper.dev:3000](http://git.teaper.dev:3000)即可，如果不行，则去阿里云防火墙配置一个3000端口  

| 应用类型 | 协议 | 端口范围 |
| -------- | ---- | -------- |
| 自定义	| TCP |	3000 |
  
再次进入[git.teaper.dev:3000](git.teaper.dev:3000)，单击注册按钮，进行配置，记得把所有域名改成`git.teaper.dev`，另外如果有没修改的也不用担心，我们可以直接修改[gitea配置](https://docs.gitea.io/zh-cn/config-cheat-sheet/)，顺便解决一下端口号的问题  
```bash
vim /usr/local/gitea/gitea/gitea/conf/app.ini   #修改gitea配置
--------------------------app.ini修改或新增----------------------------
[server]
ROOT_URL         = https://git.teaper.dev/    #https路径（将3000端口号删除）
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
登录 gitea 之后，我们可以新建一个仓库，试着 clone 一下，不出以外的话，HTTP 方式应该是成功的，再去添加下本机公钥，试试 SSH 方式 clone，SSH 的话需要输入密码，就算输入 root 密码都依然无法登录，其原因是我们用容器安装的 gitea，所有 SSH Key 在 gitea 目录中，并没有同步到主机的 .ssh 隐藏文件夹中，所以我们暂时在 app.ini 中禁用掉 SSH  
  
使用 Nginx 做反向代理  
```bash
apt-get install nginx       #安装nginx
```
使用 HTTPS 加密  
去阿里云域名管理平台申请免费 SSL 证书，勾选自动添加解析记录，申请通过后将证书下载到本地，文件夹重命名为 cert，里面有两个文件 `.pem` 和 `.key`  
```
scp -r cert root@git.teaper.dev:/etc/nginx/cert   #上传证书
vim /etc/nginx/nginx.conf   #配置nginx
-----------------------------------------------
events {
    #...省略其他配置
}

http {
    #...省略其他配置，主要配置以下部分
    server {
        listen       80;
        server_name  git.teaper.dev;
        charset utf-8;
        rewrite ^ https://git.teaper.dev;   #强制使用https
    }
     
    server {
        listen       443 ssl;       #443端口
        server_name  git.teaper.dev;        #域名
        charset utf-8;
        ssl on;
        root html;
        index index.html index.htm;
        ssl_certificate         cert/214981108460483.pem;   #两个证书路径
        ssl_certificate_key     cert/214981108460483.key;
        ssl_session_timeout     10m;
        ssl_session_cache       shared:SSL:10m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        location /{
            proxy_pass  http://127.0.0.1:3000;  #需要被代理的端口
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
        }
        location ~ .*\.(js|css|png)$ {
            proxy_pass  http://127.0.0.1:3000;
        }
    }
}
---------------------------------------------------------
nginx -s reload   #重启
```
由于我们 nginx 配置中需要用到 443 端口,所以还需要去轻量服务器  -> 防火墙添加 443 端口  
