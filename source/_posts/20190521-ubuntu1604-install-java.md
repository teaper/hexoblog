---
title: Ubuntu16.04搭建Java开发环境
date: 2019-05-21 18:01:55
categories: [Linux]
tags: [Ubuntu16.04,java]
---
### <center>常用命令</center>  
```bash  
su      #切换ROOT
sudo pkill Xorg    #注销,卡住时可按Ctrl+Fn+Alt+f2登录系统执行此命令(急救)
top  #查看当前运行的服务
sudo kill PID #杀进程
sudo miredo     #打开IPv6管理工具
sudo apt-get install -f     #处理依赖
sudo gedit /***     #打开文档
sudo mkdir ***      #创建文件夹
sudo rm -rf /***        #删除目录
sudo gedit ~/.bashrc        #修改当前用户环境变量
sudo gedit /etc/profile         #修改系统环境变量
sudo chmod a+x /***/***/fileName        #修改权限(a-所有用户 + 增加权限 x执行)
sudo chown -R teaper ./   #当前所有文件夹都使用用户teaper的权限
sudo touch ***      #创建文件
sudo tar -xzvf ***_tar.gz -C /***解压到的路径     #解压tar.gz包
sudo dpkg -i ***.deb        #安装包.deb
sudo tar xjf ***.tar.bz2 -C /****解压到的路径     #安装包.tar.bz2
reboot      #重启
java -version       #查看JDK
```  
日常清理：（偶尔使用，ubuntu一般不会产生无用垃圾）  
```bash
sudo apt-get autoclean                          #清理已经卸载软件的软件包
sudo apt-get clean                                  #清理下载的所有软件包
sudo apt-get autoremove                        #删除系统不再使用的孤立软件
 
df -hl                          #查看分区使用情况
```  
  
### <center>搭建开发环境</center>  
#### 【一】系统初始配置  
连上wifi,没网没真相,一切瞎扯蛋  
输入法选择拼音  

系统设置：   
安全和隐私：诊断的两个勾去掉  
亮度和锁屏：30分钟  
外观：行为 -> 打开 -> 灵敏度拉到最高 -> 开启工作区 ->在窗口的标题栏  
文本输入：双拼去掉，留下汉语、英语(美国)    
语言支持：有更新就安装下  
  
软件更新 -> ubuntu软件 -> 下载自`http://mirrors.aliyun.com/ubuntu` （注意：很重要，必须）   
首次设置root密码：`sudo passwd`  
  
快捷键设置：系统设置 -> 键盘 -> 快捷键 -> 自定义 -> + 输入名字，命令，点击禁用，按下快捷键  
系统监视器：`gnome-system-monitor` `Ctrl+Alt+Backspacebr`  
gnome终端： `gnome-termina supe+r`  
截图：`gnome-screenshot -a` `Ctrl+Alt+X`  
deepin-scrot截图：`deepin-scrot` `Ctrl+Alt+X`  
配置编辑器：`gconf-editor` `Ctrl+Alt+自定义`  
dconf编辑器：`dconf-editor` `Ctrl+Alt+自定义`   
  
  
多个磁盘设置挂载<span style="color:#ff0000;">(可选)</span>  
```bash
#挂载前格式化机械盘，重命名为E
sudo blkid  #查看分区UUID
-----------------示例结果-------------------
/dev/nvme0n1: PTUUID="c662d10c-d43d-4f14-8ea1-868c68402aa1" PTTYPE="gpt"
/dev/nvme0n1p1: UUID="860fe8ad-81ee-4e8c-b4c0-2c57d7674551" TYPE="swap" PARTUUID="5413290c-dede-4875-b731-9b61dd5bacc8"
/dev/nvme0n1p2: UUID="ED71-A468" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="01c87b5d-39ff-40ce-b274-f64447f5f3c1"
/dev/nvme0n1p3: UUID="4c2c7fd6-60a0-4ccb-bf37-33dbd1121754" TYPE="ext4" PARTUUID="7aa9d84c-f0ce-4aed-ab40-6b80d4e5e06b"
/dev/sda1: LABEL="E" UUID="685f2928-c306-4c16-9ec9-4999480de137" TYPE="ext4" PARTUUID="8f6c24b5-dd2c-4ccc-8918-76d5a1d53782"
--------------------------------------------
sudo gedit /etc/fstab   #编辑系统挂载配置文件
--------------------末尾追加--------------------
UUID=685f2928-c306-4c16-9ec9-4999480de137 /media    ext4    defaults    0   0
----------------------------------------------
```
<span style="color:#ff0000;">注：格式为 设备名称UUID 挂载点 分区类型 挂载选项 dump选项 fsck选项</span>  
dump选项：0 不备份  
fsck选项：0 不检查  
```bash
sudo reboot now     #重启
```  
  
