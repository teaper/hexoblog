---
title: ArchLinux搭建Java开发环境
date: 2019-05-19 21:50:34
categories: [Linux]
tags: [Arch Linux,java]
---
### 【系统安装篇】 
#### 磁盘空间准备
<span style="color:#ff0000;">注意：安装单系统的跳过此步骤(例如：我)</span>   
Windows：参考[如何删除磁盘分区](https://jingyan.baidu.com/article/1876c852b7f88f890a13765e.html);空出一块空闲空间即可  
[Linux](https://upload.wikimedia.org/wikipedia/commons/1/1b/Linux_Distribution_Timeline.svg)：使用[`fdisk`](http://www.runoob.com/linux/linux-comm-fdisk.html)命令，简单粗暴
  
#### 启动盘制作  
首先去163的镜像源下载镜像文件[archlinux-2019.01.01-x86_64.iso       ](http://mirrors.163.com/archlinux/iso)  
**Windows制作方式:** 推荐使用[usbwriter](https://sourceforge.net/projects/usbwriter/)这款轻量级的工具  
**Linux制作方式:**  
```bash
fdisk -l   #查看U盘设备(例如：/dev/sdb)
sudo dd if=archlinux-2019.01.01-x86_64.iso of=/dev/sdb bs=1440k oflag=sync    #使用dd命令制作启动盘
```
#### 修改BIOS引导  
参考百度经验——[联想小新笔记本设置U盘启动教程](https://zhinan.sogou.com/guide/detail/?id=316512718960)  
在BIOS Setup中的Security选项卡中把Secure Boot设置为Disable  
在Boot选项卡中把Boot Mode改成Legac Support；Boot Priority改成UEFI First  
<span style="color:#ff0000;">注意：如果没有UEFI选项，请选择BIOS引导(请记住你的引导方式：本教程采用**UEFI**方式)</span>      
保存退出重启  
<center>【BIOS引导界面】</center>  
  
![BIOS](http://ww1.sinaimg.cn/large/006kWbIogy1g18tf84370g30hs0dcju0.gif)  
    
<center>【UEFI引导界面】</center>
  
![UEFI](http://ww1.sinaimg.cn/large/006kWbIogy1g18tgy46kgg30sg0lca9y.gif)  
  
当屏幕上出现命令行提示符及闪烁的光标时即启动完毕  
![](http://ww1.sinaimg.cn/large/006kWbIogy1g18ti221p1g30m806o0si.gif)  
  
由于我之前系统是Ubuntu，所以Boot Menu上还有Ubuntu的引导按钮，需要[删除启动菜单多余选项](https://jingyan.baidu.com/article/67508eb426c8ad9ccb1ce445.html)  
  
#### 再次确认引导方式  
在命令提示符下执行 
```bash
ls /sys/firmware/efi/efivars    #如果出现大量信息，则为UEFI引导
```
如果出现`ls: cannot access '/sys/firmware/efi/efivars': No such file or directory
`则为BIOS引导  
  
#### 检查网络连接  
虚拟机环境下可能是有网络的，如果是笔记本，必定没有网络    
```bash
ping www.baidu.com  #看是否出现连续的ip信息
```
![ping](http://ww1.sinaimg.cn/large/006kWbIogy1g18tirmfuzg30m806otcy.gif)  
  
如果没有出现连续信息，那就是网络不通  
  
#### 连接网络  
ArchLinux安装是需要网络的，所以必须连上网络，以下提供三种连网方式<span style="color:#ff0000;">(三选一，非多选，推荐一)</span>  
**方法一**：使用 WiFi 连接，请使用 wifi-menu 命令  
```bash
wifi-menu   #选择wifi，输入密码连接
```
**方法二**：使用DHCP方式连接(需要是有线网并且路由器支持DHCP动态主机设置协议)  
```bash
dhcpcd
```
**方法三**：使用 ADSL 宽带连接  
```bash
pppoe-setup     #配置
systemctl start adsl    #连接ADSL
```
  
#### 更新系统时间  
```bash
timedatectl set-ntp true    #设置时钟
```
  
####  修改源文件  
```bash
vim /etc/pacman.d/mirrorlist    #使用vim编辑源文件
```
在没有`#`注释的第一行添加以下内容，输入`:wq`保存退出  
```bash
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch   #使用中科大的源
Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch #阿里源
```
  
#### 分区  
<span style="color:#ff0000;">注意：分区操作涉及到SSD和机械硬盘，务必想清楚再下手</span>  
这里，我先介绍我目前磁盘情况  
> 128G固态硬盘+500G机械硬盘
  
查看目前硬盘情况  
```bash
lsblk   #查看硬盘情况  
```
结果如下：  
```bash
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0       7:0    0 476.7M 1 loop /rn/archiso/sfs/airootfs      #iso镜像
sda           8:0    0 465.8G  0 disk   #465.8G的机械硬盘sda 
└─sda1        8:1    0 465.8G  0 part /mnt/home #sda下的第一个分区sda1
sdb           8:16   1  14.6G  0  disk  #16G的启动盘
├─sdb1    8:17    1  588M  0 part  /runarchiso/bootmnt
└─sda2    8:18    1  64M  0  part
nvme0n1     259:0    0 119.2G  0 disk   #119.2G固态硬盘nvme0n1 
├─nvme0n1p1 259:1    0   100G  0 part /mnt  #nme0n1第一个分区nvme0n1p1
└─nvme0n1p2 259:2 0 19.2G   0  part  /mnt/boot/efi  #nme0n1第二个分区nvme0n1p2
```
首先，我们确定要格式化的硬盘有机械硬盘`sda`和固态硬盘`nvme0n1`  
那么，目前不论俩个硬盘下的分区如何，都需要被格式化掉  
假设俩个硬盘格式化好了，那么如何来分区呢？  
  
推荐以下分区方案  
> 固态硬盘(nvme0n1) ：119.2GB分三个区  
> nvme0n1p1  100GB  Linux Filesystem  根/分区  
> nvme0n1p2  9.2GB  EFI System  EFI分区  
> nvme0n1p3  10GB  Linux Swap   交换区  
> 
> 机械硬盘(sda)：465.8GB分一个区就好  
> sda1 465.8GB Linux Filesystem  HOME分区  
  
虚拟机用户只有一个盘就不需要机械硬盘分区方案  
接下来，就要开始撸命令啦  
```bash
cfdisk /dev/nvme0n1  #对固态硬盘进行分区
```
使用delete按钮删除原来的分区，然后重新new新分区，分区大小按照上面方案设置，在Type中选择分区类型(例如：EFI System)，new出三个分区后，选择write按钮，输入yes保存，选择Quit按钮退出  
```bash
cfdisk /dev/sda #对机械硬盘分区
```
方法和方案同上，分好可以通过`lsblk`查看分区情况确认无误  
  
#### 格式化分区  
上一步分好了分区，但是还没格式化特定类型，如果不格式化或格式化类型错误都会导致后面grub引导错误，从而安装失败，这里请睁大眼睛，按照我的示例一一对应你自己的分区  
<span style="color:#ff0000;">注意：格式化是对硬盘分区操作的,不是硬盘，别看错了</span>  
```bash
mkfs.ext4 /dev/nvme0n1p1  #格式化根分区，类型为ext4
mkfs.vfat /dev/nvme0n1p2  #格式化EFI分区，类型为vfa
mkswap -f /dev/nvme0n1p3 #格式化Swap分区
swapon /dev/nvme0n1p3  #使用swapon命令将Swap分区打开
mkfs.ext4 /dev/sda1  #格式化HOME分区，类型为ext4
```
  
#### 挂载分区  
挂载就是把一块分区指定在某个文件夹下，当我们打开这个文件夹的时候，文件夹的空间大小就是使用该分区的存储空间(文件夹不占空间)  
/mnt文件夹是启动盘中的引导文件夹，由于我们目前所有操作都还在启动盘里，所以需要把硬盘分区挂载到启动盘下的/mnt下，才能进入硬盘进行系统安装和配置，/mnt就相当于打开新系统的大门或者钥匙  
```bash
mount /dev/nvme0n1p1 /mnt  #根分区挂载到/mnt

mkdir /mnt/home  #创建home文件夹
mkdir -p /mnt/boot/EFI   #创建boot文件夹及其子文件夹EFI

mount /dev/sda1 /mnt/home  #HOME分区挂载到/mnt/home
mount /dev/nvme0n1p2 /mnt/boot/EFI  #EFI分区挂载到/mnt/boot/EFI
```
挂载好可以输入`lsblk`查看下挂载结果  
```bash
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda           8:0    0 465.8G  0 disk 
└─sda1        8:1    0 465.8G  0 part /home
nvme0n1     259:0    0 119.2G  0 disk 
├─nvme0n1p1 259:1    0   100G  0 part /
├─nvme0n1p2 259:2    0   9.2G  0 part /boot/EFI
└─nvme0n1p3 259:3    0    10G  0 part [SWAP]
```
  
#### 安装系统  
确认硬盘挂载无误之后，我们就可以开始安装系统及其相关组件了  
```bash
pacstrap /mnt base    ​#安装base组件包到/mnt
pacstrap /mnt base-devel    ​#安装base-devel 开发组件包到/mnt
```

#### 分区挂载情况写入到fstab中  
```bash
genfstab -U /mnt >> /mnt/etc/fstab 
cat /mnt/etc/fstab  #检查一下
```
  
#### 切换到安装的系统  
```bash
arch-chroot /mnt    #进入/mnt下的系统
```
这时候，我们就要离开启动盘系统，进入/mnt下安装的新系统ArchLinux进行必要的配置了，后面操作的都是硬盘上ArchLinux上的文件了  
  
#### 设置时间  
配置ArchLinux时间  
```bash
ln -sf /usr/share/zoninfo/Asia/Shanghai /etc/localtime  #设为上海时间
hwclock --systohc --utc     #设置硬件时间
```
  
#### 修改编码格式  
由于新系统没有vim，所以要安装一下  
```bash
pacman -S vim   #安装vim
```
使用vim编辑locale.gen文件  
```bash
vim /etc/locale.gen
```
找到`en_US.UTF-8 UTF-8`和`zh_CN.UTF-8 UTF-8`，把前面的`#`号删除，保存退出  
```bash
locale-gen  #重新生成locale
echo LANG=en_US.UTF-8 > /etc/locale.conf  #生成locale.conf文件
cat /etc/locale.conf    #查看一下
```
#### 创建主机名  
```bash
echo Arch > /etc/hostname   #这里的Arch就是自定义的主机名
vim /etc/hosts  #编辑hosts文件
```
添加以下配置：  
```bash
127.0.0.1   localhost.localdomain   localhost
::1                 localhost.localdomain   localhost
127.0.1.1   Arch.localdomain    Arch
```
  
#### 安装网络连接组件  
这里我提供三种方案，选一个就行<span style="color:#ff0000;">(例如我：无线网络组件)</span>  
  
**无线网络组件：**
```bash
pacman -S iw wpa_supplicant dialog  #无线网络
```
后面重启系统后可以使用`wifi-menu`命令连网  
  
**有线网络组件：**  
<span style="color:#ff0000;">注意：笔记本用户千万别手欠，觉得自己笔记本也有网线插口就执行以下命令，否则系统安装好，DNS也会无法解析，除非你真的是使用网线方式连的网络</span>  
```bash
systemctl enable dhcpcd     #进入系统自动连网
systemctl start dhcpcd      #重启后执行此命令启动网络服务
```
  
**其他网络组件：**  
<span style="color:#ff0000;">ADSL电话线方式入网的，现在基本不用了，我写出来也只是为了完善教程</span>  
```bash
pacman -S rp-pppoe pppoe-setup 
systemctl start adsl    #启动ADSL
```
  
#### 设置ROOT用户  
```bash
passwd  #设置root密码
```
  
#### 安装Intel-ucode(CPU非Intel跳过)  
```bash
pacman -S intel-ucode
```
  
#### 安装Bootloader  
安装引导方式，一开始我们就已经查看过电脑的引导方式了，这里我提供两个方案<span style="color:#ff0000;">(根据你自己的引导方式选择一个)</span>  
> BIOS/MBR引导  
> EFI/GPT引导  
  
如果你的电脑还存在其他系统<span style="color:#ff0000;">(例如:双系统用户)</span>，那么还需要安装`os-prober`包，它可以自动检测电脑中的其他系统，自动设置到启动选项  
```bash
pacman -S os-prober
```
**BIOS/MBR引导**  
```bash
pacman -S grub  #安装grub
grub-install --target=i386-pc /dev/sdx #/dev/sdx是已经完成分区的磁盘，grub将安装到它上面(注意：不是分区，不清楚可以用lsblk命令查看)
grub-mkconfig -o /boot/grub/grub.cfg    #生成配置文件
```
**EFI/GPT引导**  
```bash
pacman -S grub efibootmgr   #安装grub与efibootmgr两个包
grub-install --target=x86_64-efi --efi-directory=/boot/EFI --bootloader-id=ArchLinux #部署grub
grub-mkconfig -o /boot/grub/grub.cfg    #生成配置文件
```
<span style="color:#ff0000;">如果在上面(两种方式)配置引导的过程中报`warning failed to connect to lvmetad，falling back to device scanning.`错误；将`/etc/lvm/lvm.conf`文件中的`use_lvmetad = 1`改为0，保存，重新配置`grub`</span>  
  
为了保证引导正确性，使用vim检查一下配置文件内容  
```bash
vim /boot/grub/grub.cfg
```
检查以`menuentry`开头的代码部分是否有`windows 10`或其他系统名入口(双系统用户)，示例如下：  
```bash
menuentry 'Arch Linux' --class arch --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-simple-98746137-6e37-4e0c-a1a6-096b07b1fab3' {
	load_video
	set gfxpayload=keep
	insmod gzio
	insmod part_gpt
	insmod ext2
	if [ x$feature_platform_search_hint = xy ]; then
	  search --no-floppy --fs-uuid --set=root  98746137-6e37-4e0c-a1a6-096b07b1fab3
	else
	  search --no-floppy --fs-uuid --set=root 98746137-6e37-4e0c-a1a6-096b07b1fab3
	fi
	echo	'Loading Linux linux ...'
	linux	/boot/vmlinuz-linux root=UUID=98746137-6e37-4e0c-a1a6-096b07b1fab3 rw  quiet
	echo	'Loading initial ramdisk ...'
	initrd	/boot/intel-ucode.img /boot/initramfs-linux.img
}
```
  
#### 重启  
```bash
exit    #退出新系统
umount -R /mnt #取消/mnt挂载
reboot #重启
```
  
### 【桌面安装篇】  
通过上面的操作，不出意外的话，Arch是成功装好了(U盘可以拔了)，但还是命令行界面，接下来我们就要为Arch安装桌面以及配置一些必要参数  
```bash
wifi-menu   #连接wifi
```
连上之后记得`ping www.baidu.com`，看看有没有用  
  
#### 添加用户  
如果这里不添加，安装完桌面后，登录界面没有用户出现<span style="color:#ff0000;">(root用户不会出现在gdm登录界面)</span>  
```bash
useradd -m -g users -s /bin/bash teaper #添加teaper用户，用户名你自定义
passwd teaper #为teaper用户设置密码
```
配置新用户的sudo权限  
```
vim /etc/sudoers #编辑配置文件
```
在`root ALL = (ALL) ALL`下添加`teaper ALL = (ALL) ALL`;输入`:wq!`强制保存退出vim  
  
#### 安装声卡驱动  
```bash
pacman -S alsa-utils
```
Arch Linux默认开启了声音支持，默认静音  
可通过`alsamixer`命令＋字母[M] 取消静音  
> 方向键上下调节音量  
> 字母[M]取消静音  
> [Q],[W],[E] 增大 左,右,通道 的音量  
> [Z],[X],[C] 减小 左,右,通道 的音量  
  
#### 安装显卡驱动  
```bash
lspci | grep VGA    #查看显卡型号(例如：Intel Corporation HD Graphics 530)
00:02.0 VGA compatible controller: Intel Corporation HD Graphics 530 (rev 06)   
lspci | grep 3D     #查看独显型号(例如：NVIDIA Corporation GM107M [GeForce GTX 950M])
01:00.0 3D controller: NVIDIA Corporation GM107M [GeForce GTX 950M] (rev a2)    #(rev a2) 表示正在运行，如果是ff则未运行

pacman -S 驱动包名字
```
参照下图根据你的显卡类型，选择相应驱动包  
```bash
xf86-video-resa   #——通用的
xf86-video-intel    #——Intel
xf86-video-nouveau  #——Geforce7
xf86-video-304xx #——Geforce6/7
```
![显卡类型](http://ww1.sinaimg.cn/large/006kWbIogy1g18tjl32sqj30qj09qjsg.jpg)  
如果你还有独显`NVIDIA`可以使用,可以一并安装`pacman -S xf86-video-intel nvidia`  
  
<span style="color:#fc572f;">**双显卡配置**</span>  
参考博客——[【Arch双显卡问题】](https://dclunatic.github.io/2018/09/10/%E7%8B%AC%E6%98%BE/)
  
> 什么情况需要配置双显卡？  
> 系统中有 Intel 集显和 NVIDIA 独显  
> 观察BIOS中的启用的[显卡模式](http://iknow.lenovo.com/detail/dc_102471.html) `Graphic Device` 和上方命令运行结果有没有出现集显/独显出现`(rev ff)`未运行情况。例如我的BIOS中的显卡模式有 `UMA only`（只用集显）和默认 `Discrete`（集显独显分开运行）；上方命令结果也没有出现 ` (rev ff)`显卡未启动的情况， 所以不需要进行下方双显卡配置
  
使用Linux下第三方程序 `Bumblebee` 来实现类似于显卡模式 `ptimus` （同时使用Intel VGA及Nvidia VGA）的功能；它通过 `virtualGL` 或者 `primus` 来实现独显渲染，集显显示的功能；独立显卡在空闲的时候会被禁用掉
  
安装命令`pacman -S bumblebee mesa bbswitch`；其中 `bumblebee` 提供是主要程序实现的包，`mesa` 提供开源的 `OpenGL` 实现，而 `bbswtich` 负责切换显卡  
  
> 如果你的系统是 32 位的，那么你需要启用 `Multilib`，要安装 `lib32-virtualgl` 和 `lib32-nvidia-utils` 或 `lib32-nvidia-340xx-utils` 来和 64 位的对应,具体参照上图  
  
修改配置文件 `/etc/bumblebee/bumblebee.conf`，将 `Driver` 的值设置为 `nvidia`，来让其使用 `nvidia` 驱动，其次将 `PMMethod` 的值设置为 `bbswitch`，让它使用刚刚安装的 `bbswitch` 来进行显卡的切换。修改配置文件 `/etc/modprobe.d/bbswitch.conf`，添加 `options bbswitch load_state=0 unload_state=0 ` 来设置 `bbswitch` 的状态。使用命令 `modprobe bbswitch` 来加载这个模块  
  
将要使用 `Bumblebee` 的用户添加到 `bumblebee` 组中，`gpasswd -a username bumblebee`，并将 `bumblebeed` 服务设为开机启动，`systemctl enable bumblebeed`，启用它 `systemctl start bumblebeed`  
  
#### 安装X窗口系统  
```bash
pacman -S xorg #安装xorg
pacman -S xf86-input-synaptics #安装触摸板驱动
pacman -S ttf-dejavu wqy-microhei   #安装文泉驿米黑字体
pacman -S ttf-dejavu wqy-zenhei wqy-microhei    #安装常用字体
```
  
####  安装Gnome桌面  
```bash
pacman -S gnome  #安装gnome
pacman -S gnome-tweak-tool #gnome桌面优化工具
pacman -S alacarte  #桌面菜单编辑器

systemctl enable gdm    #启用gnome窗口管理器服务
systemctl enable NetworkManager     #启用网络管理
reboot #重启
```
  
### 【桌面美化篇】
至此，Arch已经有图形界面了，不过有些丑，还需要配置一下  
在设置->设备->键盘处配置快捷键   
```
名称                      命令                         快捷键
终端            gnome-terminal          Super+R
系统监视器  gnome-system-monitor    Ctrl+Alt+Backspace
文件管理    nautilus                        Super+E
Synapse     synapse                 Ctrl+空格
```
由于我们每次打开软件都要去开始菜单，所以我这里推荐一个好用的软件`synapse`，他可以直接搜索应用名称，启动`synapse`的快捷键为`Ctrl+空格`，使用下方命令安装<span style="color:#ff0000;">(需要配好[pacman的源](#更新源)才能安装)</span>  
```bash
sudo pacman -S synapse  
```
  
打开系统监视器，右击进程`gnome-shell`选择 > `Change Priority` > `Very High`，将`gnome-shell`进程优先级设为最高<span style="color:#ff0000;">(可以解决桌面闪退到gdm的问题)</span>  
  
由于gnome3右键没有创建文件的快捷菜单，所以需要手动在`~/Templates`目录下创建模板文件，下次我们右击创建文件的时候，类似于从该文件夹下复制文件，所以你可以把一些编程模板也加进去  
```bash
cd ~/Templates  #进入模板文件夹

gedit text #创建简单文本
gedit markdown.md   #Markdown文件
```
除了简单文本文档，最好在其他新建的脚本内加上一行头代码，例如:在 markdown.md 中加入 `# markdown`，js 文件中加入 `var a = 0`，Python 文件中加入 `import os`
  
#### 更新源  
pacman是ArchLinux默认的包管理器，类似于debian系列的apt，pacman会自动安装所需要的依赖包，且下载的软件包存放在`/var/cache/pacman/pkg`目录下，如果在使用`pacman -Syu`滚动更新后，某个软件新版本不好用(例如：google Chrome)，可以在此目录找到旧的安装包，使用`pacman -U`命令安装本地包`示例：sudo pacman -U google-chrome-72.0.3626.121-1-x86_64.pkg.tar.xz`  
```bash
sudo vim /etc/pacman.conf    #编辑源文件
```
在末尾追加以下内容：  
```bash
[archlinuxcn]
SigLevel = Optional TrustAll    #该SigLevel选项/etc/pacman.conf确定安装程序包所需的信任程度
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch

[multilib]
Include = /etc/pacman.d/mirrorlist      #不添加此项无法安装32位依赖程序
```
使修改后的源文件生效  
```bash
sudo pacman -Sy
```
如果由于某种原因，你不希望`pacman -Syu`滚动更新的时候升级某个软件包，可以在`/etc/pacman.conf`中加入如下配置<span style="color:#ff0000;">(用的好可以用来防止滚挂)</span>  
```bash
IgnorePkg = 软件包名字    #软件包名字(例如：google-chrome eclips)
IgnoreGroup = 软件组名字     #软件组名字(例如：gnome linux)
```
<span style="color:#ff0000;">注意：多软件包/软件组可以用空格隔开</span>  
  
如果你使用`pacman -Syu`滚动更新之后，无法正常使用系统<span style="color:#ff0000;">(俗称：滚挂)</span>，你可以进入tty字符界面`(ctrl+alt+f[1~7])`，使用vim查看`/var/log/pacman.log`文件，查看更新日志  
> 例如：滚动更新google-chrome-72.0.3626.121-1-x86_64.pkg.tar.xz后日志如下
  
```bash
[2018-08-08 04:09] [PACMAN] Running 'pacman -Syu'
[2018-08-08 04:09] [PACMAN] synchronizing package lists
[2018-08-08 04:10] [PACMAN] starting full system upgrade
[2018-08-08 04:14] [ALPM] transaction started
[2018-08-08 04:14] [ALPM] upgraded google-chrome (72.0.3626.121-1 -> 73.0.3683.103-1)
```
`Running 'pacman -Syu'`：表示下面的紧接着的日志信息是运行`pacman -Syu`命令之后的日志输出  
`[2018-08-08 04:14]`：是你滚动更新的时间，确保这条日志和你滚挂时间匹配  
`upgraded`：表示更新软件包，如果是安装，则会显示`installed`  
`google-chrome`：这个不用说都知道是软件名字  
`(72.0.3626.121-1 -> 73.0.3683.103-1)`：表示版本从`72.0.3626.121-1`更新为`73.0.3683.103-1`  
  
根据日志信息中的软件和旧版本号，进入`/var/cache/pacman/pkg`目录下使用上面的`sudo pacman -U page1 page2 page3`方式，安装回旧版本的软件包即可  
  
#### 安装缺省的常用网络命令
以前net-tools属于base组，装base时自动就装上了，现在哪个组都不属于了，这些工具需要单独安装  
其中ifconfig、route在net-tools包中；nslookup、dig在dnsutils包中；ftp、telnet等在inetutils包中；ip命令在iproute2包中  
```bash
sudo pacman -S net-tools dnsutils inetutils iproute2
``` 
  
#### 安装AUR软件包    
```bash
sudo pacman -S yaourt    #可以使用aur中的软件，使用方法同pacman一样
sudo pacman -S archlinuxcn-keyring  #安装archlinuxcn-keyring 包以导入 GPG key
```
  
#### 安装Git及SSH  
```bash
sudo pacman -S git #安装git
git config --global user.name "Your Name"   #配置你的git用户名
git config --global user.email "youremail@example.com"      #配置你的git邮箱
git config --list   #查看

sudo pacman -S openssh   #安装ssh服务
ssh-keygen -t rsa -C "youremail@example.com"    #邮箱同上，一路按Enter
cd ~/.ssh   #进入.ssh隐藏文件夹
gedit id_rsa.pub       #打开公钥并复制id_rsa.pub文件中的内容
```
进入你自己的[github](https://github.com) 
进入`Settings` -> `SSH and GPG keys` -> `New SSH key`  
新建一个key，名字随便，把复制的内容粘贴上去  
  
配置一些git命令别名  
```bash
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.br branch

git config --global alias.psm 'push origin master'
git config --global alias.plm 'pull origin master'

git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
```
还有一个好用的git工具[gitflow](https://github.com/nvie/gitflow)  
```bash
sudo pacman -S gitflow-avh   #安装gitflow
```
  
#### Git客户端gitkraken  
```bash
sudo pacman -S gitkraken
```
  
#### 版本控制SVN  
```bash
sudo pacman -S svn  #安装SVN
```
  
#### 安装yay  
```bash
cd /opt
git clone https://aur.archlinux.org/yay.git
cd yay
sudo chmod a＋w /opt/yay
makepkg -si
```
  
#### 下载numix图标  
```bash
yaourt numix-circle
```
选择第一个安装，注意导入PGP KEY选择Y；然后打开`Tweak -> Appearance -> icons`中选择你觉得好看的  
  
#### 安装主题  
```bash
mkdir ~/.themes     #创建主题隐藏文件夹
cd ~/.themes #进入主题文件夹
git clone https://gitlab.com/ZhuHan/GNOME-OSX-II-Theme.git  #克隆主题文件
```
进入`Tweak -> Extensions`中启用`Application menu`；再到`Window Titlebars`选项卡中启用`maximize`和`minimize`以及修改称`Left`按钮  
  
#### 安装Dock状态栏  
```bash
yaourt dash-to-dock
```
选择第一个，安装成功后进入`Tweak`的`Extension`启用`Dash to dock`，如果没有出现请重启电脑
  
#### 安装oh-my-zsh  
```bash
sudo pacman -S zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
右击终端->配置文件首选项，进行颜色配置(我喜欢<span style="color:#FF5F00">#FF5F00</span>)  
  
修改[ZSH主题](https://github.com/robbyrussell/oh-my-zsh/wiki/External-themes)  
```bash
ls ~/.oh-my-zsh/themes   #列出所有主题
sudo gedit ~/.zshrc  #编辑ZSH配置文件
```
修改`ZSH_THEME="mortalscumbag"`为你想要的主题`mortalscumbag.zsh-theme`，不知道有什么主题看上面那个`ZSH主题`链接  
  
有些主题会出现字符乱码，需要下载主题[修补字体](https://github.com/powerline/fonts)  
```bash
git clone https://github.com/powerline/fonts.git --depth=1 
cd fonts    #进入这个字体文件夹
./install.sh    #安装字体
```
**其他主题：**[alien](https://github.com/eendroroy/alien)<span style="color:#ff0000;">(强力推荐)</span>  
```bash
cd ~/.oh-my-zsh/themes  #进入主题文件夹

git clone https://github.com/eendroroy/alien.git
cd alien
git submodule update --init --recursive

gedit ~/.zshrc #编写配置文件
```
在`~/.zshrc`中`source $ZSH/oh-my-zsh.sh`后一行添加`source ~/.oh-my-zsh/themes/alien/alien.zsh`就可以看到效果了<span style="color:#ff0000;">(找不到位置可以Ctrl+F搜source $ZSH/oh-my-zsh.sh)</span>
  
此外,alien 提供了多种不同的配色方案<span style="color:#ff0000;">(默认为蓝色)</span>  
```bash
export ALIEN_THEME="blue"  #蓝色
export ALIEN_THEME="green" #绿色
export ALIEN_THEME="red"    #红色
export ALIEN_THEME="soft"   #淡蓝色
export ALIEN_THEME="gruvbox"    #灰褐色
```
只需要在`~/.zshrc`中的`ZSH_THEME="mortalscumbag"`后一行添加上面提供的任意一行`export ALIEN_THEME="color"`即可  
为了让你的终端支持彩色输出，把`/etc/pacman.conf`中的`#color`的`#`删除  
  
#### 终端透明  
原版的gnome-terminal是不能调透明的，这是因为在早些版本的时候去掉了这个功能，后来Fedora的开发者有把这个透明特性给patch回去了，Arch Linux AUR包里有这个加了patch的版本  
```bash
yaourt -S gnome-terminal-transparency   #一路按y回车
```
安装成功可以在右击终端，在 Preferences 的 color选项卡中拖动透明滑块设置透明度  
  
### 【软件安装篇】  
#### 谷歌浏览器  
```bash
sudo pacman -S google-chrome #直接安装chrome
```
不过安装之后不能使用flash，所以需要安装一下  
```bash
sudo pacman -S flashplugin   #flash插件
```
  
#### 火狐浏览器  
```bash
sudo pacman -S firefox   #安装火狐浏览器
```
  
#### 安装搜狗输入法  
由于搜狗拼音输入法依赖于Fcitx，在安装搜狗拼音输入法之前，需要先行安装Fcitx  
```bash
sudo pacman -S fcitx
sudo pacman -S fcitx-configtool
sudo pacman -S fcitx-gtk2 fcitx-gtk3 fcitx-qt4 fcitx-qt5
 
sudo pacman -S fcitx-sogoupinyin
```
用文本编辑器创建或编辑 `~/.xprofile` 文件;在其末尾添加以下几行:  
```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```
若你使用的桌面环境比较特殊，可能需要在`/etc/environmenet`后方也加入<span style="color:#ff0000;">(保险起见也加上)</span>  
```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```
<span style="color:#ff0000;">常见问题：使用`pacman -Syu`更新之后，`TIM`、`gedit`里无法输入中文</span>  
> 解决方法：首先检查上面的 `~/.xprofile` 和 `/etc/environmenet` 中的配置有没有被修改或删除，如果一切正常，使用 `source /etc/environmenet` 和 `source ~/.xprofile` 使配置文件生效，然后重启电脑
  
#### 互联网小飞机
富强、民主、文明、和谐、自由、平等、公正、法治、爱国、敬业、诚信、友善  
  
先去国外网站买个VCS服务器,这里推荐[Vultr](https://www.vultr.com/?ref=7539377)  
新用户注册成功是在计费页面,说下Vultr的计费规则,它不像阿里云,一买就是年或月,而是按小时计算金额,金额从Vultr账户中扣除,所以我们需要先给账户充值金额,充值方式可以是paypal/支付宝/网银,我们先冲10美元  
我们选择一个Vultr云计算(VC2)中的服务器(推荐洛杉矶/日本/新加坡)  
系统选择CentOS/Ubuntu  
服务器大小选个便宜的$5/mo $0.007/h1 CPU 1024MB内存1000GB带宽  
<span style="color:#ff0000;">注意:1000GB带宽表示流量,用完我们可以把原来主机销毁,重新换个主机,再搭建一遍,不用加钱</span>  
成功之后.会显示系统正在安装,安装成功,点击进入管理界面,里面有基本IP地址,用户名,密码之类的  
  
我们先[ping](http://ping.chinaz.com/)一下ip(例如：167.179.77.127)能不能通，再检测端口[国内](http://coolaf.com/tool/port)开放没有，[国外](https://www.yougetsignal.com/tools/open-ports/)开放没有  
```bash
ping 167.179.77.127 #有连续的输出信息就说明可以通
  
PING 167.179.77.127 (167.179.77.127) 56(84) bytes of data.
64 bytes from 167.179.77.127: icmp_seq=1 ttl=36 time=160 ms
64 bytes from 167.179.77.127: icmp_seq=2 ttl=36 time=250 ms
64 bytes from 167.179.77.127: icmp_seq=3 ttl=36 time=247 ms
64 bytes from 167.179.77.127: icmp_seq=4 ttl=36 time=130 ms
64 bytes from 167.179.77.127: icmp_seq=5 ttl=36 time=241 ms
64 bytes from 167.179.77.127: icmp_seq=6 ttl=36 time=159 ms
```
如果不通就重新创建一个服务器  
现在我们使用本机终端窗口远程ssh连接服务器  
```bash
ssh root@167.179.77.127   #会弹出输入密码,在把管理界面的密码输入进去
```
顺利进入系统之后,就可以进行ssr安装了  
```bash
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh    #安装ssh,会显示红色未安装
```
输入数字1继续安装  
要求输入端口号:不填默认2333  
要求输入密码:不填默认doub.io(建议填下)  
选择加密方式:输入数字10(别的也可以)  
选择协议插件:2(auth_sha1_v4)  
选择是否兼容原版ss客户端:n  
选择混淆插件:1  
进行混淆插件的设置后，会依次提示你对设备数、单线程限速和端口总限速进行设置，默认值是不进行限制，个人使用的话，选择默认即可，一路敲回车键,安装完成会出现配置信息,稍后使用(也可以通过运行./ssr.sh,输入5查看)  
  
安装谷歌BBR加速  
```bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
sudo chmod +x bbr.sh  #给bbr.sh加权限
./bbr.sh
```
执行上面的代码，然后耐心等待，安装成功后重启VPS服务器即可(可以在Vultr网站上重启)    
  
下载对应的客户端软件<span style="color:#ff0000;">(已被作者删库，邮件`erguotou525@gmail.com`联系作者获取对应平台最新的安装包)</span>，当然，我这里也有一些删库前的老版本客户端可以用，需要请联系我邮箱  
* ArchLinux系Linux客户端[electron-ssr-0.2.4.pacman ](https://github.com/erguotou520/electron-ssr/releases)  
* Debian系Linux客户端[electron-ssr-0.2.5.deb](#)  
* 安卓客户端[shadowsocksr.apk](https://github.com/shadowsocksr-backup/shadowsocksr-android/releases)  
* IOS 客户端[shadowrocket.ipa](#)<span style="color:#ff0000;"> (借助PC版pp助手安装本地包方式安装到 iPhone 手机)</span>  
* MAC 客户端[ShadowsocksX-NG-R8.dmg](https://github.com/shadowsocksr-backup/ShadowsocksX-NG/releases)  
* Windows客户端[electron-ssr-0.2.6.exe](https://github.com/shadowsocksr-backup/shadowsocksr-csharp/releases)  
  
```bash
sudo pacman -U electron-ssr-0.2.4.pacman #安装本地包
```
> 然后使用`electron-ssr`命令启动小飞机，如果无法启动，并且报`error while loading shared libraries: libgconf-2.so.4: cannot open shared object file: No such file or directory`的error错误，就使用`pacman -S gconf`命令安装gconf  
  
启动后提示安装SSR，确定安装，然后配置服务器信息，如果没有出现配置界面，使用编辑器编辑`~/.config/electron-ssr/gui-config.json`文件手动设置参数  
<details>
<summary>gui-config.json</summary>

```json
{
	"configs": [
		{
			"enable": true,
			"group": "",
			"id": "D02C9C64178F5BAD5F5009DFACC33907",
			"method": "aes-256-cfb",  #加密方式
			"obfs": "plain",  #混淆方式
			"obfsparam": "",
			"password": "pawwsord",  #密码
			"protocol": "auth_sha1_v4",   #协议
			"protocolparam": "",
			"remarks": "",
			"remarks_base64": "",
			"server": "139.175.195.66",  #服务器
			"server_port": 2334   #远程端口
		}
	],
	"index": 0,
	"enable": true,
	"autoLaunch": false,
	"shareOverLan": false,
	"localPort": 1080,
	"ssrPath": "/home/teaper/.config/electron-ssr/shadowsocksr/shadowsocks",
	"pacPort": 2333,
	"sysProxyMode": 1,  #代理方式(1：PAC代理 2：全局 3：不代理)
	"serverSubscribes": [],
	"httpProxyEnable": true,
	"globalShortcuts": {
		"toggleWindow": {
			"key": "Ctrl+Shift+W",
			"enable": true
		},
		"switchSystemProxy": {
			"key": "",
			"enable": false
		}
	},
	"windowShortcuts": {
		"toggleMenu": {
			"key": "Ctrl+Shift+B",
			"enable": true
		}
	},
	"httpProxyPort": 12333,
	"autoUpdateSubscribes": true,
	"subscribeUpdateInterval": 24
}
```
</details>
  
将`electron-ssr`添加到**开机自启**，这样就不用每次开机都要手动启动它  
```bash
sudo cp electron-ssr.desktop ~/.config/autostart    #将快捷方式复制到自启程序目录
```
最后，分享一个免费获取小飞机节点的网站[SSCAP/SSTAP 小工具/SSR/SS/V2Ray/Vmess/Socks5免费账号](https://m.ssrtool.com/tool/recV3?uri=/m/free_ssr)<span style="color:#ff0000;">(打开网站需要全局代理)</span>  
  
#### 网易云音乐  
```bash
sudo pacman -S netease-cloud-music
```
  
#### 蓝牙配置  
之前我们只安装了网络工具，没有配蓝牙驱动，现在安装一下  
```bash
sudo pacman -S bluez bluez-utils bluez-firmware pulseaudio-bluetooth pavucontrol pulseaudio-alsa #全装
```
> bluez软件包提供蓝牙协议栈  
> bluez-utils软件包提供bluetoothctl工具  
> pulseaudio-bluetooth则为bluez提供了PulseAudio音频服务,若没有安装则蓝牙设备在配对 完成后,连接会失败,提示  
> pavucontrol则提供了pulseaudio的图形化控制界面  
> pulseaudio-alsa(可选)则使pulseaudio和alsa协同使用，之后就可以用alsamixer来管理蓝牙音频了    
  
启动蓝牙服务  
```bash
sudo systemctl enable bluetooth.service
sudo systemctl start bluetooth.service
```
启动pulseaudio服务(使用非root执行此命令)  
```bash
pulseaudio -k                   # 确保没有pulseaudio启动
pulseaudio --start              # 启动pulseaudio服务
```
将用户加入lp用户组  
```bash
usermod -a -G lp $USER    
```
默认情况下，蓝牙仅为 lp 用户组中的用户启用 bnep0 设备。如果想要加入蓝牙系统，需确认已将用户加入该组。可以修改/etc/dbus-1/system.d/bluetooth.conf文件中相应的组配置来实现  
现在就可以使用系统中自带的蓝牙进行连接，连接之后  
  
配置蓝牙  
> 启动bluetoothctl交互命令.可以输入 help 列出所有有效的命令  
> 
> 输入 power on 命令打开控制器电源。默认是关闭的  
> 输入 devices 命令获取要配对设备的 MAC 地址  
> 如果设备未在清单中列出，输入 scan on 命令设置设备发现模式  
> 输入 agent on 命令打开代理  
> 输入 pair $MAC 开始配对(支持 tab 键补全)  
> 如果使用无 PIN 码设备，再次连接可能需要手工认证。输入 trust $MAC 命令  
> 用 connect $MAC 命令建立连接  
  
以下为操作示例
```bash
[teaper@Arch ~]$ bluetoothctl 
Agent registered
[CHG] Device 5C:7E:6A:EF:81:CB RSSI: -52
[CHG] Device 5C:7E:6A:EF:81:CB RSSI: -30
[小爱音箱-9708]# agent KeyboardOnly 
Agent is already registered
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -86
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -77
[小爱音箱-9708]# default-agent 
Default agent request successful
[小爱音箱-9708]# scan on
Discovery started
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -85
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -76
[CHG] Device 5C:7E:6A:EF:81:CB RSSI: -52
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -85
[CHG] Device 5C:7E:6A:EF:81:CB RSSI: -30
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -76
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -86
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -76
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -85
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -76
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -84
[小爱音箱-9708]# pair B8:98:F7:5D:8F:54
Attempting to pair with B8:98:F7:5D:8F:54
[CHG] Device B8:98:F7:5D:8F:54 Connected: yes
Request confirmation
[agent] Confirm passkey 853334 (yes/no): yes
Failed to pair: org.bluez.Error.AuthenticationFailed
[CHG] Device B8:98:F7:5D:8F:54 Connected: no
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -76
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -85
[NEW] Device 4D:87:74:CB:11:18 小爱音箱-9708
[CHG] Device 4D:87:74:CB:11:18 RSSI: -52
[小爱音箱-9708]# connect B8:98:F7:5D:8F:54
Attempting to connect to B8:98:F7:5D:8F:54
Failed to connect: org.bluez.Error.Failed
[CHG] Device 4D:87:74:CB:11:18 RSSI: -30
[CHG] Device B8:98:F7:5D:8F:54 RSSI: -76
[小爱音箱-9708]# 
```
设置自动启动蓝牙  
将 `/etc/bluetooth/main.conf `最后的 `AutoEnable` 值修改为 `true`
```bash
sudo vim /etc/bluetooth/main.conf        #使用root用户修改，：wq!强制保存退出
  
sudo pacman -S ansible  #安装ansible
sudo ansible localhost -m lineinfile -a "path=/etc/bluetooth/main.conf line='AutoEnable=true'" #设置蓝牙自启
```
指定使用蓝牙音频输出  
通过 `pavucontrol `的`Playback`和`Pecording`标签页重定向音频的输入和输出  
  
#### 安装打印机  
```bash
sudo pacman -S cups ghostscript gsfonts  #安装打印机组件
```
如果是hp的打印机，还需要安装`pacman -S hplip hpoj`组件   
```bash
sudo systemctl restart avahi-daemon.service 
sudo systemctl start cups-browsed.service   #启动服务
sudo systemctl enable cups-browsed.service  #开机自启
```
配置打印机：  
方法一：  
在浏览器中打开[http://localhost:631](http://localhost:631)   
选择`Administration` > `Add Printer` > `Add Printer`   
选择`LPD/LPR`主机或打印机 > `Continue`   
然后输入打印机的ip地址，这里是`1.1.1.1`   
然后之后就选择你的打印机型号，名字什么的自己取，设置好后会提示成功  
  
方法二：
在桌面右上角的设置 > Devices > Printers 中添加打印机  
    
#### 安装TIM/QQ
```bash
sudo pacman -S deepin.com.qq.office  #TIM
sudo pacman -S deepin.com.qq.im  #QQ
```
  
#### 安装微信  
```bash
yay -S deepin-wine-wechat  #一路回车
```
  
#### 安装钉钉  
```bash
yay -S dingtalk-electron
```
  
#### 安装电报Telegram  
[Telegram](https://telegram.org/)是一个高度安全无监控的软件，消息支持自毁  
```bash
yay -S telegram-desktop-bin
```
  
#### 安装多线程下载工具
```bash
sudo pacman -S wget
sudo pacman -S axel  
```
  
#### BaiduPCS  
百度网盘shell版，开玩笑的，下载请戳[BaiduPCS-Go-v3.5.6-linux-amd64.zip
](https://github.com/iikira/BaiduPCS-Go/releases)  
提取到本地  
```bash
mv BaiduPCS-Go-v3.5.6-linux-amd64 /opt/
cd /opt/BaiduPCS-Go-v3.5.6-linux-amd64
./BaiduPCS-Go   #终端运行该文件

login -bduss=BDUSS值   #使用BDUSS登录
```
BDUSS值获取方式戳[这里](https://github.com/iikira/BaiduPCS-Go/wiki/%E5%85%B3%E4%BA%8E-%E8%8E%B7%E5%8F%96%E7%99%BE%E5%BA%A6-BDUSS)  
登录成功之后就可以进行下载等操作
```
cd  文件夹名        #切换目录
config set -savedir /目录     #自定义下载目录
d   [文件或目录1]  [文件或目录2]  #下载
exit        #退出BaiduPCS-Go

sudo ln -s /opt/BaiduPCS-Go-v3.5.6-linux-amd64/BaiduPCS-Go /usr/bin/BaiduPCS    #添加到终端命令,输入BaiduPCS即可打开
```
<span style="color:#ff0000;">注意：如果下载提示服务器超时，下载失败，请充值百度网盘超级会员，最近的一个bug，同样下面的百度网盘客户端也不行，下载才几十 KB 每秒
  
#### 百度网盘客户端  
建议还是使用上面的[BaiduPCS](#baidupcs)，感觉更快，毕竟多线程  
```bash
yay -S baidunetdisk #客户端版
```
  
#### 录像/直播 
```bash
sudo pacman -S obs-studio    #OBS
yay -S obs-linuxbrowser-bin #哔哩哔哩弹幕需要这个插件
```
启动后，obs 会根据你电脑性能自动配置出一套默认设置，但是默认设置下的视频清晰度达不到我们的要求，所以需要在此基础上修改一下  
> 进入 file > settings 打开设置面板，点击左边列表的 Output 选项卡  
> 设置 Video Bitrate 比特率为 `5000` <span style="color:#ff0000;">(直播比特率 = (上行KB × 8) < 用户下行 < 平台带宽)</span>  
> Encoder选择带 `NVENC` 的N卡驱动  
> 勾选 `Enable Advanced Encoder Settings` 复选框  
> 点击左边列表的 Video 选项卡,设置`Output(Scaled)Resolution`输出像素为`1920×1080`  
  
<span style="color:#ff0000;">注意：如果OBS无法录制电脑桌面，请在终端使用`sudo obs`命令启动一次</span>
  
#### 哔哩哔哩弹幕库  
[官网](http://bilibili.danmaku.live/#/)直接下载，解压之后运行其中名叫`弹幕库`的`run`脚本即可，唯一差的就一个快捷方式，下载下方图片，重命名为`danmuku.png`，将其移动到解压的文件夹中，并且将文件夹中的`弹幕库.run`重命名为`danmuku`，将整个解压文件夹移动到`/opt`目录  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g4ss20u7yvj30340340sl.jpg)  
  
创建快捷方式  
```bash
sudo gedit danmuku.desktop  #创建快捷方式，内容如下
```
```bash
[Desktop Entry]
Encoding=UTF-8
Name=Bilibili Danmuku
Comment=BiliBili live assistant
Exec=/opt/bilibilidanmuku-linux-x64/danmuku
Icon=/opt/bilibilidanmuku-linux-x64/danmuku.png
Categories=Application;
Version=2.1.1
Type=Application
Terminal=0
```
添加权限  
```bash
sudo chmod a+x danmuku.desktop  #添加运行权限
sudo cp danmuku.desktop /usr/share/applications/  #复制到快捷方式文件夹
```
  
#### Teamviewer远程工具
```bash
yay -S teamviewer
```
启动后如果出现，未就绪，请检查网络连接，没问题的话运行  
```bash
sudo systemctl enable --now teamviewerd
```
  
#### 安装WPS  
```bash
yay -S wps-office
```
安装wps缺失字体  
```bash
sudo pacman -S ttf-wps-fonts
```
  
#### 安装jdk  
```bash
yay -S jdk8     #安装jdk8
yay -S jdk11     #安装jdk11
archlinux-java status   #列出所有jdk
sudo archlinux-java set java-11-jdk #切换默认jdk,记得切回来
```
  
#### 安装xmind  
```bash
yay -S xmind        #一路回车
```
如果出现打不开，并且弹出一个错误弹窗，则编辑`XMind.ini`
```bash
sudo gedit /usr/share/xmind/XMind/XMind.ini
#最后一行--add-modules=java.se.ee修改为
-javaagent:/usr/share/xmind/XMind/XMindCrack.jar
```
保存修改，下载破解jar包[XMindCrack.jar](https://stormxing.oss-cn-beijing.aliyuncs.com/files/XMindCrack.jar)；将其移动到XMind.ini同级目录  
```bash
sudo mv XMindCrack.jar /usr/share/xmind/XMind/
```
打开XMind, 点击帮助——序列号，然后输入以下序列号，邮箱随便填，可以填自己的  
```bash
XAka34A2rVRYJ4XBIU35UZMUEEF64CMMIYZCK2FZZUQNODEKUHGJLFMSLIQMQUCUBX
RENLK6NZL37JXP4PZXQFILMQ2RG5R7G4QNDO3PSOEUBOCDRYSSXZGRARV6MGA33TN2
AMUBHEL4FXMWYTTJDEINJXUAV4BAYKBDCZQWVF3LWYXSDCXY546U3NBGOI3ZPAP2SO
3CSQFNB7VVIY123456789012345
```
  
#### 安装Visual Paradigm社区版  
代替Windows下的Visio，我一般拿来画[UML](http://www.umlchina.com/Tools/Newindex1.htm)图，开发人员常用  
```bash
yay -S visual-paradigm-community
```
如果无法安装，可以进[ArchLinux Wiki](https://aur.archlinux.org/packages/visual-paradigm-community/)查看它的相关问题，发现它已经修改了新地址[https://www.visual-paradigm.com/downloads/vpce/Visual_Paradigm_CE_Linux64_InstallFree.tar.gz](https://www.visual-paradigm.com/downloads/vpce/Visual_Paradigm_CE_Linux64_InstallFree.tar.gz)，点击链接下载`tar.gz`压缩包  
下载成功解压到本地，出现一个`Visual_Paradigm_CE_15_2_20190501_Linux64_InstallFree`文件夹，将它里面的`Visual_Paradigm_CE_15.2`文件夹移动到`/opt`目录
```bash
sudo mv Visual_Paradigm_CE_15.2 /opt        #移动到/opt
cd /opt/Visual_Paradigm_CE_15.2     #进入文件夹
./Visual_Paradigm   #运行启动脚本
```
不出意外是启动了(出意外是Java问题)，会提示激活，不管它，直接跳过  
打开界面之后发现它的语言是英语，修改语言在Window -> Application Options -> Appearance -> User language -> Simplified Chinese，然后Apply，OK即可，重启软件后可以看到效果  
由于我们是手动移动文件方式安装的，所以没有快捷方式，需要手动创建一个  
```bash
sudo gedit visual_paradigm.desktop  #创建快捷方式，内容如下
```
```bash
[Desktop Entry]
Type=Application
Name=Visual Paradigm Community
GenericName=UML design application
Comment=Launch Visual Paradigm Community
Icon=/opt/Visual_Paradigm_CE_15.2/Application/resources/vpuml.ico   #图标
Exec=/opt/Visual_Paradigm_CE_15.2/Visual_Paradigm   #启动脚本
Terminal=false
Categories=Office;
```
附加权限  
```bash
sudo chmod a+x visual_paradigm.desktop  #添加运行权限
sudo cp visual_paradigm.desktop /usr/share/applications/  #复制到快捷方式文件夹
```
![](http://ww1.sinaimg.cn/large/006kWbIoly1g3dnjcohuhj31hc0u0qaa.jpg)
  
#### 安装eclipse  
```bash
yay -S  eclipse-jee     #安装社区版
```
**配置代码提示**     
> 进入 Window > preferences > Keys > Content Assist选项卡  
> 在列表中找到Content Assist，设置Binding为"Alt+/"  
> 设置When 一项为 Ending Text，保存退出    
> [代码提示配置](https://blog.csdn.net/BrotherDong90/article/details/49614027)
  
**安装Spring插件**  
> 需要先查看Eclipse的版本号：Help->About Eclipse  
> 再去Spring[插件官网](http://spring.io/tools/sts/all)复制与Eclipse版本相对应的Update Sites 网址  
> 在Eclipse的Help-->Install New Software..中Add的Work  with地址，名字随便，地址填写上面复制的Update Sites网址  
> 勾选四个带/Spring IDE的复选框,finsh完成,静待安装完成,重启Eclipse  
  
**配置MyBatis的DTD**  
> 下载[mybatis-3-config.dtd](http://mybatis.org/dtd/mybatis-3-config.dtd)和[mybatis-3-mapper.dtd](http://mybatis.org/dtd/mybatis-3-mapper.dtd)文件  
> 然后打开window -> preferences下搜索xml catalog选项卡  
> 点击Add按钮添加dtd文件路径，Key的话，分情况填  
> mybatis-3-config.dtd的Key:`-//mybatis.org//DTD Config 3.0//EN`  
> mybatis-3-mapper.dtd的Key:`-//mybatis.org//DTD Mapper 3.0//EN`  
  
**配置代码主题**  
> 进入[Eclipse主题官网](http://www.eclipsecolorthemes.org/?q=)下载你喜欢的主题的`.epf`文件，将其存放到一个单独的文件夹里  
> 在菜单Windows -> Preference -> General -> Appearance 选择黑色主题(Dark)  
> 点击File -> Import -> General -> Preferences -> Next ;`Browse...`选择`.epf`文件路径，finish完成
  
#### 安装和破解MyEclipse  
Linux离线版下载[myeclipse-ci-2018.12.0-offline-installer-linux.rar](https://www.myeclipsecn.com/download/)  
Windows版+破解文件下载：[https://pan.baidu.com/s/1BwPy91v6Wu1aiF9wvDsvHA](https://pan.baidu.com/s/1BwPy91v6Wu1aiF9wvDsvHA)
提取码：`i8co`  
```bash
sudo pacman -S rar   #先安装rar解压程序
```
右击`myeclipse-ci-2018.12.0-offline-installer-linux.rar`提取到此处，可以得到一个`myeclipse-ci-2018.12.0-offline-installer-linux`文件夹，进入文件夹可以看到一个run文件  
```bash
sudo cp myeclipse-ci-2018.12.0-offline-installer-linux.run /opt #把run文件移动到/opt目录
cd /opt  #进入/opt文件夹
mkdir MyEclipse\ CI     #创建MyEclipes文件夹
sudo chmod a+x myeclipse-ci-2018.12.0-offline-installer-linux.run   #添加运行权限
su #切换root用户
./myeclipse-ci-2018.12.0-offline-installer-linux.run    #运行该文件
```
安装位置选择`/opt/MyEclipse CI`下一步，安装完成  
```bash
cd /opt/MyEclipse\ CI   #进入安装目录
sudo chmod -R 777 ./    #给所有文件加执行权限
```
双击`/opt/MyEclipse CI/myeclipse`文件，进行安装，选择项目地址为`~/myeclipse-workspace`文件夹(手动创建)  
下一步，破解，进入下载的`MyEclipse2018.9.0破解文件/MyEclipse Cracker`文件夹打开终端  
```bash
sudo java -jar cracker.jar  #使用java执行该脚本
```
随便输入一个`Usercode`,下拉菜单选择`BLUE`  
连续点两次`Systeml...`,再点`Active`  
点`Tools -> SaveProperties`,再点`Tools -> ReplaceJarFile`  
点击`ReplaceJarFile`后，需要选择`myeclipse`安装路径下的`plugins`文件夹  
最后点击打开按钮，程序会卡顿一会儿，耐心等待，直到出现`Done`提示，表明破解成功  
复制`MyEclipse2018.9.0破解文件/patch`中所以的内容到`/opt/MyEclipse CI/plugins`，选择全部替换  
打开`myeclipse`，选择`Windows -> Preferences`输入`Subscription`查看是否破解成功  
  
<span style="color:#ff0000;">注意：如果激活失败可以手动复制cracker.jar日志信息中的数据到MyEclipse的Enter License弹窗中，Usercode/LICENSEE对应Your subscriber ID输入框，LICENSE_KEY对应Subscription code输入框，Activate，选择I already have an activation code-previously retrieved单选按钮，Activate New，Skip Email跳过邮箱，Activation Code处输入ACTIVATION_KEY</span>  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g1qhhsxfzij30ln0403yn.jpg)  
  
#### Maven安装及配置  
```bash
yay -S maven
```
检查一下`~/.m2`文件夹是否存在，不存在则执行  
```bash
mvn help:system     #执行该命令的过程中，会生成~/.m2文件夹,并且会从Maven官网下载必要的依赖包
```
复制settings.xml到`.m2`文件夹  
```bash
sudo cp /opt/maven/conf/settings.xml ~/.m2        
sudo gedit ~/.m2/settings.xml     #配置maven仓库
```
配置信息如下：  
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
  
#### Tomcat安装及配置  
下载[apache-tomcat-9.0.14.tar.gz](https://tomcat.apache.org/download-90.cgi)  
找到压缩包右击提取到此处  
```bash
sudo chmod a+x apache-tomcat-9.0.16          #添加权限
sudo mv apache-tomcat-9.0.16 /opt
cd /opt/apache-tomcat-9.0.16/bin/
sudo chmod 777 *.sh  #给所有.sh文件加777权限(解决Idea中Tomcat权限错误)
sudo gedit /etc/profile     #配置环境变量

export TOMCAT_HOME=/opt/apache-tomcat-9.0.16
export PATH=$TOMCAT_HOME/bin:$PATH

source /etc/profile   #使环境变量生效
sudo /opt/apache-tomcat-9.0.16/bin/startup.sh   #启动tomcat测试一下
```
测试端口：[ http://localhost:8080/ ]( http://localhost:8080/ )  
  
#### 安装Redis  
```bash
yay -S redis    #安装redis
```
Redis配置文件位于`/etc/redis.conf`  
启动redis服务  
```bash
sudo systemctl start redis.service  #启动
sudo systemctl enable redis.service  #开机自启
```
使用`redis-server`启动试试  
  
#### 安装Docker容器  
[Arch Linux Docker](https://wiki.archlinux.org/index.php/Docker_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))提供了两个版本，二选一喽（我选一）  
```bash
sudo pacman -S docker    #稳定版
yaourt -S docker-git    #开发版
```
启动Docker服务  
```bash
sudo systemctl start docker.service
sudo systemctl enable docker.service
```
安装完成后，运行下面的命令，验证是否安装成功  
```bash
sudo docker version     #由于还没把docker用户加入用户组，所以运行docker命令需要加sudo
#或者
sudo docker info
```
`Docker`需要用户具有`sudo`权限，为了避免每次命令都输入`sudo`，可以把用户加入`Docker`用户组(系统重启后生效)  
```bash
sudo usermod -aG docker $USER   #自动创建docker用户并加入到用户组
cat /etc/group  #查看文件中有没有出现docker
```
列出本机的镜像  
```bash
docker image ls     #列出本机的所有image文件
docker images       #列出本机的所有image文件 (等同于docker image ls)
```
想设置你自己的存储驱动选项和`docker`源，编辑 `/etc/docker/daemon.json` (如果不存在就自己创建)  
```
sudo gedit /etc/docker/daemon.json      #创建daemon.json文件
#内容如下
{
"storage-driver": "overlay2",   #存储驱动选项
"registry-mirrors": ["http://hub-mirror.c.163.com"]    #网易163源
}
```
其他源如下：  
> 阿里源：https://pee6w651.mirror.aliyuncs.com  
> 网易163源：http://hub-mirror.c.163.com  
> docker中国区源：https://registry.docker-cn.com  
> 中国科技大学：https://docker.mirrors.ustc.edu.cn  
  
修改重新启动`docker`服务  
```bash
systemctl restart docker
```
**测试docker**   
从远程仓库[docker hub](https://hub.docker.com/)`pull`一个镜像到本地仓库`/var/lib/docker`，以[hello-world](https://hub.docker.com/_/hello-world)为例  
```bash
docker pull hello-world     #pull一个镜像
docker images     #查看本地镜像
docker [container] run hello-world    #运行hello-world镜像(本地仓库没有镜像，会自动运行远程仓库中的)
```
`hello-world`镜像运行之后输出一大段文本，然后进程会自动停止，有的镜像不会自动停止(例如：`Ubuntu`系统`ubuntu bash`)，这时候就需要手动杀死进程`docker container kill <CONTAINER ID>`  
```bash
docker run -it ubuntu bash  #使用bash运行ubuntu镜像
```
再打开一个终端窗口，查看容器运行情况  
```bash
docker container ls     #列出本机正在运行的容器
docker ps   #列出本机正在运行的容器，等效于 docker container ls
docker container ls --all     #列出本机所有容器，包括终止运行的容器
docker ps --all   ##列出本机所有容器，包括终止运行的容器，等效于 docker container ls --all
```
可以看到以下内容  
```bash
#集装箱ID           图像                命令                   创建               状态                端口                名称
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
dadf7d24b756        ubuntu              "bash"              6 seconds ago       Up 5 seconds                            hungry_stallman
```
手动杀死`ubuntu`进程  
```bash
docker [container] kill dadf7d24b756    #dadf7d24b756是上面查出的CONTAINER ID  
```
杀死进程，容器还在，如果容器也不要了，可以删除  
```bash
docker container ls --all     #列出本机所有容器，包括终止运行的容器
docker [container] rm <CONTAINER ID>...      #删除指定容器
```
容器是运行镜像之后产生的，你可以理解为镜像是个压缩包，容器就是解压出来的文件夹，如果还要把本地镜像也删除，可以  
```bash
docker image rm <REPOSITORY>...     #根据镜像名称删除image文件
docker rmi <IMAGE ID>...     #根据镜像id删除image文件
```
<span style="color:#ff0000;">注意：删除镜像之前，必须把该镜像的所有容器删除（包括正在运行的容器和停止使用的容器）才能删除镜像，否则会报错</span>  
  
#### 安装MySQL/MariaDB  
Archlinux的MySQL被称为MariaDB  
```bash
yay -S mariadb      #安装mariadb及其依赖包
```
根据shell提示初始化数据目录  
```bash
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```
启动MariaDB  
```bash
sudo systemctl start mysqld
```
为root用户设置一个新密码  
```bash
mysqladmin -u root password '123'    #设置root密码为123
```
尝试登录以下  
```bash
mysql -u root -p123
```
如果想要`MariaDb`开机自动启动，那么就运行以下命令  
```bash
sudo systemctl enable mysqld
```
  
#### 使用Docker安装MySQL8.0  
前面说了`MariaDb`相当于`MySQL5.7`以下版本，如果我们要使用原版`MySQL8.0`，Arch Linux是不可以直接安装的  
```bash
docker pull mysql:8.0   #拉取MySQL8.0镜像
docker run -h "mysqlhost" --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123 --restart=always -d mysql:8.0    #启动镜像生成mysql容器
```
启动镜像命令也可以参照下方给出参数进行适当调整，如果3306端口被占用，可以使用`netstat -tunlp | grep 3306`查看端口情况
> -h "mysqlhost"：容器主机名  
> --name mysql：容器名  
> -p 3306:3306：端口映射  
> -e MYSQL_ROOT_PASSWORD=123：设置root密码为123  
> -d mysql:8.0：指定镜像  
> --restart=always：docker服务启动时，自动启动容器，并且当容器停止时，尝试重启容器<span style="color:#ff0000;">(可选)</span>  
> -v /usr:/var/lib/mysql：初始化数据到`/var/lib/mysql`目录<span style="color:#ff0000;">(可选)</span>  
> --character-set-server=utf8mb4：设置mysql默认编码为utf8mb4 <span style="color:#ff0000;">(mysql8.0默认编码就是utf8mb4，所以无需设置)</span>  
> --collation-server=utf8mb4_unicode_ci：设置mysql排序编码为utf8mb4_unicode_ci<span style="color:#ff0000;">(可选)</span>  
  
```bash
docker exec -it mysql bash     #进入容器主机
```
接下来就和本地终端使用`mysql`一样了，不过`mysql8.0`的用户的加密方式变成了`caching_sha2_password`，使用`Navicat`和`DBeaver`这样的第三方数据库管理软件会无法连接，需要修改成`mysql_native_password`    
```bash
root@mysqlhost:/# mysql -u root -p123       #使用密码登录MySQL8.0
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.16 MySQL Community Server - GPL

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql;   #选择mysql数据表
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select host,user,plugin from user;   #查询本地/远程用户及其加密方式(%表示远程用户，localhost表示本地用户)
+-----------+------------------+-----------------------+
| host      | user             | plugin                |
+-----------+------------------+-----------------------+
| %         | root             | caching_sha2_password |
| localhost | mysql.infoschema | caching_sha2_password |
| localhost | mysql.session    | caching_sha2_password |
| localhost | mysql.sys        | caching_sha2_password |
| localhost | root             | caching_sha2_password |
+-----------+------------------+-----------------------+
5 rows in set (0.00 sec)

mysql> 
mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123';     #修改远程root用户的加密方式为mysql_native_password，并且新密码改为123 (当然，出于数据库安全考虑，我们一般都不会把root用户进行远程登录，而是创建其他用户并分配少量权限，这里我自己用就随便任性一下了)
Query OK, 0 rows affected (0.04 sec)

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123';   #修改本地root用户的加密方式为mysql_native_password
Query OK, 0 rows affected (0.04 sec)

mysql> select host,user,plugin from user;   #再次查询本地/远程用户及其加密方式
+-----------+------------------+-----------------------+
| host      | user             | plugin                |
+-----------+------------------+-----------------------+
| %         | root             | mysql_native_password |
| localhost | mysql.infoschema | caching_sha2_password |
| localhost | mysql.session    | caching_sha2_password |
| localhost | mysql.sys        | caching_sha2_password |
| localhost | root             | mysql_native_password |
+-----------+------------------+-----------------------+
5 rows in set (0.00 sec)

mysql> flush privileges;    #刷新权限
Query OK, 0 rows affected (0.03 sec)

mysql> 
```
这样一来，我们就可以使用`DBeaver`登录`MySQL8.0`啦  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g319dovj3zj30ku0m3wga.jpg)
  
#### 数据库管理神器DataGrip  
`jetbrains`出品的数据库软件，破解同[安装IntelliJ IDEA](#安装intellij-idea)
```bash
yay -S datagrip
```
  
#### 数据库管理神器Dbeaver  
Dbeaver是一个免费的多平台数据库管理工具  
它支持流行的数据库，如MySQL，MariaDB，PostgreSQL，SQLite，Oracle  
```bash
yay -S dbeaver dbeaver-plugin-office dbeaver-plugin-svg-format
```
打开软件会出现引导，类型选择`MariaDB`，它会在线下载驱动包，然后填写数据库名`mysql`，用户名及密码即可登录  
  
如果设置软件中文失败，可以手动在`/usr/lib/dbeaver/dbeaver.ini`追加以下内容：  
```bash
-Duser.language=zh
```
  
#### 数据库管理神器Navicat Premium  
**安装**  
Linux下的Navica运行是需要wine的，因为之前安装过deepin-tim，wine已经默认作为依赖包安装好了，除此之外，Navicat安装包中也默认封装了一套wine组件  

找到Linux的Navicat Premium，随便找个下载线路，点击[下载](https://www.navicat.com/en/products)，在Chrome中的下载列表复制下载文件的下载路径`(例如：http://download3.navicat.com/download/navicat121_premium_en_x64.tar.gz)`  
```bash
axel -n 10 http://download3.navicat.com/download/navicat121_premium_en_x64.tar.gz  #使用axel多线程(10线程)下载Navicat安装包
```
右击`navicat121_premium_en_x64.tar.gz`提取到本地  
```bash
sudo mv navicat121_premium_en_x64 /opt
cd /opt/navicat121_premium_en_x64/
gedit start_navicat  #编辑start_navicat文件，解决中文乱码
```
把`export LANG="en_US.UTF-8"` 改为 `export LANG="zh_CN.UTF-8"`可以解决中文系统下乱码问题
```bash
./start_navicat #开始安装，会提示安装wine Mono，点安装
```
打开之后，点左边Trial试用，右边是注册；这时候发现字体好小，进工具 > 选项改字体；找张[图片](https://i.loli.net/2019/03/20/5c9162665551c.png)做LOGO；修改图片名称为icon_navicat.png  
```bash
sudo mv icon_navicat.png /opt/navicat121_premium_en_x64/
gedit navicat.desktop   #创建快捷方式
```
`navicat.desktop`中填入一下内容(注意版本信息相应替换)
```
[Desktop Entry]
Encoding=UTF-8
Name=navicat Premium
Comment=The Smarter Way to manage dadabase
Exec=/bin/sh "/opt/navicat121_premium_en_x64/start_navicat"
Icon=/opt/navicat121_premium_en_x64/icon_navicat.png
Categories=Application;Database;MySQL;navicat
Version=1.0
Type=Application
Terminal=0
```
附加运行权限  
```bash
sudo chmod a+x navicat.desktop  #添加运行权限
sudo mv navicat.desktop /usr/share/applications/  #复制到快捷方式文件夹
```
  
**破解**  
破解方法参考——[【Ubuntu的安装的Navicat正版永久使用方法】](https://yq.aliyun.com/ziliao/5468)  
  
事先约定下环境，在你的文件夹下确认是否拥有这些文件  
> navicat配置文件夹路径：~/.navicat64  
> 原始文件user.reg：~/.navicat64/user.reg #navicat读取配置使用
> 备份文件user_backup.reg: ~/.navicat64/user_backup.reg     #稍后我们自己创建
  
使用创建好的快捷图标打开navicat，设置数据库连接信息,后面要记录信息并存放到创建的备份文件中，到时候修改数据库连接信息就会比较麻烦  
```bash
cd ~/.navicat64/ && ls -a  #进入文件夹
gedit user.reg  #复制里面的内容
gedit user_backup.reg #新建user_backup.reg文件，把user.reg中的内容粘贴进去
```
把每个`[]`后的`数字`和重复性的摸块`(以空行为分隔，重复性的小段代码块)`这样的重复代码块全部删除,保存  
最终效果类似于如下示例：  
<details>
<summary>user_backup.reg文件</summary>
  
```bash
WINE REGISTRY Version 2
;; All keys relative to \\User\\S-1-5-21-0-0-0-1000

#arch=win64

[AppEvents\\Schemes\\Apps\\Explorer\\Navigating\\.Current] 
#time=1d4b06f4ffe9cec
@=""

[Control Panel\\Accessibility\\Blind Access] 
#time=1d4b06cc6848bf4
"On"="0"

[Control Panel\\Accessibility\\Keyboard Preference] 
#time=1d4b06cc684826c
"On"="1"

[Control Panel\\Accessibility\\ShowSounds] 
#time=1d4b06cc6848df2
"On"="0"

[Control Panel\\Colors] 
#time=1d4b06cc68cc918

[Control Panel\\Desktop] 
#time=1d4b06fa2eb5364
"ActiveWndTrackTimeout"=dword:00000000
"BlockSendInputResets"="0"
"CaretWidth"=dword:00000001
"ClickLockTime"=dword:000004b0
"DoubleClickHeight"="4"
"DoubleClickWidth"="4"
"DragFullWindows"="0"
"DragHeight"="4"
"DragWidth"="4"
"FocusBorderHeight"=dword:00000001
"FocusBorderWidth"=dword:00000001
"FontSmoothing"="2"
"FontSmoothingGamma"=dword:00000578
"FontSmoothingOrientation"=dword:00000001
"FontSmoothingType"=dword:00000002
"ForegroundFlashCount"=dword:00000003
"ForegroundLockTimeout"=dword:00000000
"IconTitleWrap"="1"
"LowPowerActive"="0"
"MenuShowDelay"="400"
"UserPreferencesMask"=hex:30,00,00,80,10,00,00,00
"Wallpaper"=""
"WheelScrollChars"="3"
"WheelScrollLines"="3"

[Control Panel\\International] 
#time=1d4b06f8b78a9b6
"iCalendarType"="1"
"iCountry"="86"
"iCurrDigits"="2"
"iCurrency"="0"
"iDate"="2"
"iDigits"="2"
"iFirstDayOfWeek"="6"
"iFirstWeekOfYear"="0"
"iLDate"="2"
"iLZero"="0"
"iMeasure"="0"
"iNegCurr"="2"
"iNegNumber"="1"
"iPaperSize"="9"
"iTime"="1"
"iTimePrefix"="1"
"iTLZero"="0"
"LC_CTYPE"="00000804"
"LC_MEASUREMENT"="00000804"
"LC_MONETARY"="00000804"
"LC_NUMERIC"="00000804"
"LC_PAPER"="00000804"
"LC_TELEPHONE"="00000804"
"LC_TIME"="00000804"
"Locale"="00000804"
"Numshape"="1"
"s1159"="\x4e0a\x5348"
"s2359"="\x4e0b\x5348"
"sCountry"="People's Republic of China"
"sCurrency"="\xffe5"
"sDate"="-"
"sDecimal"="."
"sGrouping"="3;0"
"sLanguage"="CHS"
"sList"=","
"sLongDate"="yyyy'\x5e74'M'\x6708'd'\x65e5'"
"sMonDecimalSep"="."
"sMonGrouping"="3;0"
"sMonThousandSep"=","
"sNativeDigits"="0123456789"
"sNegativeSign"="-"
"sPositiveSign"=""
"sShortDate"="yyyy-M-d"
"sThousand"=","
"sTime"=":"
"sTimeFormat"="H:mm:ss"
"sYearMonth"="yyyy'\x5e74'M'\x6708'"

[Control Panel\\Keyboard] 
#time=1d4b06cc684830c
"KeyboardDelay"="1"
"KeyboardSpeed"="31"

[Control Panel\\Mouse] 
#time=1d4b06cc6848fa0
"ActiveWindowTracking"=dword:00000000
"DoubleClickHeight"="4"
"DoubleClickSpeed"="500"
"DoubleClickWidth"="4"
"MouseHoverHeight"="4"
"MouseHoverTime"="400"
"MouseHoverWidth"="4"
"MouseSensitivity"="10"
"MouseSpeed"="1"
"MouseThreshold1"="6"
"MouseThreshold2"="10"
"SnapToDefaultButton"="0"
"SwapMouseButtons"="0"

[Control Panel\\Sound] 
#time=1d4b06cc6846ed0
"Beep"="Yes"

[Environment] 
#time=1d4b06cc7108bfe
"TEMP"="C:\\users\\teaper\\Temp"
"TMP"="C:\\users\\teaper\\Temp"

[Keyboard Layout\\Preload] 
#time=1d4b06cc68b598e
"1"="00000409"

[Software\\PremiumSoft\\Navicat\\Servers\\MySQL] 
#time=1d4b47a4056b0cc
"AllowInvalidHostName"=dword:00000000
"AltHTTPProxyUserName"=""
"AltHTTPUserName"=""
"AltSSHUserName"=""
"AltUserName"=""
"AskPassword"="false"
"AutoConnect"=dword:00000000
"CACert"=""
"Cipher"=""
"ClientCert"=""
"ClientKey"=""
"ClientKeyPassword"=""
"Codepage"=dword:0000fde9
"Host"="localhost"
"HTTP_Authen"=dword:00000000
"HTTP_CACert"=""
"HTTP_CertAuth"=dword:00000000
"HTTP_ClientCert"=""
"HTTP_ClientKey"=""
"HTTP_EncodeBase64"=dword:00000000
"HTTP_Passphrase"=""
"HTTP_Password"=""
"HTTP_Proxy"=dword:00000000
"HTTP_ProxyHost"=""
"HTTP_ProxyPassword"=""
"HTTP_ProxyPort"=dword:00001f90
"HTTP_ProxyUsername"=""
"HTTP_URL"=""
"HTTP_Username"=""
"HttpProxySavePassword"=dword:00000001
"HttpSavePassword"=dword:00000001
"NamedPipeSocket"=""
"NSYDirtyFlag"=dword:00000000
"NSYHash"=""
"NSYID"=""
"NSYProjectUUID"=""
"NSYSyncHTTPProxyUserName"=dword:00000001
"NSYSyncHTTPUserName"=dword:00000001
"NSYSyncSSHUserName"=dword:00000001
"NSYSyncUserName"=dword:00000001
"PGSSLCRL"=""
"PGSSLMode"="smRequire"
"PingInterval"=dword:000000f0
"Port"=dword:00000cea
"Pwd"="15057D7BA390"
"QuerySavePath"="C:\\users\\teaper\\\x6211\x7684\x6587\x6863\\Navicat\\MySQL\\Servers\\MySQL"
"RootCert"=""
"SaveClientKeyPassword"=dword:00000000
"ServerVersion"=dword:00018729
"ServerVersionStr"="10.1.37-MariaDB"
"ServiceProvider"="spDefault"
"SessionLimit"=dword:00000000
"SSH_AuthenMethod"="saPassword"
"SSH_Host"=""
"SSH_Passphrase"=""
"SSH_Password"=""
"SSH_Port"=dword:00000016
"SSH_PrivateKey"=""
"SSH_SavePassphrase"=dword:00000001
"SSH_SavePassword"=dword:00000001
"SSH_UserName"=""
"UseAdvSettings"="false"
"UseCharacterSet"=dword:00000001
"UseCompression"=dword:00000000
"UseHTTP"=dword:00000000
"UseNamedPipe"=dword:00000000
"UsePingInterval"=dword:00000000
"UserName"="root"
"UseSSH"=dword:00000000
"UseSSL"=dword:00000000
"UseSSLAuthen"=dword:00000000
"VerifyCACert"=dword:00000000
"WeakCertValidation"=dword:00000000

[Software\\PremiumSoft\\NavicatPremium] 
#time=1d4b479b4fb125c
"AlreadyShowNavicateV121WelcomeScreen"=dword:00000001
"FirstFileAssociation"=dword:00000001

[Software\\Wine\\Fonts] 
#time=1d4b479b3066690
"Codepages"="936,936"
"LogPixels"=dword:00000060

[Software\\Wine\\Fonts\\External Fonts] 
#time=1d4b479b3066690

[Software\\Wine\\MenuFiles] 
#time=1d4b479b40c2732
```
</details>
  
创建开机自动替换文件的脚本  
```bash
gedit reset_navicat.sh
```
内容如下,`teaper`是我的系统用户名,你自己替换为你自己的  
```bash
#!/bin/sh
#！每次开机删除user.reg
rm /home/teaper/.navicat64/user.reg
#！将备份脚本中的内容换成navicat读取的配置
cp /home/teaper/.navicat64/user_backup.reg /home/teaper/.navicat64/user.reg
```
给文件添加权限  
```bash
sudo chmod a+x reset_navicat.sh
/home/teaper/.navicat64/reset_navicat.sh    #24小时后运行一下试试有没有推迟试用时间
```
以后增加数据库链接，只需在user.reg中把新增加的数据库链接添加到user_backup.reg文件中即可  
  
**添加开机自启服务**  
如果24小时候运行`reset_navicat.sh`没问题，就可以把`reset_navicat.sh`添加到开机自启的service中  
```bash
cd /etc/systemd/system  #进入文件夹
sudo gedit autonavicat.service #创建autonavicat.service文件
```
`autonavicat.service`中内容如下  
```bash
[Unit]
Description=autonavicat 
[Service]
ExecStart=/home/teaper/.navicat64/reset_navicat.sh
[Install]
WantedBy=multi-user.target
```
添加权限  
```bash
sudo chmod 644 autonavicat.service
```
设置开机自启  
```bash
sudo systemctl start autonavicat.service  #启动一下试试
sudo systemctl enable autonavicat.service #添加到开机自启服务
```
  
#### 使用Docker安装oracle 11g数据库  
安装oracle数据库之前，需要先[安装Docker容器](#安装docker容器)和[数据库管理神器Dbeaver](#数据库管理神器dbeaver)客户端  
```bash
docker search oracle    #搜索oracle相关的镜像，找到alexeiled/docker-oracle-xe-11g 
docker pull alexeiled/docker-oracle-xe-11g      #拉取镜像
docker images   #查看镜像文件
#docker run -d --shm-size=2g -p 1521:1521 -p 8080:8080 alexeiled/docker-oracle-xe-11g  #启动镜像(官方方式)
docker run -h "oraclehost" --name "oracle" -d -p 1521:1521 alexeiled/docker-oracle-xe-11g  #启动镜像
```
`docker run -d --shm-size=2g -p 1521:1521 -p 8080:8080 alexeiled/docker-oracle-xe-11g`  是官方提供的启动镜像方式，容器NAMES是自动生成的，bash系统名是CONTAINER ID，特别不美观，所以使用` -h "oraclehost" --name "oracle" `方式启动镜像  
  
这里需要说明一下上面的参数：  
> -h "oraclehost"：指定容器的hostname为oracle  
> --name "oracle"：将容器命名为oracle  
> -d：在后台运行  
> -p: 端口映射，格式为：主机(宿主)端口:容器端口  
  
```bash
docker ps   #查看正在运行的容器
#-----------------结果如下------------------------
CONTAINER ID        IMAGE                            COMMAND                  CREATED             STATUS              PORTS                              NAMES
58d94af5df82        alexeiled/docker-oracle-xe-11g   "/bin/sh -c /start.sh"   9 seconds ago       Up 8 seconds        0.0.0.0:1521->1521/tcp, 8080/tcp   oracle
#--------------------------------------------------
#docker rename brave_perlman oracle    #官方方式启动镜像，修改查出的NAMES容器名字brave_perlman为oracle(这里不需要修改)
docker exec -it 58d94af5df82 bash   #在bash终端中进入容器：58d94af5df82为容器CONTAINER ID，也可使用容器名oracle
```
不出意外的话，我们可以看到是一个容器主机，以`root`用户登录的，主机名为`oraclehost`，我们可以使用`ps aux`命令查看容器中启动的进程  
  
接下来就直接使用内部虚拟`bash`登录`oracle`数据库  
```bash
su oracle   #容器中切换为oracle用户

oracle@oraclehost:/$ sqlplus system/oracle      #以普通用户登录，system是用户名，oracle是容器默认密码
SQL*Plus: Release 11.2.0.2.0 Production on Sun May 12 13:37:11 2019
Copyright (c) 1982, 2011, Oracle.  All rights reserved.

ERROR:
ORA-28002: the password will expire within 7 days   #提示密码有效期7天


Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production
SQL> alter user system identified by 123;      #修改system用户的密码为123                     
User altered.
SQL> 
```
修改成功，使用`exit`退出`oracle`，然后重新使用`sqlplus system/123`命令登录试试  
  
确定没问题，使用`exit`退出`oracle`，再使用`exit`退出容器主机，回到本机  
我们暂时不需要使用oracle，所以先关闭oracle容器，需要的时候再启动  
```bash
docker ps   #查看正在运行的容器      
#--------------------------效果如下-----------------------------
CONTAINER ID        IMAGE                            COMMAND                  CREATED             STATUS              PORTS                              NAMES
58d94af5df82        alexeiled/docker-oracle-xe-11g   "/bin/sh -c /start.sh"   15 minutes ago      Up 15 minutes       0.0.0.0:1521->1521/tcp, 8080/tcp   oracle
----------------------------------------------------------------
docker stop 58d94af5df82    #关闭CONTAINER ID为58d94af5df82的Oracle容器
```
**下次使用oracle数据库**  
```bash
docker ps -all      #第一步：查看已关闭的容器
docker start oracle   #第二步：启动NAMES为oracle的容器 (也可以把NAMES换成CONTAINER ID)
docker exec -it oracle bash   #第三步：在bash终端中进入容器
su oracle   #第四步：切换容器用户为oracle
sqlplus system/123  #第五步：使用123密码登录system用户的Oracle数据库
```
**客户端软件连接Oracle数据库**  
点击`DBeaver`左边的`新建连接`按钮，选择`Oracle`数据库，填写连接信息如图所示  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2yuh7blg8j30kt0m0q4r.jpg)  
单击`编辑驱动设置`按钮，单击窗口中的网址，进入oracl官网下载[ojdbc6.jar](https://www.oracle.com/technetwork/cn/database/enterprise-edition/jdbc-112010-094555-zhs.html)驱动包，单击`添加文件`按钮将其导入，导入成功点`ok`  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2yuh794a3j30dw0kogna.jpg)  
最后就是测试连接信息  
<span style="color:#ff0000;">注意：如果测试连接报`Locale not recognized`错,手动在`/usr/lib/dbeaver/dbeaver.ini`追加以下内容：</span>
```bash
-Duser.language=zh 或 -Duser.language=en
```
  
#### 使用Docker安装SqlServer数据库  
教程参照官方手册[【快速入门：使用 Docker 运行 SQL Server 容器映像】](https://docs.microsoft.com/zh-cn/sql/linux/quickstart-install-connect-docker?view=sql-server-2017&pivots=cs1-bash)，使用最新版的[mssql server linux](https://hub.docker.com/_/microsoft-mssql-server)镜像  
```bash
docker pull mcr.microsoft.com/mssql/server  #pull镜像
```
如果想指定`pull`某个版本的镜像，例如：`2017-latest-ubuntu`或`2019-CTP2.2-ubuntu`，可在后面追加`:版本`，不指定则选择最新版本<span style="color:#ff0000;">(我是最新版)</span>  
```bash
docker pull mcr.microsoft.com/mssql/server:2017-latest-ubuntu   #2017-latest-ubuntu版
docker pull mcr.microsoft.com/mssql/server:2019-CTP2.2-ubuntu   #2019-CTP2.2-ubuntu版
```
使用`SQL Server 2019`的最新更新启动mssql-server实例，官方提供了一个`docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest`命令  
  
其中`:2017-latest`我不要<span style="color:#ff0000;">(如果是安装指定版本的需要)</span>，`yourStrong(!)Password`是数据库`SA`用户的密码，该密码必须符合8位以上大小写字母数字组合，这里我们用密码`Zxcvbnm%`将其替换，然后参照[使用Docker安装oracle 11g数据库](#使用docker安装oracle-11g数据库)，给容器初始名字为`MSSQL_1433`，主机名为`mssqlhost`，修改后命令如下：
```bash
docker run -h "mssqlhost" --name MSSQL_1433 -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Zxcvbnm%' -p 1433:1433 -d mcr.microsoft.com/mssql/server
```
查看正在运行的容器，`NAMES`自动变成我们指定的`MSSQL_1433`了  
```bash
docker ps   #查看正在运行的容器
---------------------------------运行结果如下----------------------------
CONTAINER ID        IMAGE                            COMMAND                  CREATED             STATUS              PORTS                    NAMES
9054ca89f526        mcr.microsoft.com/mssql/server   "/opt/mssql/bin/sqls…"   28 seconds ago      Up 27 seconds       0.0.0.0:1433->1433/tcp   MSSQL_1433
------------------------------------------------------------------------
docker exec -it MSSQL_1433 bash        #登录容器
```
我们可以看到`root@mssqlhost:/#`这样一个容器主机，其中容器主机名自动变为指定的`mssqlhost`，接下来使用主机内部的`sqlcmd`工具登录`SQL Server`，方法是在容器主机上使用以下命令  
```bash
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Zxcvbnm%'    #使用SA用户登录SQLserve数据库
```
你也可以使用官方的命令，把上面两步(登录容器和登录SQLServer)合并一步到位  
```bash
docker exec -it MSSQL_1433 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'Zxcvbnm%'
```
使用`DBeaver`新建`SQL Server`登录连接，它会自动下载驱动包，登录信息如下 
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2zlhiyhfwj30i90k7abm.jpg)  
  
确定没问题，使用`exit`直接退回本机，
我们暂时不需要使用`SqlServer`，所以先关闭`MSSQL_1433`容器，需要的时候再启动  
```bash
docker stop MSSQL_1433    #关闭MSSQL_1433容器
```
**下次使用oracle数据库**  
```bash
docker ps -all      #第一步：查看已关闭的MSSQL_1433容器
docker start MSSQL_1433   #第二步：启动MSSQL_1433容器
docker exec -it MSSQL_1433 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'Zxcvbnm%'   #第三步：一步到位登录SQL Server数据库
```
  
#### 安装IntelliJ IDEA  
```bash
yay -S intellij-idea-ultimate-edition
```
> ---------------------------------安装配置-------------------------------------  
> Do not import settings　　　ok  
> 拉到最下面　　　Accept  
> Send Usage Statististics  
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
  
破解方法，在`/etc/hosts`文件中加入`0.0.0.0 account.jetbrains.com`和`0.0.0.0 www.jetbrains.com`，在idea中加入下方激活码  
```
56ZS5PQ1RF-eyJsaWNlbnNlSWQiOiI1NlpTNVBRMVJGIiwibGljZW5zZWVOYW1lIjoi5q2j54mI5o6I5p2DIC4iLCJhc3NpZ25lZU5hbWUiOiIiLCJhc3NpZ25lZUVtYWlsIjoiIiwibGljZW5zZVJlc3RyaWN0aW9uIjoiRm9yIGVkdWNhdGlvbmFsIHVzZSBvbmx5IiwiY2hlY2tDb25jdXJyZW50VXNlIjpmYWxzZSwicHJvZHVjdHMiOlt7ImNvZGUiOiJJSSIsInBhaWRVcFRvIjoiMjAyMC0wMy0xMCJ9LHsiY29kZSI6IkFDIiwicGFpZFVwVG8iOiIyMDIwLTAzLTEwIn0seyJjb2RlIjoiRFBOIiwicGFpZFVwVG8iOiIyMDIwLTAzLTEwIn0seyJjb2RlIjoiUFMiLCJwYWlkVXBUbyI6IjIwMjAtMDMtMTAifSx7ImNvZGUiOiJHTyIsInBhaWRVcFRvIjoiMjAyMC0wMy0xMCJ9LHsiY29kZSI6IkRNIiwicGFpZFVwVG8iOiIyMDIwLTAzLTEwIn0seyJjb2RlIjoiQ0wiLCJwYWlkVXBUbyI6IjIwMjAtMDMtMTAifSx7ImNvZGUiOiJSUzAiLCJwYWlkVXBUbyI6IjIwMjAtMDMtMTAifSx7ImNvZGUiOiJSQyIsInBhaWRVcFRvIjoiMjAyMC0wMy0xMCJ9LHsiY29kZSI6IlJEIiwicGFpZFVwVG8iOiIyMDIwLTAzLTEwIn0seyJjb2RlIjoiUEMiLCJwYWlkVXBUbyI6IjIwMjAtMDMtMTAifSx7ImNvZGUiOiJSTSIsInBhaWRVcFRvIjoiMjAyMC0wMy0xMCJ9LHsiY29kZSI6IldTIiwicGFpZFVwVG8iOiIyMDIwLTAzLTEwIn0seyJjb2RlIjoiREIiLCJwYWlkVXBUbyI6IjIwMjAtMDMtMTAifSx7ImNvZGUiOiJEQyIsInBhaWRVcFRvIjoiMjAyMC0wMy0xMCJ9LHsiY29kZSI6IlJTVSIsInBhaWRVcFRvIjoiMjAyMC0wMy0xMCJ9XSwiaGFzaCI6IjEyMjkxNDk4LzAiLCJncmFjZVBlcmlvZERheXMiOjAsImF1dG9Qcm9sb25nYXRlZCI6ZmFsc2UsImlzQXV0b1Byb2xvbmdhdGVkIjpmYWxzZX0=-SYSsDcgL1WJmHnsiGaHUWbaZLPIe2oI3QiIneDtaIbh/SZOqu63G7RGudSjf3ssPb1zxroMti/bK9II1ugHz/nTjw31Uah7D0HqeaCO7Zc0q9BeHysiWmBZ+8bABs5vr25GgIa5pO7CJhL7RitXQbWpAajrMBAeZ2En3wCgNwT6D6hNmiMlhXsWgwkw2OKnyHZ2dl8yEL+oV5SW14t7bdjYGKQrYjSd4+2zc4FnaX88yLnGNO9B3U6G+BuM37pxS5MjHrkHqMTK8W3I66mIj6IB6dYXD5nvKKO1OZREBAr6LV0BqRYSbuJKFhZ8nd6YDG20GvW6leimv0rHVBFmA0w==-MIIElTCCAn2gAwIBAgIBCTANBgkqhkiG9w0BAQsFADAYMRYwFAYDVQQDDA1KZXRQcm9maWxlIENBMB4XDTE4MTEwMTEyMjk0NloXDTIwMTEwMjEyMjk0NlowaDELMAkGA1UEBhMCQ1oxDjAMBgNVBAgMBU51c2xlMQ8wDQYDVQQHDAZQcmFndWUxGTAXBgNVBAoMEEpldEJyYWlucyBzLnIuby4xHTAbBgNVBAMMFHByb2QzeS1mcm9tLTIwMTgxMTAxMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxcQkq+zdxlR2mmRYBPzGbUNdMN6OaXiXzxIWtMEkrJMO/5oUfQJbLLuMSMK0QHFmaI37WShyxZcfRCidwXjot4zmNBKnlyHodDij/78TmVqFl8nOeD5+07B8VEaIu7c3E1N+e1doC6wht4I4+IEmtsPAdoaj5WCQVQbrI8KeT8M9VcBIWX7fD0fhexfg3ZRt0xqwMcXGNp3DdJHiO0rCdU+Itv7EmtnSVq9jBG1usMSFvMowR25mju2JcPFp1+I4ZI+FqgR8gyG8oiNDyNEoAbsR3lOpI7grUYSvkB/xVy/VoklPCK2h0f0GJxFjnye8NT1PAywoyl7RmiAVRE/EKwIDAQABo4GZMIGWMAkGA1UdEwQCMAAwHQYDVR0OBBYEFGEpG9oZGcfLMGNBkY7SgHiMGgTcMEgGA1UdIwRBMD+AFKOetkhnQhI2Qb1t4Lm0oFKLl/GzoRykGjAYMRYwFAYDVQQDDA1KZXRQcm9maWxlIENBggkA0myxg7KDeeEwEwYDVR0lBAwwCgYIKwYBBQUHAwEwCwYDVR0PBAQDAgWgMA0GCSqGSIb3DQEBCwUAA4ICAQAF8uc+YJOHHwOFcPzmbjcxNDuGoOUIP+2h1R75Lecswb7ru2LWWSUMtXVKQzChLNPn/72W0k+oI056tgiwuG7M49LXp4zQVlQnFmWU1wwGvVhq5R63Rpjx1zjGUhcXgayu7+9zMUW596Lbomsg8qVve6euqsrFicYkIIuUu4zYPndJwfe0YkS5nY72SHnNdbPhEnN8wcB2Kz+OIG0lih3yz5EqFhld03bGp222ZQCIghCTVL6QBNadGsiN/lWLl4JdR3lJkZzlpFdiHijoVRdWeSWqM4y0t23c92HXKrgppoSV18XMxrWVdoSM3nuMHwxGhFyde05OdDtLpCv+jlWf5REAHHA201pAU6bJSZINyHDUTB+Beo28rRXSwSh3OUIvYwKNVeoBY+KwOJ7WnuTCUq1meE6GkKc4D/cXmgpOyW/1SmBz3XjVIi/zprZ0zf3qH5mkphtg6ksjKgKjmx1cXfZAAX6wcDBNaCL+Ortep1Dh8xDUbqbBVNBL4jbiL3i3xsfNiyJgaZ5sX7i8tmStEpLbPwvHcByuf59qJhV/bZOl8KqJBETCDJcY6O2aqhTUy+9x93ThKs1GKrRPePrWPluud7ttlgtRveit/pcBrnQcXOl1rHq7ByB8CFAxNotRUYL9IF5n3wJOgkPojMy6jetQA5Ogc8Sm7RG6vg1yow==
```
> 激活码失效，戳[这里](http://idea.lanyus.com/)，10￥购买正版（+微信：wobupeilianai）  
> 配置IDEA，戳[这里](https://www.jetbrains.com/help/idea/guided-tour-around-the-user-interface.html)  
  
**配置编辑器主题**  
> 进入[Color Themes](http://color-themes.com)下载你喜欢的编辑器主题的`.jar`文件，将其存放到一个单独的文件夹里  
> 在菜单File -> Import Settings 中导入`.jar`文件选择黑色主题(Tangid)/白色主题(Relax Your Eyes)  
> 
> Appearance&Behavior -> Appearance：字体(Open sans)，字号15  
> Editor -> Font：字体(MonoSpaced)，字号15，行高1.1  
> Editor -> Color Scheme -> Color Scheme Font：字体(MonoSpaced)，字号18，行高1.1  
> Editor -> Color Scheme -> Console Font：字体(MonoSpaced)，字号12，行高1.1  
> 
> UI主题选择File -> Settings -> Plugins，MarketPlace选项卡，搜Material Theme UI安装即可或者使用[自定义UI主题Gray](https://blog.jetbrains.com/idea/2019/03/brighten-up-your-day-add-color-to-intellij-idea/)(也在Plugins选项卡点击右边设置 -> Install plugin from disk -> 你下载的jar包文件) 
  
**IDEA插件**  
[热部署插件JRebel](https://www.jianshu.com/p/8a62db5c982a)  
[开发测试必备部署神器Cloud Toolkit](https://help.aliyun.com/product/29966.html)
  
#### 安装Android Studio  
```bash
sudo pacman -S android-studio
```
  
#### 安装虚拟机 VMware  
虚拟机需要Linux-headers头文件来支持虚拟化  
```bash
sudo pacman -S linux-headers
```
开始安装虚拟机软件  
```bash
yay vmware  # 选择11 archlinuxcn/vmware-workstation 15.0.2-3 (400.9 MiB 829.2 MiB)
```
输入编号11，按照提示操作即可；安装成功启动输入以下序列号  
```bash
GV7N2-DQZ00-4897Y-27ZNX-NV0TD
```
如果虚拟机中的镜像系统无法联网，并且打开`虚拟网络编辑器`出现`network configuration is missing. ensure that /etc/vmware/networking exists`的error弹框  
则选择`start`或`enable`以下三个服务  
```bash
sudo systemctl start vmware-networks.service     #网络服务
sudo systemctl start vmware-usbarbitrator.service       
sudo systemctl start vmware-hostd.service     

sudo systemctl enable vmware-networks.service 
sudo systemctl enable vmware-usbarbitrator.service  
sudo systemctl enable vmware-hostd.service
```
启动完服务记得重新加载VMware模块  
```bash
sudo modprobe -a vmw_vmci vmmon  
```
  
#### 安装虚拟机 Virtualbox  
```bash
sudo pacman -S virtualbox  
正在解决依赖关系...  
      :: 有 2 个软件包可提供 VIRTUALBOX-HOST-MODULES ：  
      :: 软件库 community  
       1) virtualbox-host-dkms  2) virtualbox-host-modules-arch  

      输入某个数字 ( 默认=1 ): 2  
```
执行上面安装命令后，有两个选择，按照[Arch wiki](https://wiki.archlinux.org/index.php/VirtualBox_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))的解释：  
如果在用默认的 linux 内核，建议安装virtualbox-host-modules-arch  
如果用了其它的内核，需要安装 virtualbox-host-dkms  
  
第一种virtualbox-host-dkms安装：输入1的时候,等待安装完成  
```bash
sudo pacman -S virtualbox   #再安装virtualbox
sudo pacman -S linux-headers    #安装Linux头文件内核
```
  
第二种virtualbox-host-modules-arch安装：输入2的时候，也就是我目前安装的方式  
```bash
sudo pacman -S virtualbox   #也要安装virtualbox
```
  
<span style="color:#ff0000;">注意：如果再创建虚拟机后启动虚拟机时报错，大意是： 不是模块没有加载，就是有权限许可问题，用 /sbin/vboxconfig 解决问题！但是，在 /sbin 目录里面根本没有这条命令，那么可以使用下面这条命令重新加载模块</span>  
```bash
sudo vboxreload
```
  
#### 安装 Visual Studio Code  
```bash
yay vscode
```
选择一个下载量最高的`aur/visual-studio-code-bin 1.30.1-1 (+781 28.27%)  `，输入编号，一路回车  
> <center>插件列表</center>
>
> [Material](#)　　　#一款冷门主题    
> [One Dark Pro](#)　　　#源于Atom的主题  
> [Material Theme](https://marketplace.visualstudio.com/items?itemName=Equinusocio.vsc-material-theme)　　　#Visual Studio Code现在最神奇的主题  
> [Power Mode](#)　　　#打字泡沫  
> 
> [vscode-icon](#)　　　#树目录加上图标  
> [Path Intellisense](#)　　　#默认路径补全  
> [Document this](#)　　　#快速注释  
> [Project Manager](#)　　　#多个项目切换  
> [vscode-fileheader](#)　　　#顶部注释模板  
> [filesize](#)　　　#底部显示文件大小  
> 
> [terminal](#)　　　#终端(右键菜单启动)  
> [view-in-browser](#)     #打开浏览器插件  
>  
> [Atuo Rename Tag](#)　　　#同时修改html标签首  
> 
> [vscode-leetcode](https://github.com/jdneo/vscode-leetcode/blob/master/docs/README_zh-CN.md)　　　#leetcode刷题助手  
  
#### 安装Node.js及GitBook  
安装**Node.js**及**npm**包  
```bash
yay -S nodejs npm       #安装
  
node -v     #查看node.js版本
npm -v      #查看npm版本

sudo npm config set registry https://registry.npm.taobao.org    #更换npn镜像为淘宝镜像
sudo npm config list    #查看下配置是否生效
```
**安装gitbook**  
```bash
sudo npm install gitbook-cli -g      #使用npm安装,前提是你先安装了Node.js
```
gitbook使用方式可以使用`gitbook -help`命令查看  
  
安装**PDF**输出  
```bash
sudo -v && wget -nv -O- https://download.calibre-ebook.com/linux-installer.sh | sudo sh /dev/stdin  #安装PDF阅读器calibre-ebook
sudo npm install gitbook-pdf -g  #安装gitbook pdf插件
```
使用 `gibook pdf` 命令输出为PDF文件  
  
**发布**到[github Pages](https://pages.github.com/)上，首先需要先安装 gh-pages  
```bash
npm i gh-pages -g   #安装gh-pages
gh-pages --help     #查看帮助
```
如已经通过 `gitbook build`或者`gitbook init + gitbook seve` 命令构建好了书籍，则会在目录下有个`_book` 子目录，该目录存放着本书籍的静态网页  
上传之前需要先`git init`初始化本目录，然后`git add .`添加到git暂存区，紧接着`git commit`，`git remote add origin`加入仓库地址，最后一步使用以下命令代替`git push`提交  
```bash
gh-pages -d _book
```
提交成功后，github远程仓库会有一个`gh-pages`分支  
> gh-pages 分支：保存书籍编译以后的静态网页  
  
通过上面的`gh-pages -d _book`命令，静态网页就已经部署在`github page`上了，但是.md的源文件还没上传，这里我们创建一个忽略文件`.gitignore`，内容如下：   
```bash
_book       #忽略_book文件夹
```
然后使用git的`git add .`，`git commit`，`git push -u origin master`提交，这样远程就会有一个master分支  
> master 分支：保存书籍的.md源码  
  
#### 命令行手册tldr  
Linux那么多命令的用法记不住怎么办？`例如：tar具体参数`不知道是什么了？每次上网查太麻烦，这里就推荐[tldr](https://github.com/tldr-pages/tldr)工具，还有[tldr网页版](https://tldr.ostera.io/)  
```bash
sudo npm install -g tldr    #安装
```
使用方法，例如：例如：tar具体使用  
```bash
tldr tar    #tldr 命令
-----------------------------------显示结果如下------------------------------
tar

  Archiving utility.
  Often combined with a compression method, such as gzip or bzip.

  - Create an archive from files:
    tar -cf target.tar file1 file2 file3

  - Create a gzipped archive:
    tar -czf target.tar.gz file1 file2 file3

  - Extract an archive in a target directory:
    tar -xf source.tar -C directory

  - Extract a gzipped archive in the current directory:
    tar -xzf source.tar.gz

  - Extract a bzipped archive in the current directory:
    tar -xjf source.tar.bz2

  - Create a compressed archive, using archive suffix to determine the compression program:
    tar -caf target.tar.xz file1 file2 file3

  - List the contents of a tar file:
    tar -tvf source.tar

  - Extract files matching a pattern:
    tar -xf source.tar --wildcards "*.html"
```
  
#### 安装kchmviewer / xchm  
做开发的经常看一些类似于[Java+API文档中文版.chm](http://doc.d8jd.com/)的`.chm`文件，就需要这个软件
```bash
yay -S kchmviewer
```
<span style="color:#ff0000">注意：如果打开的文档乱码，可能是文件路径存在中文的原因</span>  
```bash
-------------------------其他类似软件-----------------
sudo pacman -S xchm 
```
  
#### 安装krita绘图软件  
[Krita](https://krita.org) 是一款自由、免费、开源的数字绘画软件，针对概念美术、插图、布景、材质和电影特效行业的需求而设计，我主要是作为Linux下Photo Shop的替代品来使用  
```bash
yay -S krita
```
  
#### 图片处理软件GIMP
Linux下的photoshop解决方案  
```bash
sudo pacman -S gimp
```
  
#### 团队协作工具slack  
[slack](https://slack.com/features)采用聊天群组+大规模工具集成+文件整合+统一搜索四大功能，帮团队打造了一条流畅的消息总线，弥补了团队协作中的沟通断层
```bash
sudo pacman -S slack-desktop
```
  
#### 翻译神器Goldendict  
[Goldendict](http://goldendict.org/)是Linux下开源的翻译软件，支持屏幕取词/选词，还可以集成谷歌翻译，有道翻译，百度翻译的词库  
[有道翻译](https://aur.archlinux.org/packages/youdao-dict/)也有Linux版，但是好久没更新，启动也特别慢，还有各种广告
```bash
yay -S goldendict   #默认社区版，一路回车即可
```
安装成功之后，启动`Goldendict`，我们会发现它是英文的，而且只有维基百科的翻译  
先配置软件语言，单击`Edit` -> `Preferences`，在`Inteface`选项卡中  
勾选`Start with System`复选框，选择`Inteface language`为中文，`Display style`为`Modern`，重启软件后生效  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2bv50knftj30ii0drq4x.jpg)  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2bs3ca42rj30if0ds75h.jpg)  
默认只有维基百科一个搜索，现在给它添加谷歌翻译，需要安装`translate-shell`  
```bash
git clone git@github.com:soimort/translate-shell.git
cd translate-shell/
make
sudo make install
```
打开`GoldenDict`，在菜单编辑 -> 词典 -> 词典来源 -> 程序中，点击添加，勾上已启用，在类型中选`Plain` Text，在名称填写google，在命令中输入  
```bash
trans -e google -s auto -t zh-CN -show-original y -show-original-phonetics n -show-translation y -no-ansi -show-translation -phonetics n -show-prompt-message n -show-languages n -show-original-dictionary n -show-dictionary n -show-alternatives n "%GDWORD%"
```
最终效果如下图所示，命令中每个参数的具体含义可以在[translate-shell](https://github.com/soimort/translate-shell)的`README.md`中找到说明
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2bs3s0r00j30qc0fg403.jpg)  
最后，你可以在`Tweaks`中把它添加到自启程序  
  
#### 有道云笔记  
使用npm打包的跨平台[有道云笔记](https://github.com/jamasBian/youdao-note-electron)，在Downloads目录打开终端  
```bash
git clone https://github.com/jamasBian/youdao-note-electron.git     #克隆仓库
cd youdao-note-electron     #进入文件夹
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org  #安装依赖和运行应用程序 
cnpm install    #安装cnpm
cp ./assets/icon.png ./node_modules/_electron@4.1.1@electron/dist/resources/
npm start   #运行有道云笔记
```
不出意外应该可以运行有道云笔记，不过这还不是一个应用程序，这只相当于调试代码，还没打包成软件  
```bash
npm run build:linux   #打包成Linux应用程序
```
终端显示应用程序打到了`dist/Youdao-Note-Electron-linux-x64`文件夹，该文件夹下有一个`Youdao-Note-Electron`的run文件，接下来我们需要把`Youdao-Note-Electron-linux-x64`文件夹移动到指定目录，再创建一个快捷方式即可  
```bash
sudo cp -r dist/Youdao-Note-Electron-linux-x64 /opt     #将文件夹复制到opt目录
cd /opt/Youdao-Note-Electron-linux-x64  #进入文件夹
sudo gedit youdao-note.desktop     #创建快捷方式
```
在`youdao-note.desktop`填入如下内容  
```bash
[Desktop Entry]
Name=youdao-note
Comment=Cross platform ShadowsocksR GUI client built with electron
Exec="/opt/Youdao-Note-Electron-linux-x64/Youdao-Note-Electron" %U
Terminal=false
Type=Application
Icon=/opt/Youdao-Note-Electron-linux-x64/resources/icon.png
Encoding=UTF-8
StartupWMClass=youdao-note
Categories=Development;
```
附加运行权限  
```bash
sudo chmod a+x youdao-note.desktop  #添加运行权限
sudo cp youdao-note.desktop /usr/share/applications/  #复制到快捷方式文件夹
```
  
#### 免费的股市软件JStock  
[JStock](https://jstock.org/)可以轻松跟踪您的库存投资。它提供组织良好的股票市场信息，帮助您确定最佳投资策略。  
```bash
yay -S jstock
```
  
### 【Mac美化篇】  
之前的美化是不是意犹未尽，由于还没安装基本的软件Chrome以及git之类的，所以美化不得已排在软件安装篇后面  
  
#### 窗口主题
**主题一：** [VimixDark-Gtk-Theme](https://github.com/vinceliuice/vimix-gtk-themes)(推荐)  
下载之后解压，进入目录有一个shell执行文件，进入终端输入安装命令：  
```bash
./Vimix-installer.sh
```
安装成功，可以在gnome-tweak-tool中的外观处选择主题  
  
**主题二：** 其他Mac主题[Mac OS Mojave ](https://github.com/vinceliuice/Mojave-gtk-theme);下载之后解压到`~/.themes`中即可
  
 
#### 图标主题  
<span style="color:#ff0000;">注意：图标主题如果下载慢可以用手机热点</span>  
  
**主题一：** [La Capitaine](https://github.com/keeferrourke/la-capitaine-icon-theme)
```bash
cd /usr/share/icons
git clone https://github.com/keeferrourke/la-capitaine-icon-theme.git
```
在gnome-tweak-tool中的外观处选择图标  
  
**主题二：** MacOS[图标](https://git.opendesktop.org/umayanga/Cupertino-macOS-iCons)
```bash
cd /usr/share/icons
git clone https://git.opendesktop.org/umayanga/Cupertino-macOS-iCons.git
```
  
#### 指针主题  
**主题一：**[Capitaine Cursors](https://www.opendesktop.org/p/1148692/)  
选择一个颜色下载(例如：capitaine-cursors-r3.tar.gz)，右击提取到当前目录  
```bash
sudo cp -r capitaine-cursors-r3 /usr/share/icons   #复制文件夹到icons目录
```
在gnome-tweak-tool中的Appearance处选择Cursor鼠标指针主题  
  
**主题二：**[OpenZone](https://www.opendesktop.org/p/999999/)  
直接下载`openzone-cursors-1.2.8.tar.xz`包，右击提取到当前目录会有一个`openzone-cursors-1.2.8`文件夹，解压`openzone-cursors-1.2.8/openzone-cursors`下的`OpenZone_*.tar.xz`文件，才是需要复制到icons目录的文件夹  
例如：右击`OpenZone_Black.tar.xz`提取到此处会得到`OpenZone_Black`文件夹  
```bash
sudo cp -r OpenZone_Black /usr/share/icons 
```
  
####  安装gnome-shell-extensions
```bash
sudo pacman -S gnome-shell-extensions
sudo pacman -S chrome-gnome-shell
```
然后在谷歌商店直接搜`Gnome Shell Integration`进行安装，需要更多美化插件，可以通过[Gnome Shell Integration](https://extensions.gnome.org)下载和安装  
<span style="color:#ff0000;">注意：谷歌商店和Gnome Shell Integration需要翻墙，如果下载慢可以用手机热点</span>  
> [Appfolders管理扩展](https://extensions.gnome.org/extension/1217/appfolders-manager/)开始菜单可以创建文件夹管理快捷方式  
> [状态栏天气插件](https://extensions.gnome.org/extension/750/openweather/)配置[坐标](https://lbs.qq.com/tool/getpoint/index.html)  
> [状态栏系统监测插件](https://extensions.gnome.org/extension/120/system-monitor/)  
> [系统检测插件Vitals](https://extensions.gnome.org/extension/1460/vitals/)  
> [Coverflow Alt-Tab](https://extensions.gnome.org/extension/120/system-monitor/)轮播式切换窗口  
> [Blyr](https://extensions.gnome.org/extension/1251/blyr/)高斯模糊GNOME Shell UI元素  
> [TopIcons Plus](https://extensions.gnome.org/extension/1031/topicons/)类似于windows的系统托盘(Opacity：255；Icon Size：16；Spacing between icons：12；Tray horizontal alignment：Right)   
> [Notes](https://extensions.gnome.org/extension/1357/notes)GNOME Shell桌面的粘滞便笺(番茄工作法利器)  
  
#### GRUB主题  
GRUB 是什么？GRUB 是引导程序，负责引导操作系统，开机时那个选择系统的画面  
<span style="color:#ff0000;">注意：下载主题需要翻墙，如果下载慢可以用手机热点</span>  
  
**主题一：**[Breeze GRUB2主题](https://www.opendesktop.org/p/1000140/)  
下载主题包并解压  
```bash
sudo mv grub2-theme-breeze-5.13.1 /boot/grub/themes/    #解压文件夹内的 breeze 文件夹复制到 /boot/grub/themes/
cd /boot/grub/themes/grub2-theme-breeze-5.13.1/breeze/  #观察是否有theme.txt文件

sudo gedit /etc/default/grub     #修改该文件
```
将`#GRUB_THEME="/path/to/gfxtheme"`改为`GRUB_THEME="/boot/grub/themes/grub2-theme-breeze-5.13.1/breeze/theme.txt"`  
最后还要重新生成grub.cfg文件才能让背景或者主题生效  
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
  
**主题二：**[蛴螬主题vimix](https://www.opendesktop.org/p/1009236/)  
下载主题包并解压  
```bash
sudo ./Install  #使用root用户运行里面的安装脚本
```
  
#### 自动更换桌面壁纸/锁屏壁纸<span style="color:#ff0000;">(可选)</span>  
gnome的默认壁纸存放在`/usr/share/backgrounds/gnome/`下，同时该目录下有个`adwaita-timed.xml`文件，记录了桌面背景信息  
为了不会搞乱，我们就把背景图全部复制到此目录，然后再新建个xml文件`adwaita-ppt.xml`；样式如下：  
```xml
<background>

  <starttime>
    <year>2011</year>
    <month>11</month>
    <day>24</day>
    <hour>7</hour>
    <minute>00</minute>
    <second>00</second>
  </starttime>

    <static>
    <duration>3600.0</duration>
    <file>picture1.jpg</file>
    </static>

    <transition type="overlay">
    <duration>18000.0</duration>
    <from>picture1.jpg</from>
    <to>/picture2.jpg</to>
    </transition>

    <static>
    <duration>18000.0</duration>
    <file>picture2.jpg</file>
    </static>

    <transition type="overlay">
    <duration>21600.0</duration>
    <from>picture2.jpg</from>
    <to>picture1.jpg</to>
    </transition>

</background>
```
简单解释以下这个xml文件的含义：  
> starttime ：这个部分规定了壁纸切换起始时间，设置成过去的某个时间即可(设置成2020年就要等到2020年才会有效果)  
> static ：这个部分表示在duration规定的时间(以秒为单位)中壁纸都是file中给定的那张图片  
> transition ：这个部分表示在duration规定的时间内壁纸从from中的图片切换到to中的图片  
  
你可以添加任意多个static+transition的组合，只需要最后一个transition切换回最初的static那张图片就可以循环更换壁纸了  
  
启用这样的xml文件需要使用dconf系统配置编辑器，使用下面命令安装  
```bash
sudo pacman -S dconf-editor
```
首先打开dconf编辑器，展开`org–gnome–desktop–background`这一项，可以看到其中的`icture-uri`修这一项的默认值是`file:///usr/share/backgrounds/gnome/adwaita-timed.xml`，这个就是你刚装好桌面是的默认壁纸啦，将其改成你的xml文件就可以了  
  
锁屏界面的壁纸更换方法也一样，只不过把`org–gnome–desktop–background`改成`org–gnome–desktop–screensaver`而已  
  
#### 视频壁纸Komorebi  
[komorebi](https://github.com/cheesecakeufo/komorebi)是适用于Linux的漂亮且可定制的壁纸管理器，支持普通图片壁纸，视频壁纸<span style="color:#ff0000;">(强力推荐)</span>  
```bash
yay -S komorebi     #安装
```
安装之后在开始菜单有两个软件**komorebi**和**wallpaper**  
> komorebi负责启动壁纸，和壁纸切换，时钟显示效果  
> wallpaper用于制作壁纸，可以选择图片/视频，制作完成会提示使用命令移动一个文件到`/usr/share/Komorebi`目录，例如：`sudo mv /home/teaper/sx /usr/share/Komorebi`  
  
#### X窗口系统监视软件Conky  
[Conky](https://github.com/brndnmtthws/conky)是一个用于X窗口系统的系统监视软件，`Conky` 可以监控许多系统变量，包括 CPU、内存、交换分区、磁盘空间、温度、top、上传、下载、系统消息等  
<span style="color:#ff0000;">注意：`Conky`和[视频壁纸Komorebi](#视频壁纸komorebi)只能选一个，因为`Komorebi`会遮住`Conky`，导致显示不出来</span>  
  
安装conky的软件包`conky-lua`  
```bash
yay conky
```
然后会出现多个包，这里就说三个  
> `aur/conky-nvidia 1.11.3-1 (+123 0.27%)`支持Nvidia的conky包  
> `aur/conky-lua 1.11.3-1 (+50 0.71%)`支持Lua的conky包  
> `aur/conky-lua-nv 1.11.3-2 (+118 0.87%)`支持Lua和Nvidia的conky包  
  
这里选择`aur/conky-lua 1.11.3-1 (+50 0.71%)`这个，输入它的编号回车，输入y回车  
安装成功之后再安装`conky-manager`管理器  
```bash
sudo pacman -S conky-manager
```
再开始菜单中运行`conky-manager`，使用以下命令刷新字体缓存  
```bash
fc-cache -vf
```
克隆conky主题[Conky-Rings](https://github.com/9527tech/conkyrc)到`.Conky`隐藏文件夹，该主题源文件在[deviantart](https://www.deviantart.com/trollpunny/art/Conky-Rings-Revamped-591137228)也可以下载到  
```bash
git clone git@github.com:9527tech/conkyrc.git ~/.Conky
cd ~/.Conky #进入文件夹
```
使用`cat /proc/cpuinfo`查看CPU信息，核心数`core id`，线程数`processor`  
根据自己的CPU核心数和线程数选择对应分支<span style="color:#ff0000;">(例如：我的是4核4线程)</span>   
> master分支：4核8线程&&纯粹8线程  
> other_4t分支：4核4线程  
> other_8t分支：8核8线程  
  
```bash
git checkout other_4t   #切换到other_4t分支
```
运行启动脚本  
```bash
sh startconky.sh
```
如果要修改模块位置，开启或者关闭，可以修改同名的文件，例如时钟  
```bash
gedit clock     #编辑clock文件
```
把`own_window yes`改成`own_window no`即可关闭时间  
模块的具体参数可以根据[参考文档](http://conky.sourceforge.net/docs.html)配置  

运行之后`NETWORK`和`DISK`模块无效果，需要分别指定你自己的网卡和硬盘  
```bash
-----------network文件----------
Down:$alignr${downspeed wlp2s0}/s   #wlp2s0就是我的网卡，不知道网卡名字可以使用ifconfig查看
Up:$alignr${upspeed wlp2s0}/s
${downspeedgraph wlp2s0 324D23 77B753}

${upspeedgraph wlp2s0 324D23 77B753}
```
```bash
-----------disk文件----------
Read:$alignr${diskio_read /dev/sda}/s  #sda是我固态盘(不是分区),不知道名字使用lsblk查看
Write:$alignr${diskio_write /dev/sda}/s
${diskiograph_read /dev/sda 324D23 77B753}

${diskiograph_write /dev/sda 324D23 77B753}
```
还有其他问题，参考——[【conky 配置文件详解】](https://a-wing.top/linux/linux%E7%BE%8E%E5%8C%96/2016/01/05/conky-e9-85-8d-e7-bd-ae-e6-96-87-e4-bb-b6-e8-af-a6-e8-a7-a3.html)解决  
  
设置**开机自启**  
```bash
sudo gedit ~/.config/autostart/conky.desktop    #创建自启快捷方式
```
内容如下  
```bash
[Desktop Entry]
Type=Application
Name=conky
Exec=/home/teaper/.Conky/startconky.sh --daemonize --pause=5
StartupNotify=false
Terminal=false
```
  
### 【游戏安装篇】
Linux下玩游戏，wine用的比较多，请确保已经安装  
  
#### 安装Steam  
安装steam之前，确保已经安装了32位的N卡驱动`lib32-nvidia-utils`，没有的话使用`sudo pacman -S lib32-nvidia-utils`命令安装，否则会出现无法启动问题
```bash
sudo pacman -S steam    #安装steam
```
另外，由于我教程的`/home`目录属于机械硬盘，机械盘不适合运行软件，所以需要在`/opt`目录新建`steam`文件夹  
```bash
sudo mkdir /opt/steam   #创建文件夹
sudo chmod a+w /opt/steam   #加权限
```
然后在启动steam之后，进入steam设置 > 下载 > steam库文件中添加`/opt/steam`路径，并且右击设置为默认路径，以后在steam中下载的游戏就会自动进入该文件夹  
> STEAM 游戏推荐  
> [Northgard](https://store.steampowered.com/app/466560/Northgard/)（北境之地）  
> [Dota 2](https://store.steampowered.com/app/570/Dota_2/)  
> [文明 VI](https://store.steampowered.com/app/289070/Sid_Meiers_Civilization_VI/)  

![](http://ww1.sinaimg.cn/large/006kWbIoly1g25l4i5aupj31hc0u0e83.jpg)  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g25l59vjxnj31hc0u04qp.jpg)  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g25l6c0oq0j31hc0u0x6p.jpg)  