#### 【二】系统清理篇  
##### 更新源  
[阿里源](https://opsx.alibaba.com/mirror)  
```bash
sudo gedit /etc/apt/sources.list
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
sudo apt-get upgrade
```  
  
##### 卸载libreOffice  
```bash
sudo apt-get remove libreoffice-common
```  
  
##### 删除Amazon的链接  
```bash
sudo apt-get remove unity-webapps-common
```  
  
##### 删除一些不常用的软件  
```bash
sudo apt-get remove thunderbird totem rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot
sudo apt-get remove gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku  landscape-client-ui-install
sudo apt-get remove onboard deja-dup
sudo apt-get remove --purge webbrowser-app
```  
 
##### 关闭来宾账户(可选)  
```bash
sudo sh -c 'echo "allow-guest=false" >> /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf'
sudo service lightdm restart
```
  
##### 卸载火狐(可选)  
```bash
dpkg --get-selections |grep firefox #查询所有和火狐相关的
---------------------找firefox开头的----------------------
firefox                     install
firefox-locale-en               install
firefox-locale-zh-hans              install
unity-scope-firefoxbookmarks            install
---------------------------------------------------------
sudo apt-get purge firefox firefox-locale-en firefox-locale-zh-hans
``` 
  
##### 安装BleachBit清理工具(可选)  
```bash
sudo apt-get install bleachbit      #安装
sudo apt-get remove bleachbit       #卸载
``` 
  
##### 安装vim文本编辑器(可选)  
```bash
sudo apt install vim        #安装
vimtutor        #使用教程
```  
  
##### 更新Linux内核  
[下载](http://kernel.ubuntu.com/~kernel-ppa/mainline/)  
```bash
uname -r        #查看当前使用的内核
注：按照自己的系统下载相应位数的内核，比如我的64位
--------------------------------------64位下载-------------------------------------
linux-headers-4.16.0-041600_4.16.0-041600.201804012230_all.deb
linux-headers-4.16.0-041600-generic_4.16.0-041600.201804012230_amd64.deb
linux-image-4.16.0-041600-generic_4.16.0-041600.201804012230_amd64.deb
---------------------------------------------------------------------------------------
--------------------------------------32位下载-------------------------------------
linux-headers-4.16.0-041600_4.16.0-041600.201804012230_all.deb
linux-headers-4.16.0-041600-generic_4.16.0-041600.201804012230_i386.deb
linux-image-4.16.0-041600-generic_4.16.0-041600.201804012230_i386.deb
----------------------------------------------------------------------------------------
sudo dpkg -i  linux-headers-4.16.0-041600_4.16.0-041600.201804012230_all.deb
              linux-headers-4.16.0-041600-generic_4.16.0-041600.201804012230_amd64.deb
              linux-image-4.16.0-041600-generic_4.16.0-041600.201804012230_amd64.deb    #多个内核一起安装，不会存在依赖问题
uname -r        #重启之后查看内核
 
删除不用的老旧内核(可选、慎用)
uname -r        #查看当前内核
dpkg --get-selections | grep linux      #查看所有Linux相关内核
#内核文件(linux-image-4.4.0~)， 头文件( linux-headers-4.4.0~、 linux-headers-4.4.0~generic~)
sudo apt-get purge  内核文件   头文件
```  
#### 【三】系统美化篇  
##### 安装unity-tweak-tool  
```bash
sudo apt-get install unity-tweak-tool
```  
  
##### 安装flatabulous-theme主题  
```bash
sudo add-apt-repository ppa:noobslab/themes
sudo apt-get update
sudo apt-get install flatabulous-theme
```
  
##### 安装numix图标  
```bash
sudo add-apt-repository ppa:numix/ppa
sudo apt-get update
sudo apt-get install numix-icon-theme-circle
```
<span style="color:#ff0000;">注：打开unity-tweak-tool主题和图标分别选择flatabulous和Numix-circle</span>  
启动器 -> 面板 -> 透明度最高，勾选对于最大化窗口不透明  
  
##### 安装docky（桌面地部任务栏）    
```bash
sudo apt-get install docky
```  
<span style="color:red;">注：设置-外观-行为</span>  
<span style="color:red;">打开自动隐藏启动器，显示位置设置为左边，灵敏度拉到最高，显示窗口菜单在窗口标题栏</span>  
<span style="color:red;">按win键，打开docky，右击设置，主题class,三维背景</span>  
  
取消下方docky第一个锚图标  
```bash
sudo apt install gconf-editor       #安装gconf-editor
gconf-editor        #运行
```  
![取消下方docky第一个锚图标](http://ww1.sinaimg.cn/large/006kWbIogy1g18tqqp7tpj30jl0g5q5d.jpg)  
  
##### 终端美化    
```bash
sudo apt-get install zsh        #安装zsh
```
下载 oh-my-zsh 项目来帮我们配置 zsh,这里需要先安装git  
```bash
sudo apt-get install git        #安装git
git config --global user.name "Your Name"   #配置git用户名
git config --global user.email "youremail@example.com"      #配置git邮箱
git config --list   #查看
查看home目录下是否有.ssh目录，一般情况是没有的
sudo apt-get install openssh-server  #安装SSH
ssh-keygen -t rsa -C "youremail@example.com"    #一路按Enter
cd /home/计算机名
ls -a       #看下有没有.ssh，有的话执行下一步
cd .ssh     #进入.ssh目录
li -a       #id_rsa是私钥，id_rsa.pub是公钥
sudo gedit id_rsa.pub       #打开公钥并复制id_rsa.pub文件中的内容
进入你自己的github(网址：https://github.com/)
进入Settings > SSH and GPG keys > New SSH key
新建一个key，名字随便，把复制的内容粘贴上去
 
提交项目:
git init        #在当前目录下建立一个.git文件夹
git add .       #把当前路径下的所有文件，添加到一个列表中(指定文件“.”改为文件名)
git commit -m '项目说明，必填'     #提交文件到本地仓库
git remote add origin git@github.com:用户名/仓库名.git        #添加远程主机
git push -u origin master   #把本地仓库上传到GitHub
 
sudo wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```
在终端任意地方右键，进入配置文件->配置文件首选项，弹出如下界面，进入‘颜色’一栏  
取消使用系统主题颜色，内置方案自定义，颜色橙色、绿色、黑色  
使用透明背景拉到40%，调色板内置方案Tango  
  
<span style="color:#ff0000;">其他版本控制工具(可选)</span>  
```bash
sudo apt install subversion     #安装SVN
```
　　
##### 字体    
ubuntu自带的字体不太好看，所以采用文泉译微米黑字体替代  
```bash
sudo apt-get install fonts-wqy-microhei
```
然后通过unity-tweak-tool来替换字体  
  
| 温泉一米黑Regular | 12 | 等宽一米黑Regular | 12 |
| ----------------- | -- | ----------------- | -- |
| 温泉一米黑Regular | 12 | 等宽一米黑Regular | 12 |
| RGBA | 1.00 |
| 轻微 |

<span style="color:#9B00FF;">解决gedit打开来源于windows平台的.txt文件乱码问题</span>
  
```bash
sudo apt install dconf-editor   #安装dconf编辑器
dconf-editor        #启动
```
找到org -> gnome -> gedit -> preferences -> encodings,修改['']为['GB18030','UTF-8','CURRENT','ISO-8859-15','UTF-16']   
![解决gedit打开来源于windows平台的.txt文件乱码问题](http://ww1.sinaimg.cn/large/006kWbIogy1g18tpvj04sj30m90hjgni.jpg)  
  
##### 自定义状态栏“Ubuntu 桌面”  
```bash
cd /usr/share/locale/en/LC_MESSAGES        # 英文环境（选一个，此路径很重要）
cd /usr/share/locale/zh_CN/LC_MESSAGES      # 中文环境
sudo gedit user.po      #创建文件user.po
-----------------------填入内容--------------------------
msgid "Ubuntu Desktop"
msgstr "自定义内容"
-------------------------------------------------------
sudo gedit startShell.sh    #创建文件startShell.sh
--------------------------填入内容--------------------------
Cur_Dir=/usr/share/locale/zh_CN/LC_MESSAGES #当前路径
echo 进入TITLE设置路径
#cd /usr/share/locale/en/LC_MESSAGES        # 英文环境
cd /usr/share/locale/zh_CN/LC_MESSAGES      # 中文环境（选一个）
echo 设置Ubuntu Title......
sudo msgfmt -o unity.mo $Cur_Dir/user.po
echo 设置成功
--------------------------------------------------------------------------------
sh startShell.sh    #测试是否成功！成功后重启电脑
cd /usr/bin
sudo gedit chtitle  #创建一个脚本
------------------------------------------内容如下----------------------------------
#!/bin/bash
config_dir=~/.config/chtitle
ver=1.0
uninstall() {
  rm -rf $config_dir
  rm /usr/bin/chtitle   #此文件所在位置
}
deploy() {
  if [ -f $config_dir/install_status ] ; then
    echo "chtitle已经完成配置"
  else
    curl --help
    if [ $? != 0 ] ; then
      echo "需要curl支持，开始安装curl"
      apt update
      apt install curl
    fi
    update
  fi
}
update() {
  echo "正在准备更新短语"
  if [ $(curl -I -s https://v1.hitokoto.cn -w %{http_code} | tail -n1) = 200 ]; then
    phrase=`curl -s https://v1.hitokoto.cn/?encode=text`
    echo "获取短语：\"$phrase\"  完成！正在更新到user.po......"
    sed -i "2c msgstr \"$phrase\"" /usr/share/locale/zh_CN/LC_MESSAGES/user.po
    echo "更新完成"
    sh /usr/share/locale/zh_CN/LC_MESSAGES/startShell.sh #执行脚本
  else
    echo "网络未连接!更新失败"
    exit
  fi
}
 
if [ $# = 0 ] ; then
     echo "安装:sudo chtitle deploy"
     echo "更新:sudo chtitle update"
     echo "卸载:sudo chtitle uninstall"
     echo "使用教程：http://note.youdao.com/noteshare?id=420731b9da5b2fe679d5978eba3eeb94"
  elif [ $1 = "deploy" ] ; then
    deploy
  elif [ $1 = "update" ] ; then
    update
  elif [ $1 = "uninstall" ] ; then
    uninstall
fi
-----------------------------------------------------------------------------------------
sudo chmod a+x chtitle  #添加权限
sudo chtitle deploy     #安装
sudo chtitle update     #更新句子，不喜欢可以继续执行update，重启电脑后生效
sudo chtitle            #查看chtitle全部命令
```
##### 状态栏新增网速、CPU及内存显示  
```bash
sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor
sudo apt-get update
sudo apt-get install indicator-sysmonitor
indicator-sysmonitor &      #运行
```
右击状态栏cpu -> Preferences ，勾上Run on startup复选框，这样就能开机启动了；切换到 Advanced 选项，可以对要显示的信息的格式进行设置  
网速:{net} CPU:{cpu} {cputemp} 内存:{mem} #Customize output参数  
关闭终端后会消失，重启电脑后生效  
  
#### 【四】软件安装篇  
开篇提示：  
所有安装指令都在安装包所在目录，右击空白处打开终端  
所有`.***`说明此文件或文件夹是隐藏的，使用ls -a查看  
  
##### 安装AXEL和WGET多线程下载  
```bash
sudo apt install axel
sudo apt-get install wget
```
使用方法：终端输入axel查看教程  
示例：`axel -n 10 "http://cdn2.ime.so7/sogou_pinyin.exe"`  
  
##### 使用BaiduPCS-Go下载百度云文件  
[下载](https://github.com/iikira/BaiduPCS-Go/releases)  
右击提取到当前目录  
```bash
sudo mv BaiduPCS-Go-v3.5.6-linux-amd64 /opt/    #将文件夹BaiduPCS-Go-v3.5.6-linux-amd64移动到opt目录
cd /opt/BaiduPCS-Go-v3.5.6-linux-amd64
./BaiduPCS-Go   #终端运行该文件
login   #登录
cd  文件夹名        #切换目录
config set -savedir /目录     #自定义下载目录
d   [文件或目录1]  [文件或目录2]  #下载
exit        #退出BaiduPCS-Go
sudo ln -s /opt/BaiduPCS-Go-v3.5.6-linux-amd64/BaiduPCS-Go /usr/bin/BaiduPCS    #添加到终端命令,输入BaiduPCS即可打开
```
[更多](https://github.com/iikira/BaiduPCS-Go)  
  
##### 安装截图工具DeepinScrot  
```bash
python --version    #查看python版本，需要python 2.16+
sudo apt-get install python-xlib
axel -n 10 http://packages.linuxdeepin.com/deepin/pool/main/d/deepin-scrot/deepin-scrot_2.0-0deepin_all.deb
sudo dpkg -i deepin-scrot_2.0-0deepin_all.deb   #安装
deepin-scrot    #启动成功去系统设置配置一个快捷键
```
##### 安装录屏工具  
动画GIF屏幕录像机[Peek](https://github.com/phw/peek)  
简单的屏幕录制程序[Kazam](https://launchpad.net/kazam)  
```bash
sudo add-apt-repository ppa:peek-developers/stable
sudo apt update
sudo apt install peek    #安装peek
 
sudo apt-get install kazam  #安装kazam
```
  
##### 谷歌浏览器  
[下载](https://www.google.cn/chrome/index.html)  
```bash
sudo dpkg -i google-chrome-stable_current_amd64.deb
```
XX-NET[下载](https://codeload.github.com/XX-net/XX-Net/zip/3.11.10)  
右键.zip提取到此处  
```bash
sudo mv XX-Net-3.11.10 /opt
```
[使用SwitchyOmega代理插件和更新情节模式](https://github.com/XX-net/XX-Net/wiki/%E4%BD%BF%E7%94%A8Chrome%E6%B5%8F%E8%A7%88%E5%99%A8#%E8%AE%BE%E7%BD%AE%E4%BB%A3%E7%90%86)  
```bash
#进入/opt/xx-net的解压目录，打开终端
sudo ./start    #安装xx-net，会在xx-net目录下生成root权限的data目录
sudo chown -R username ./       #username为当前使用的用户名
```
[导入证书](https://github.com/XX-net/XX-Net/wiki/%E4%BD%BF%E7%94%A8Chrome%E6%B5%8F%E8%A7%88%E5%99%A8#%E6%89%8B%E5%8A%A8%E5%AF%BC%E5%85%A5%E8%AF%81%E4%B9%A6%E5%A4%87%E9%80%89)  
```bash
./start #启动
sudo gedit xx-net.desktop   #创建快捷图标
-------------------------------填入以下内容-----------------------------
[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Icon=/opt/XX-Net-3.11.10/code/default/launcher/web_ui/favicon.ico
Icon[zh_CN]=/opt/XX-Net-3.11.10/code/default/launcher/web_ui/favicon.ico
Name[zh_CN]=xx-net
Exec=/bin/sh "/opt/XX-Net-3.11.10/start"
Comment[zh_CN]=network
Name=xx-net
Comment=network
----------------------------------------------------------------------
sudo chmod a+x xx-net.desktop       #设置权限
sudo cp xx-net.desktop /usr/share/applications      #复制到快捷方式文件夹
 
#设置IPv6
sudo apt-get install miredo     #安装虚拟网卡miredo
ifconfig    #查看网卡配置
sudo miredo     #启动miredo，重启电脑后连不上就用这句
```
<span style="color:#FF7400;">其他梯子搭建方式</span>  
先去国外网站买个VCS服务器,这里推荐[Vultr](https://www.vultr.com/?ref=7539377)  
新用户注册成功是在计费页面,说下Vultr的计费规则,它不像阿里云,一买就是年或月,而是按小时计算金额,金额从Vultr账户中扣除,所以我们需要先给账户充值金额,充值方式可以是paypal/支付宝/网银,我们先冲10美元  
我们选择一个Vultr云计算(VC2)中的服务器(推荐洛杉矶/日本/新加坡)  
系统选择CentOS/Ubuntu  
服务器大小选个便宜的$5/mo $0.007/h1 CPU 1024MB内存1000GB带宽  
<span style="color:red;">注意:1000GB带宽表示流量,用完我们可以把原来主机销毁,重新换个主机,再搭建一遍,不用加钱</span>  
成功之后.会显示系统正在安装,安装成功,点击进入管理界面,里面有基本IP地址,用户名,密码之类的  
  
现在我们使用本机终端窗口远程ssh连接  
```bash
ssh root@167.179.77.127   #会弹出输入密码,在把管理界面的密码输入进去
```
顺利进入系统之后,就可以进行ssr安装了  
```bash
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh    #安装ssh,会显示红色未安装
```
输入数字1继续安装  
要求输入端口号:不填默认2334  
要求输入密码:不填默认doub.io(建议填下)  
选择加密方式:输入数字10(别的也可以)  
选择协议插件:2(auth_sha1_v4)  
选择是否兼容原版ss客户端:n  
选择混淆插件:1  
  
进行混淆插件的设置后，会依次提示你对设备数、单线程限速和端口总限速进行设置，默认值是不进行限制，个人使用的话，选择默认即可，一路敲回车键,安装完成会出现配置信息,稍后使用(也可以通过运行./ssr.sh,输入5查看)  
  
安装谷歌BBR加速  
```bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
chmod +x bbr.sh  #给bbr.sh加权限
./bbr.sh
```
执行上面的代码，然后耐心等待，安装成功后重启VPS服务器即可(可以在Vultr网站上重启)  
本机下载ssr客户端[electron-ssr_0.2.4_amd64.deb](https://github.com/erguotou520/electron-ssr/releases)  
安卓客户端[shadowsocksr.apk](https://pan.lanzou.com/i0zfv5g)  
```bash
sudo dpkg -i electron-ssr_0.2.4_amd64.deb  #安装
```
安装成功之后,启动,等待安装ssr,成功之后,把上方ssr.sh运行之后的配置信息,一一对应填入,重启本机系统,开机之后启动elecron-ssr软件,应该就可以打开[google](https://www.google.com/)试试水了!  
<span style="color:#ff0000;">故障排除:如果vps可以ping通，ssr和brr安装顺利，没有出现无法启动等问题，看上去一切正常，vpn却无法使用，可以试试更改下ssr端口号</span>  
  
##### 搜狗输入法  
[下载](https://pinyin.sogou.com/linux/?r=pinyin)  
```bash
sudo add-apt-repository ppa:fcitx-team/nightly
sudo apt-get update
sudo apt-get install fcitx
sudo apt-get install fcitx-config-gtk
sudo apt-get install fcitx-table-all
sudo apt-get install im-switch
sudo dpkg -i sogoupinyin_2.2.0.0108_amd64.deb
```
中途若出现依赖问题，输入最上方的常用指令（处理依赖），再次执行原来命令  
重启电脑  
  
##### 网易云音乐  
[下载](http://music.163.com/#/download)  
```bash
sudo dpkg -i netease-cloud-music_1.1.0_amd64_ubuntu.deb
```
中途若出现依赖问题，输入最上方的常用指令（处理依赖），再次执行原来命令  
<span style="color:red;">注：设置下载目录，如果不设置，听过歌曲之后将无法设置</span>  
<span style="color:red;">注：如果出现单击云音乐图标,没打开软件的情况下,先去系统监视器关闭所有已知的网易云音乐服务,单击状态栏上的喇叭图标(音量),在下拉中打开网易云,一般系统开机第一次打开网易云都在这里打开</span>  
  
##### Wine-QQ  
百度网盘：链接:[https://pan.baidu.com/s/1miZg5mw](https://pan.baidu.com/s/1miZg5mw)　　密码:`tash`  
```bash
wget -nc https://dl.winehq.org/wine-builds/winehq.key
sudo apt-key add Release.key
sudo apt-add-repository 'https://dl.winehq.org/wine-builds/ubuntu/'
sudo apt-get update
sudo apt-get install winehq-stable  #安装wine稳定版 (开发版:winehq-devel)
 
------64位系统设置模拟32位windows(64位系统必选)-----------
rm -fr ~/.wine  #删除原来的目录
export WINEARCH=win32 
export WINEPREFIX="/home/USER/.wine"  #USER为你的系统用户名,不清楚自己看文件夹
winecfg 
------------------------------------------------------
 
tar xvf wineQQ8.9_19990.tar.xz -C ~/    #安装QQ
 
---------------------以下命令卸载wineqq------------------
rm -rf ~/.wine
rm -rf ~/.local/share/applications/wine-QQ.desktop
rm -rf ~/.local/share/icons/hicolor/256x256/apps/QQ.png
rm -rf ~/.fonts/simsun.ttc
--------------------------------------------------------
```
<span style="color:red;">注：安装完打开Wine QQ，提示安装wine **,一律点安装，启动QQ后设置好文件传输的路径,数据路径不可修改</span>  
```bash
---------------------------------wine配置(可选)-----------------------------
WINEARCH=win32 winecfg   #设置wine,默认Windows2000,建议设置为xp
sudo apt-get install cabextract #安装cabextract软件包,可以自动下载所需组件
sudo apt install winetricks   #winetricks组件大全(运行Windows游戏必备)
---------------------------------------------------------------------------
```
更多详细配置请参阅[如何安装和配置Wine？](https://askubuntu.com/questions/316025/how-to-install-and-configure-wine)  
  
##### Wine-TIM  
[下载](https://github.com/askme765cs/Wine-QQ-TIM)  
右键TIM.AppImage文件->Peroperties/属性->Permissions/权限->勾选Allow executing file as program/允许以应用程序执行复选框  
```bash
sudo mkdir /opt/tim
sudo mv sudo mv TIM-x86_64.AppImage /opt/tim/
sudo ln -s /opt/tim/TIM-x86_64.AppImage /usr/bin/tim    #添加到终端命令,输入tim可以启动程序
```
[下载](https://upload-images.jianshu.io/upload_images/348712-a245e7a10fbc0853.png)TIM图标,另存为tim.png  
```bash
sudo mv tim.png /opt/tim/
sudo gedit tim.desktop      #新建TIM快捷方式
--------------------tim.desktop内容-----------------------
[Desktop Entry]
Encoding=UTF-8
Name=Tim
Exec=/opt/tim/TIM-x86_64.AppImage
Icon=/opt/tim/tim.png
Terminal=false
Type=Application
Categories=Internet;
---------------------------------------------------------
sudo chmod a+x tim.desktop      #设置权限
sudo cp tim.desktop /usr/share/applications     #复制到快捷方式文件夹
```
  
##### 微信  
[下载](https://github.com/trazyn/weweChat)  
```bash
sudo dpkg -i wewechat-1.1.7-linux-amd64.deb
```
  
##### 安装远程工具TeamViewer  
[下载](https://www.teamviewer.com/zhcn/download/linux/)  
```bash
sudo apt install ./teamviewer_13.2.13582_amd64.deb
```
##### WPS安装及解决字体缺失  
WPS[下载](http://community.wps.cn/download/)　　#Alpha版，认准_amd64.deb  
缺失字体：[https://pan.baidu.com/s/1i5dzB9r](https://pan.baidu.com/s/1i5dzB9r)密码：`pwe1`  
```bash
sudo dpkg -i wps-office_10.1.0.5672_a21_amd64.deb
sudo mkdir /usr/share/fonts/wps-office
sudo cp -r wps_symbol_fonts.zip /usr/share/fonts/wps-office
cd /usr/share/fonts/wps-office
sudo unzip wps_symbol_fonts.zip
sudo rm -r wps_symbol_fonts.zip
```
##### 安装XMind流程图  
软件[下载](https://www.xmind.net/download/xmind8/)　　　LOGO[下载](http://s3.amazonaws.com/xmindnet/blog/en/new_branding_new_logo)  
<span style="color:red;">注意:浏览器下载比较慢,而且可能会中断,建议使用wget URL 方式下载</span>  
右击提取到当前目录  
```bash
sudo mv xmind-8-update8-linux /opt/
sudo mv xmind.png /opt/xmind-8-update8-linux/
cd /opt/xmind-8-update8-linux/XMind_amd64/
 
sudo gedit run.sh   #新建一个运行脚本
-------------------------------------------
cd /opt/xmind-8-update8-linux/XMind_amd64/
./XMind
-------------------------------------------
sudo chmod a+x run.sh
./run.sh    #测试一下是否可以运行
```
新建桌面快捷图标  
```bash
cd ../  #返回父级目录
 
sudo gedit xmind.desktop    #新建快捷方式
----------------------内容如下----------------------
[Desktop Entry]
Encoding=UTF-8
Name=XMind
Exec=/opt/xmind-8-update8-linux/XMind_amd64/run.sh
Icon=/opt/xmind-8-update8-linux/xmind.png
Terminal=false
Type=Application
Categories=GTK;GNOME;Office;
---------------------------------------------------
sudo chmod a+x xmind.desktop
sudo cp xmind.desktop /usr/share/applications
```
升级为Xmind pro 8  
下载破解文件[XMindCrack.jar](https://stormxing.oss-cn-beijing.aliyuncs.com/files/XMindCrack.jar)  
```bash
sudo mv XMindCrack.jar /opt/xmind-8-update8-linux/XMind_amd64/
cd /opt/xmind-8-update8-linux/XMind_amd64/
 
sudo gedit XMind.ini    #打开XMind.ini文件
---------------------------XMind.ini文件追加-----------------------
-javaagent:/opt/xmind-8-update8-linux/XMind_amd64/XMindCrack.jar
------------------------------------------------------------------
```
打开XMind, 点击帮助——序列号，然后输入以下序列号，邮箱随便填，可以填自己的
```bash
--------------------------------序列号-----------------------------
XAka34A2rVRYJ4XBIU35UZMUEEF64CMMIYZCK2FZZUQNODEKUHGJLFMSLIQMQUCUBX
RENLK6NZL37JXP4PZXQFILMQ2RG5R7G4QNDO3PSOEUBOCDRYSSXZGRARV6MGA33TN2
AMUBHEL4FXMWYTTJDEINJXUAV4BAYKBDCZQWVF3LWYXSDCXY546U3NBGOI3ZPAP2SO
3CSQFNB7VVIY123456789012345
------------------------------------------------------------------
```
##### JDK安装与配置  
[下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
```bash
sudo mkdir /usr/jvm  #创建jvm文件夹
sudo tar -xzvf jdk-8u172-linux-x64.tar.gz -C /usr/jvm  #解压jdk1.8到jvm文件夹
sudo tar -xzvf jdk-11.0.1_linux-x64_bin.tar.gz -C /usr/jvm/  #解压jdk11到jvm文件夹(可选)

sudo gedit /etc/profile     #配置环境变量(配置默认jdk)
-------------------------------末尾追加---------------------------------
export JAVA_HOME=/usr/jvm/jdk1.8.0_172
export JRE_HOME={JAVA_HOME}/jre
export CLASSPATH=.:{JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=$JAVA_HOME/bin:$PATH
-----------------------------------------------------------------------
#jdk1.8
sudo update-alternatives  --install /usr/bin/java java /usr/jvm/jdk1.8.0_172/bin/java 300
sudo update-alternatives  --install /usr/bin/javac javac /usr/jvm/jdk1.8.0_172/bin/javac 300  
#jdk11(可选)
sudo update-alternatives  --install /usr/bin/java java /usr/jvm/jdk-11.0.1/bin/java 300
sudo update-alternatives  --install /usr/bin/javac javac /usr/jvm/jdk-11.0.1/bin/javac 300

sudo update-alternatives --config java  #输入数字切换多个jdk版本
java -version  #查看jdk版本
```
##### Eclipse安装  
[下载](https://www.eclipse.org/downloads/eclipse-packages/)  
注：安装Eclipse需要先安装JDK  
```bash
sudo tar -xzvf eclipse-jee-oxygen-3a-linux-gtk-x86_64.tar.gz -C /opt
```
进入/opt/eclipse，双击eclipse文件  
  
##### MyEclipse安装破解  
[下载](http://www.myeclipsecn.com/download/)  
右击下载的.zip文件，提取到此处  
```bash
sudo mv myeclipse-2017-ci-10-offline-installer-linux.run /opt
cd /opt
sudo chmod a+x myeclipse-2017-ci-10-offline-installer-linux.run
sudo mkdir MyEclipse
su
./myeclipse-2017-ci-10-offline-installer-linux.run
```
安装位置选择/opt/MyEclipse,下一步，安装完成，进入安装目录  
```bash
sudo chown -R teaper ./
```
双击myeclipse打开软件  
下一步破解，需要先安装[rar](http://www.rarlab.com/download.htm)解压  
```bash
sudo tar -xzvf rarlinux-x64-5.6.b4.tar.gz -C /opt
cd /opt/rar
sudo make
------------------使用-------------------
# 将*****.rar压缩文件解压到当前文件目录
rar e *****.rar
# 将*****.rar压缩文件解压到*****目录下
rar x *****.rar
-------------------------------------
```
破解文件：[https://pan.baidu.com/share/init?surl=sm72r6H](https://pan.baidu.com/share/init?surl=sm72r6H)　　　密码：`jg3n`  
右击提取到本地，进入MyEclipse2017CI10破解文件/tools下  
```bash
sudo java -jar cracker2017.jar
```
输入一个Username,下拉菜单中选择plue,点两次SystemId,点Active，点Tooes > SaveProperties,复制Plugins内容到MyEclipse的plugins目录下  
  
##### Maven3安装及配置  
[下载](http://mirror.bit.edu.cn/apache/maven/maven-3/)  
```bash
sudo tar -xzvf apache-maven-3.5.3-bin.tar.gz -C /opt
sudo gedit /etc/profile     #配置环境变量
---------------------末尾追加---------------------
M2_HOME=/opt/apache-maven-3.5.3
export MAVEN_OPTS="-Xms256m -Xmx512m"
---------------------PATH添加--------------------
export PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH
------------------------------------------------
. /etc/profile      #使环境变量生效
mvn -version        #查看Maven版本
 
cd /opt/apache-maven-3.5.3/conf     #进入conf目录
sudo cp settings.xml ~/.m2/     #复制settings.xml到.m2文件夹下
cd ~/.m2
ls -a
sudo gedit settings.xml  #打开settings.xml文件
```
配置你的maven仓库地址  
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
<!-- 本地仓库地址 -->
<localRepository>${user.home}/.m2/repository</localRepository>
 
<!-- 阿里云镜像地址 -->
<mirrors>
    <mirror>
      <id>aliyunmaven</id>
      <mirrorOf>*</mirrorOf>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirrors>
 
<!-- 配置maven的jdk版本 -->
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
 
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```
##### Tomcat安装及配置  
[下载](https://tomcat.apache.org/download-90.cgi)　　　#认准.zip
找到压缩包右击提取到此处  
```bash
sudo chmod a+x apache-tomcat-9.0.8          #添加权限
sudo mv apache-tomcat-9.0.8 /opt
sudo gedit /etc/profile     #配置环境变量
---------------------末尾追加---------------------
export TOMCAT_HOME=/opt/apache-tomcat-9.0.8
---------------------PATH添加--------------------
export PATH=$JAVA_HOME/bin:$TOMCAT_HOME/bin:$PATH
------------------------------------------------
. /etc/profile          #使环境变量生效
```
注：Tomcat启动文件可能无法启动，若要测试可以使用eclipse  
  
##### MySQL安装  
提示：期间会弹出设置root账户的密码框，输入两次相同密码  
```bash
sudo apt-get install mysql-server   #安装mysql
sudo apt install mysql-client
sudo apt install libmysqlclient-dev
sudo netstat -tap | grep mysql      #查询是否成功
------------------------成功出现----------------------
tcp    0   0 localhost:mysql      *:*         LISTEN
16452/mysqld
----------------------------------------------------
mysql -u root -pfvh2014   #测试一下
sudo apt-get install mysql-workbench    #客户端安装
```
##### Navicat安装及破解  
[下载](https://www.navicat.com/en/products)  
```bash
sudo tar -zxvf navicat120_premium_cs_x64.tar.gz -C /opt
cd /opt/navicat120_premium_cs_x64
./start_navicat     #会提示安装wine***,install
```
打开之后出现乱码，左边试用，右边注册，点左边，字体很小，不要管，先退出  
```bash
sudo gedit start_navicat
export LANG="en_US.UTF-8" 改为 export LANG="zh_CN.UTF-8"
./start_navicat     #再运行看下
```
这时候发现字体好小，进工具 -> 选项改字体  
找张[图片](https://i.loli.net/2019/03/20/5c9162665551c.png)做LOGO  
修改图片名称为navicat.png  
```bash
sudo mv navicat.png /opt/navicat120_premium_cs_x64
sudo gedit Navicat.desktop
-------------------------------填入以下内容-----------------------------
[Desktop Entry]
Encoding=UTF-8
Name=Navicat Premium
Comment=The Smarter Way to manage dadabase
Exec=/bin/sh "/opt/navicat120_premium_cs_x64/start_navicat"
Icon=/opt/navicat120_premium_cs_x64/navicat.png
Categories=Application;Database;MySQL;navicat
Version=1.0
Type=Application
Terminal=0
----------------------------------------------------------------------
sudo chmod a+x Navicat.desktop      #设置权限
sudo cp Navicat.desktop /usr/share/applications     #复制到快捷方式文件夹
```
破解Navicat[参照](https://yq.aliyun.com/ziliao/5468)  
约定环境：  
navicat配置路径：/home/你的用户名/.navicat64 #点击Home所在的位置，打开终端  
原始文件user.reg：/home/你的用户名/.navicat64/user.reg #navicat读取配置使用  
备份文件user_backup.reg:/home/你的用户名/.navicat64/user_backup.reg  
```bash
ls -a   #查询下有没有.navicat64
cd .navicat64       #进入
ls -a       #原始文件叫system.reg、备份文件userdef.reg
```
使用创建好的快捷图标打开navicat，设置数据库连接信息,后面要记录  
```bash
sudo gedit user.reg     #复制里面的内容，保存
sudo gedit user_backup.reg      #新建一个脚本user_backup.reg记录数据库链接信息,粘贴内容，把每个[]后的数字和[重复内容***]这样的重复代码删除,保存
sudo gedit reset_navicat.sh     #开机自启脚本
-------------------------------编写启动脚本----------------------------------
#!/bin/sh
#！每次开机删除user.reg
rm /home/teaper/.navicat64/user.reg
#！将备份脚本中的内容换成navicat读取的配置
cp /home/teaper/.navicat64/user_backup.reg /home/teaper/.navicat64/user.reg
---------------------------------------------------------------------------
sudo chmod a+x reset_navicat.sh     #设置权限
/home/teaper/.navicat64/reset_navicat.sh    #24小时后运行一下试试
```
以后增加数据库链接，只需在user.reg中把新增加的数据库链接添加到user_backup.reg文件中即可  
  
##### ideaUI安装  
[下载](http://www.jetbrains.com/idea/download/#section=linux)  
```bash
sudo tar -xzvf ideaIU-2018.1.3.tar.gz -C /opt
```
进入idea解压目录下的bin文件夹，右击打开终端  
```bash
./idea.sh
```
> ---------------------------------安装配置-------------------------------------  
> Do not import settings　　　ok  
> 拉到最下面　　　Accept  
> Send Usage Statististics  
> username: teapers　　　password:Zxcvbnm9　　　Activate  
> 　　　　　　Next:Desktop Entry  
> 　　　　　　Next:Launcher Script  
> 自定义路径　　　Next:Default plugins  
> 
> ---------------------------------功能选择--------------------------------------  
> Java Frameworks: Hibernate Spring Struts JavaEE Velocity  
> Build Tools: Maven  
> Web Developent: HTML JavaScript CSS  
> Version Controls: Git GitHub  
> Test Tools: JUnit  
> Application Servers: Tomcat and TomEE  
> Clouds : DisableAll (一个都不要)  
> Swing: Disable  
> Android: Disable  
> Other Tools: UML  
> 
> 　　　　　　Next:Featured plugins  
> 　　　　　　Start using IntelliJ IDEA  
  
字体不好看：File > setting > Appearance 选WenQuanYi Micro Hei字体  
  
##### VMware Workstation Pro 14虚拟机（暂不可用）  
[下载](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)  
```bash
sudo apt install build-essential    #安装依赖
chmod +x VMware-Workstation-Full-14.1.2-8497320.x86_64.bundle
sudo ./VMware-Workstation-Full-14.1.2-8497320.x86_64.bundle
--------------------------激活码---------------------------------
YV50H-4MZ80-081XZ-97NZC-YQ8Y0
-----------------------------------------------------------------
```
  
##### VirtualBox虚拟机  
[下载](https://www.virtualbox.org/wiki/Downloads)  
```bash
sudo dpkg -i virtualbox-5.2_5.2.10-122088_Ubuntu_xenial_amd64.deb
 
lsmod |grep kvm     #查看系统VT功能是否开启,如果有Kvm说明cpu支持虚拟化,不过不一定开启
--------------------显示结果--------------------
#未开启(显示两项)
kvm                   554609  0
irqbypass              13503  1 kvm
 
#已开启(显示三项)
kvm_intel             170181  0
kvm                   554609  1 kvm_intel
irqbypass              13503  1 kvm
-----------------------------------------------
```
<span style="color:red;">如果VT功能未开启,则使用虚拟机镜像时,没有64位镜像选项,需要进入BIOS(F12/FN+F12/针眼)开启虚拟化技术,选择Configuration > Inter Virtual Technology 按Enter键,选择Enabled回车,按FN+F10保存</span>  
  
##### Thunderbird邮箱  
[下载](https://www.thunderbird.net/zh-CN/)  
```bash
sudo tar xjf thunderbird-52.7.0.tar.bz2 -C /opt
```
鼠标双击 opt/thunderbird/thunderbird 文件打开  
  
##### Sublime Text3安装破解汉化  
[下载](http://www.sublimetext.com/3)  
```bash
sudo tar xjf sublime_text_3_build_3170_x64.tar.bz2 -C /opt
```
> 进入opt/sublime_text3目录下，双击sublime_text打开  
> 单击help菜单下的enter license  
```bash
------------------------输入一下激活码------------------------
------------------------------------------------------------
```
> 汉化：  
> ctrl+shift+p，输入install，选择install Package  
> 安装完之后再在preferences 里面有一个Packages Control 打开  
> 然后在里面输入Chinese Localization安装完之后再在preferences  
> 里面有一个Packages Control 打开，然后在里面输入Chinese Localization  
  
解决中文输入问题：  
进入下载目录，打开终端  
```bash
git clone https://github.com/lyfeyaj/sublime-text-imfix.git
cd sublime-text-imfix
sudo cp ./lib/libsublime-imfix.so /opt/sublime_text_3
sudo gedit ./src/subl
--------------------修改成---------------------
#!/bin/sh
export LD_PRELOAD=/opt/sublime_text_3/libsublime-imfix.so
exec /opt/sublime_text_3/sublime_text "$@"
---------------------------------------------------
sudo cp ./src/subl /usr/bin/
终端输入subl命令试试能不能启动sublime，如果成功启动的话，应该就可以输入中文了
sudo gedit ./.local/share/applications/sublime_text.desktop
-------------------------修改----------------------
Exec=/opt/sublime_text_3/sublime_text
            修改为
Exec=/usr/bin/subl
------------------------------------------------------
```
插件和配置：  
在弹出的下一个页面里，即可输入各个插件的名字进行安装了  
修改各个插件的配置在sublime text 菜单中preferences->package settings->插件名->xxx settings   

一般有XXX-Default和XXX-User，一般从Default中把需要修改的项粘贴到User中进行修改，User配置文件的优先级比Default高  
> <center>插件列表</center>
>
> [Flatland](#)　　　#UI主题  
> [Emmet](#)　　　#快速编写HTML，CSS代码  
> [SublimeCodeIntel](#)　　　#代码提示插件，可以帮你补全代码  
> [jQuery](#)　　　#在使用jQuery的时候，给出对应的方法和API  
> [Alignment](#)　　　#该插件是用来对齐的（快捷键：ctrl+alt+a）  
> [DocBlocker](#)　　　#快熟书写注释  
> [JsFormat](#)　　　#javascript格式化插件  
> [BracketHighlighter](#)　　　#鼠标选中标签，对应标签会高亮  
> [sublimeLinter](#)　　　#提供js和css的语法检测  
> [SublimeTmpl](#)　　　#快速生成文件模板插件  
> [View in Browser](#)　　　#谷歌浏览器中浏览CTRL+ALT+C  
> [SideBarEnhancements](#)　　　#设置浏览器快捷键  
> [AutoFileName](#)　　　#在你输入引用文件路径时给出提示  
> [ConvertToUTF8](#)　　　#安装支持UTF8编码的一个插件  
  
配置浏览器及快捷键  
打开preferences菜单->package settings->View in Browser->settings-User  
输入:{"browser": "chrome"}  
打开preferences菜单->package settings->Side Bar->Key Bindings-User,粘贴以下内容  
```json
[
    { "keys": ["f12"],  //f12是快捷键,可以修改
        "command": "side_bar_open_in_browser" ,
        "args":{"paths":[], "type":"testing", "browser":""}
    },
    { "keys": ["alt+f12"],
        "command": "side_bar_open_in_browser",
        "args":{"paths":[], "type":"production", "browser":""}
    },
    {
        "keys": ["ctrl+t"],
        "command": "side_bar_new_file2"
    },
    {
        "keys": ["f2"],
        "command": "side_bar_rename"
    },
]
```
  
##### 适合21世纪的黑客文本编辑器Atom  
[官网](https://atom.io/)  
```bash
curl -sL https://packagecloud.io/AtomEditor/atom/gpgkey | sudo apt-key add -
sudo sh -c 'echo "deb [arch=amd64] https://packagecloud.io/AtomEditor/atom/any/ any main" > /etc/apt/sources.list.d/atom.list'   #添加Atom源
sudo apt-get update
sudo apt-get install atom  #安装Atom
 
---------------------快捷键--------------------
Ctrl+Shift+P   #作用同Sublime,全局搜索工具/文件
Ctrl+P         #查找已打开目录下的文件
Ctrl+\         #显示/隐藏树目录
----------------------------------------------
```
安装插件:单击Edit -> Preferences菜单,选择Install选项,输入插件名  
> <center>插件列表</center>
>
> [Emmet](＃)　　　#HTML补全  
> [Open in Browser](＃)　　　#浏览器打开工具  
> [Atom Beautify](＃)　　　#代码格式化  
> [color-picker](＃)　　#取色器  
> [file-icons](＃)　　　#文件图标  
> [Autocomplete Modules](＃)　　　#候选项(提示)  
> [Activate Power Mode](＃)　　　#打字泡沫  
> [docblockr](＃)　　　#文档化注释  
> 
> [markdown-preview-plus](＃)　　　#markdown增强预览(安装后禁用自带的markdown-preview)  
> [markdown-scroll-sync](＃)　　　#markdown同步滚动  
> [language-markdown](＃)　　　#代码增强  
> [markdown-image-paste](＃)　　　#图片粘贴  
> [markdown-table-editor](＃)　　　#表格编辑  
> [markdown-themeable-pdf](＃)　　　#PDF导出  
  
##### 安装Visual Studio Code  
[下载](https://code.visualstudio.com/docs/?dv=linux64_deb)  
```bash
sudo dpkg -i code_1.27.2-1536736588_amd64.deb
```
创建快捷图标(如果VSCode图标显示的是文本文档图标)  
```bash
cd /usr/share/code   #进入安装后目录
sudo gedit visualstudiocode.desktop
-----------------------内容如下----------------------
[Desktop Entry]
Name=Visual Studio Code
Comment=Multi-platform code editor for Linux
Exec=/usr/share/code/code
Icon=/usr/share/code/resources/app/resources/linux/code.png
Type=Application
StartupNotify=true
Categories=TextEditor;Development;Utility;
MimeType=text/plain;
----------------------------------------------------
sudo chmod a+x visualstudiocode.desktop
sudo cp visualstudiocode.desktop /usr/share/applications
cd /usr/share/applications/     #进入图标目录
sudo rm code.desktop        #删除原来的图标
```
> <center>插件列表</center>
>
> [Material](#)　　　#一款冷门主题    
> [One Dark Pro](#)　　　#源于Atom的主题  
> [Power Mode](#)　　　#打字泡沫  
> 
> [vscode-icon](#)　　　#树目录加上图标  
> [Path Intellisense](#)　　　#默认路径补全  
> [Document this](#)　　　#快速注释  
> [Project Manager](#)　　　#多个项目切换  
> [vscode-fileheader](#)　　　#顶部注释模板  
> [filesize](#)　　　#底部显示文件大小  
> 
> [open-in-browser](#)　　　#打开浏览器插件  
> [Atuo Rename Tag](#)　　　#同时修改html标签首尾  

##### 安装go语言  
[官网](https://golang.org/)  
```bash
wget https://dl.google.com/go/go1.10.3.linux-amd64.tar.gz       #下载Go
tar -xvf go1.10.3.linux-amd64.tar.gz    #解压Go包
sudo mv go /opt
sudo gedit /etc/profile
-------------------------配置环境-------------------------
export GOROOT=/opt/go
export PATH=$GOROOT/bin:$PATH
---------------------------------------------------------------
配置完重启
go --version        #查看环境是否生效
```
##### 安装kchmviewer  
我们做开发,需要查看很多.chm类型的开发手册,就需要这个软件  
```bash
sudo apt-get update
sudo apt-get install kchmviewer     #安装
```
<span style="color:red;">注意:.chm文件名及该文件所在路径都只能是英文,否则打不开还会报错</span>  

##### 安装Redis  
[下载](http://download.redis.io/releases/)  
右键.zip提取到此处  
```bash
sudo mv redis-4.0.10 /opt      #移动到opt目录
su            #root权限
apt-get install make   #安装make
apt-get install gcc    #安装gcc
cd /opt/redis-4.0.10
make    #编译redis
make install  #安装,服务在/usr/local/bin目录
redis-server /opt/redis-4.0.10/redis.conf  #启动redis
```
另外再开一个终端窗口  
```bash
redis-cli ping      #如果返回PONG说明一切OK
 
sudo gedit redis.conf   #配置
------------------redis.conf------------------
daemonize yes    #必配
logfile "/var/log/redis.cj.log"  #可选
dir /opt/redis-4.0.10/data   #可选
----------------------------------------------
```
<span style="color:red;">注意:redis.cj.log文件需要授权,data目录需要手动创建</span>  
在现有的navicat破解脚本上追加开机自启redis命令  
```bash
sudo gedit /home/teaper/.navicat64/reset_navicat.sh   #该文件的创建请参考上方navicat安装及破解
-------------reset_navicat.sh-----------------
#!/bin/sh
#!redis开机自启
redis-server /opt/redis-4.0.10/redis.conf
----------------------------------------------
reboot  #重启
redis-cli ping  #测试
```
<span style="color:#FF7400;">**安装Node.js及GitBook**</span>  
安装Node.js  
```bash
sudo apt-get update #检查更新
sudo apt-get install nodejs  #安装node.js(低版本)
sudo apt install nodejs-legacy
sudo apt install npm    #安装npm
 
sudo npm config set registry https://registry.npm.taobao.org    #更换淘宝镜像
sudo npm config list    #查看下配置是否生效
 
sudo npm install n -g       #安装版本更新工具N
--------------------工具N使用方法-----------------
n                       显示已安装的Node版本
n latest                安装最新版本Node
n stable                安装最新稳定版Node
n lts                   安装最新长期维护版(lts)Node
n <version>             根据提供的版本号安装Node
-------------------------------------------------
sudo n stable   #更新node版本
 
node -v     #查看node.js版本
npm -v      #查看npm版本
```
安装GitBook  
```bash
sudo npm install gitbook-cli -g      #使用npm安装,前提是你先安装了Node.js
```
  
#### 【五】游戏安装篇  
<span style="color:#FF7400;">开篇提示:Linux安装游戏,大多需要使用到Wine已经安装了(安装过wine-QQ的)的不再累赘,以下为Wine安装及配置,更多详细配置请参阅</span>[如何安装和配置Wine？](https://askubuntu.com/questions/316025/how-to-install-and-configure-wine)  
```bash
wget https://dl.winehq.org/wine-builds/Release.key
sudo apt-key add Release.key
sudo apt-add-repository 'https://dl.winehq.org/wine-builds/ubuntu/'
sudo apt-get update
sudo apt-get install winehq-devel  #安装wine开发版
 
rm -fr ~/.wine  #删除原来的64位目录
export WINEARCH=win32  #设置为32
export WINEPREFIX="/home/USER/.wine"  #USER为你的系统用户名
winecfg  #更新Wine配置
 
WINEARCH=win32 winecfg   #设置wine,默认Windows2000,建议设置为xp
sudo apt-get install cabextract #安装cabextract软件包
sudo apt install winetricks   #winetricks组件大全(运行Windows游戏必备)
```
  
##### 安装Steam  
Steam对linux系统的[要求](https://github.com/ValveSoftware/steam-for-linux)  
下载steam[安装包](http://media.steampowered.com/client/installer/steam.deb)(32位X86的)  
```bash
sudo dpkg -i steam_latest.deb  #安装deb包
sudo apt-get install -f #处理依赖问题(64位系统)
sudo dpkg -i steam_latest.deb  #再次安装
```
这时候会弹出一个Infomatin available窗口,单击Start Steam,出现安装进度,安装成功登录即可  
Steam游戏有Windows,Mac,SteamOS三种类型,SteamOS类型游戏直接安装即可  
游戏安装成功之后,启动报"steam无法连接到更新服务器"错误,在Steam客户端的Steam菜单 -> 设置 -> 下载 -> China-Hong Kong -> 确定,更换服务器位置,重启后再次尝试启动游戏  
  
##### 安装红色警戒2之尤里地复仇  
需要先安装snapd  
```bash
sudo apt install snapd snapd-xdg-open
```
需要使用wine-platform才能运行游戏  
```bash
sudo snap install wine-platform
```
由于红警2需要使用红警的资源文件，所以索性一并安装  
```bash
sudo snap install cncra cncra2yr  #红警：cncra 红警2：cncra2yr
```
卸载方式如下：  
```bash
sudo snap remove cncra cncra2yr
```
