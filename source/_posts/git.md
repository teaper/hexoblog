---
title: 版本控制工具Git
categories: [Git]
tags: [git,ssh,github,git flow]
date: 2019-06-30 13:07:11
---
#### 什么是git   
[Git](https://git-scm.com/) 是一个免费的开源 分布式版本控制系统，旨在处理速度和效率从小到大的项目  
Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件  
Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持  
  
入门推荐：[《Pro.Git中文版》](https://git-scm.com/book/en/v2)、《版本控制之道——使用Git》、[《Git权威指南》](http://www.worldhello.net/gotgit/)  
  
#### git的安装  
[Mac平台](https://sourceforge.net/projects/git-osx-installer/)  
[Windows平台](https://git-for-windows.github.io/)  
Linux平台:`apt-get install git`  
  
#### 如何学习git  
安装好 Git 之后,怎么学习是个问题,其实关于 Git 有很多图形化的软件可以操作,但是我强 烈建议大家从命令行开始学习理解,我知道没接触过命令行的人可能会很抵触,但是我的亲 身实践证明,只有一开始学习命令行,之后你对 Git 的每一步操作才能理解其意义,而等你熟 练之后你想用任何的图形化的软件去操作完全没问题  
  
我一开始教我们团队成员全是基于命令行的,事后证明他们现在已经深深爱上命令行无法自 拔,他们很理解 Git 每一步操作的具体含义,以致于在实际项目很少犯错,所以我这里也是基 于命令行去教你们学习理解  
  
#### git命令列表  
```bash
~$ git
usage: git [--version] [--help] [-C <path>] [-c name=value]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           <command> [<args>]
 
These are common Git commands used in various situations:
 
start a working area (see also: git help tutorial)
   clone      Clone a repository into a new directory
   init       Create an empty Git repository or reinitialize an existing one
 
work on the current change (see also: git help everyday)
   add        Add file contents to the index
   mv         Move or rename a file, a directory, or a symlink
   reset      Reset current HEAD to the specified state
   rm         Remove files from the working tree and from the index
 
examine the history and state (see also: git help revisions)
   bisect     Use binary search to find the commit that introduced a bug
   grep       Print lines matching a pattern
   log        Show commit logs
   show       Show various types of objects
   status     Show the working tree status
 
grow, mark and tweak your common history
   branch     List, create, or delete branches
   checkout   Switch branches or restore working tree files
   commit     Record changes to the repository
   diff       Show changes between commits, commit and working tree, etc
   merge      Join two or more development histories together
   rebase     Forward-port local commits to the updated upstream head
   tag        Create, list, delete or verify a tag object signed with GPG
 
collaborate (see also: git help workflows)
   fetch      Download objects and refs from another repository
   pull       Fetch from and integrate with another repository or a local branch
   push       Update remote refs along with associated objects
 
'git help -a' and 'git help -g' list available subcommands and some
concept guides. See 'git help <command>' or 'git help <concept>'
to read about a specific subcommand or concept.
```
  
#### git的几个概念  
**index** 暂存，又名暂存区   
**staging area** 暂存区：可以设置哪些变更要提交到版本库，哪些先不提交  
> 指/.git/index文件  
  
**work area** 工作区：就是我们进行工作的地方  
> 除.git文件夹外的其他内容区域  
  
**local repository** 本地仓库：就是我们自己工作的电脑上保存版本数据的地方  
> .git文件夹，内容在/.git/objects中  
  
**remote repository** 远程仓库：远程的服务器上的仓库，如[gitHub](https://github.com/)、[gitLab](https://about.gitlab.com/)  
![工作区](http://ww1.sinaimg.cn/large/006kWbIogy1g18u5wza2lj30sz0h3n3i.jpg)
![暂存区](http://ww1.sinaimg.cn/large/006kWbIogy1g18u6aacpej30tk0h7agc.jpg)
  
#### git具体命令  
第一步,我们先新建一个文件夹,在文件夹里新建一个文件<span style="color:#ff0000">(我是用Linux命令去新建的,Windows用户可以自己手动新建)</span>  
```bash
mkdir test             #创建文件夹test
cd test                #切换到test目录
sudo gedit a.md        #新建a.md文件
```
这里提醒下:在进行任何 Git 操作之前,都要先切换到 Git 仓库目录,也就是先要先切换到项目的文件夹目录下  
  
这个时候我们先随便操作一个命令,比如 git status ,可以看到如下提示<span style="color:#ff0000">(别纠结颜色之类的,配置与主题不一样而已)</span>:
```bash
~/test$ git status   #查看git状态
fatal: Not a git repository (or any of the parent directories): .git
```
意思就是当前目录还不是一个Git仓库  
  
**git init**  
这个时候用到了第一个命令,代表初始化git仓库,输入`git init`之后会提示:  
```bash
~/test$ git init   #初始化git仓库
Initialized empty Git repository in /home/teaper/Downloads/test/.git/
```
可以看到初始化成了,至此 test目录已经是一个 git 仓库了  
  
**git status**  
紧接着我们输入 `git status` 命令,会有如下提示:  
```bash
~/test$ git status
On branch master
 
Initial commit
 
Untracked files:
  (use "git add <file>..." to include in what will be committed)
 
    a.md
 
nothing added to commit but untracked files present (use "git add" to track)
```
默认就直接在 master 分支,关于分支的概念后面会提,这时最主要的是提示a.md文件  
Untracked files,就是说a.md 这个文件还没有被跟踪,还没有提交在git仓库里呢,而且提示你可以使用 git add 去操作你想要提交的文件  
  
`git status`这个命令顾名思义就是查看状态,这个命令可以算是使用最频繁的一个命令了,建议大家没事就输入下这个命令,来查看你当前git仓库的一些状态  
  
**git add**  
上面提示 a.md文件还没有提交到git仓库里,这个时候我们可以随便编辑a.md文件,然后输入`git add a.md`,然后再输入`git status`  
```bash
~/test$ git add a.md    #让git跟踪a.md文件,此时a.md文件会进入暂存区
~/test$ git status
On branch master
 
Initial commit
 
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
 
    new file:   a.md
```
  
**git commit**  
接着我们输入`git commit -m 'first commit'` ,这个命令什么意思呢? commit是提交的意思,-m代表是提交信息,执行了以上命令代表我们已经正式进行了第一次提交
这个时候再输入 `git status` ,<span style="color:#ff0000;">会提示nothing to commit</span>  
```bash
~/test$ git commit -m 'first commit'  #提交到版本库
[master (root-commit) 60990d1] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 a.md
~/test$ git status
On branch master
nothing to commit, working directory clean
```
当我们对已经提交到版本库的文件进行修改后,是不能马上commit提交的,需要重新`add`一次,有没有一步到位的呢?有的  
```bash
git commit -am "first commit"     #不通过暂存区直接将文件提交到版本库
```
如果我们不小心把上一次提交的注解说明(first commit)写错了,怎么修改呢?  
```bash
git commit --amend -m "新的注释说明"     #仅修改上一次提交的注解说明
```
如果我们需要撤销或修改更多操作,比如上次提交少提交了a.txt文件,那么我们可以  
```bash
git add a.txt
git commit --amend -m "first commit"     #重新提交上一次提交的内容
```
<span style="color:#ff0000;">注意:--amend只能使用它来修正你的上一次提交,更早的提交是不能使用“amend”来进行操作,你不要对一个已经在远程仓库上被发布，或者说已经被共享的提交进行“amend”操作,很容易导致整个团队陷入麻烦</span>  
  
**git log**  
这个时候我们输入`git log`命令,会看到如下:  
```bash
~/test$ git log  #查看提交日志
commit 60990d11c3634daa16d0a3fe735a0d6a768c2fbf
Author: teaper <teaper@aliyun.com>
Date:   Tue Jul 17 10:21:07 2018 +0800
 
    first commit
```
git log 命令可以查看所有产生的commit记录,所以可以看到已经产生了一条commit记录,而提交时候的附带信息叫 'first commit'  
  
**git branch**  
branch 即分支的意思,分支的概念很重要,尤其是团队协作的时候,假设两个人都在做同一个项目,这个时候分支就是保证两人能协同合作的最大利器了。举个例子,A, B俩人都在做同一个项目,但是不同的模块,这个时候A新建了一个分支叫a,B新建了一个分支叫b,这样A、B做的所有代码改动都各自在各自的分支,互不影响,等到俩人都把各自的模块都做完了,最后再统一把分支合并起来  
  
执行git init初始化git仓库之后会默认生成一个主分支master,也是你所在的默认分支,也基本是实际开发正式环境下的分支,一般情况下master分支不会轻易直接在上面操作的,你们可以输入`git branch`查看下当前分支情况:  
```bash
~/test$ git branch  #查看所有分支
* master
```
如果我们想在此基础上新建一个分支呢,很简单,执行`git branch develop`就新建了一个名字叫develop的分支,这时候分支develop跟分支 master是一模一样的内容,我们再输入`git branch`查看的当前分支情况:  
```bash
~/test$ git branch develop  #新建develop分支
~/test$ git branch
  develop
* master    #*在哪里就表示那个是当前所在的分支
```
即虽然新建了一个develop的分支,但是当前所在的分支还是在master上,如果我们想在develop分支上进行开发,首先要先切换到develop分支上才行,所以下一步要切换分支  
```bash
~/test$ git  checkout develop   #切换到develop分支
Switched to branch 'develop'
~/test$ git branch
* develop
  master
```
<span style="color:#ff0000;">那有人就说了,我要先新建再切换,未免有点麻烦,有没有一步到位的,聪明:</span>
```bash
git checkout -b develop   #一步到位,创建并且切换到develop分支
```
有新建分支,那肯定有删除分支,假如这个分支新建错了,或者develop分支的代码已经顺利合并到master分支来了,那么develop分支没用了,需要删除  
```bash
git branch  -d develop   #develop分支已经合并到其他(比如:master)分支,需要删除用这个
git branch  -D develop   #develop分支没有合并到其他分支,需要强制删除用这个
```
  
**git merge**  
A同学在develop分支代码写的不亦乐乎,终于他的功能完工了,并且测试也都ok了,准备要上线了,这个时候就需要把他的代码合并到主分支master上来,然后发布支用到的命令,针对这个情况,需要先做两步,第一步是切换到master分支,如果你已经在了就不用切换了,第二步执行git merge develop,意思就是把a分支的代码合并过来,不出意外,这个时候develop分支的代码就顺利合并到master分支来了  
  
为什么说不出意外呢?因为这个时候可能会有冲突而合并失败,留个包袱,这个到后面进阶的时候再讲  
```bash
~/test$ git checkout master  #切换到master分支
Switched to branch 'master'
~/test$ git merge develop   #合并develop分支
Already up-to-date.
```
  
**git tag**  
我们在客户端开发的时候经常有版本的概念,比如v1.0、v1.1之类的,不同的版本肯定对应不同的代码,所以我一般要给我们的代码加上标签,这样假设v1.1版本出了一个新bug,但是又不晓得v1.0是不是有这个bug,有了标签就可以顺利切换到v1.0的代码,重新打个包测试了  
  
所以如果想要新建一个标签很简单,比如`git tag v1.0`就代表我在当前代码状态下新建了一个v1.0的标签,输入`git tag`可以查看历史tag记录  
```bash
~/test$ git tag v1.0    #新建v1.0版本标签
~/test$ git tag         #查看历史版本
v1.0
v1.1
```
可以看到我新建了两个标签v1.0、v1.1  
想要切换到某个tag怎么办?也很简单,执行`git checkoutv 1.0`,这样就顺利的切换到v1.0tag的代码状态了  
```bash
git checkout v1.0   #切换到v1.0版本
```
  
#### SSH  
你拥有了一个 GitHub 账号之后,就可以自由的 clone 或者下载其他项目,也可以创建自己的项目,但是你没法提交代码。仔细想想也知道,肯定不可能随意就能提交代码的,如果随意可以提交代码,那么 GitHub 上的项目岂不乱了套了,所以提交代码之前一定是需要某种授权的,而 GitHub上一般都是基于SSH授权的  
  
那么什么是SSH呢?简单点说,SSH是一种网络协议,用于计算机之间的加密登录。目前是每一台Linux电脑的标准配置。而大多数Git服务器都会选择使用SSH公钥来进行授权,所以想要在 GitHub 提交代码的第一步就是要先添加SSH key配置  
  
#### 生成SSH key  
Linux与Mac都是默认安装了SSH,而Windows系统安装了Git Bash应该也是带了SSH的。大家可以在终端<span style="color:#ff0000;">(win下在 Git Bash里)</span>输入ssh如果出现以下提示证明你本机已经安装SSH,否则请搜索自行安装下  
```bash
~/test$ ssh     #查看是否安装SSH
usage: ssh [-1246AaCfGgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec]
           [-D [bind_address:]port] [-E log_file] [-e escape_char]
           [-F configfile] [-I pkcs11] [-i identity_file] [-L address]
           [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port]
           [-Q query_option] [-R address] [-S ctl_path] [-W host:port]
           [-w local_tun[:remote_tun]] [user@]hostname [command]
 
~/test$ ssh-keygen -t rsa -C "teaper@aliyun.com"  #生成SSH key,邮箱填你gitHub上的绑定邮箱
Generating public/private rsa key pair.
Enter file in which to save the key (/home/teaper/.ssh/id_rsa):
/home/teaper/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/teaper/.ssh/id_rsa.
Your public key has been saved in /home/teaper/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:wcNn6oSZO/1qafMyuYTCF13njjFO4RJAKUzA0GUZ98A teaper@aliyun.com
The key's randomart image is:
+---[RSA 2048]----+
|.+.====.         |
|  o.+.E*         |
|     .  B = .    |
|       = X +     |
|      = S = .    |
|   .   B + =     |
|    o = +oo .    |
|     o oO.       |
|       ooBo      |
+----[SHA256]-----+
```
紧接着输入`ssh-keygen -t rsa`,什么意思呢?就是指定rsa算法生成密钥,接着连续三个回车键(不需要输入密码),然后就会生成两个文件id_rsa和 id_rsa.pub,而id_rsa是密钥,id_rsa.pub就是公钥。这两文件默认分别在如下目录里生成:
Linux/Mac系统在~/.ssh下,win系统在/c/Documents and Settings/username/.ssh下,都是隐藏文件,相信你们有办法查看的  
  
接下来要做的是把id_rsa.pub的内容添加到GitHub上,这样你本地的id_rsa密钥跟 GitHub上的id_rsa.pub公钥进行配对,授权成功才可以提交代码  
  
#### GitHub上添加SSH key  
进入你自己的[github](https://github.com/)  
点击头像旁边的下拉，Settings -> SSH and GPG keys -> New SSH key  
新建一个key，title不用填，把复制的SSH key内容粘贴上去  
![ssh](http://ww1.sinaimg.cn/large/006kWbIogy1g18u7550u2j30rq0g0acc.jpg)  
```bash
~/test$ ssh git@github.com       #测试连接服务器是否成功
PTY allocation request failed on channel 0
Hi TEAPER! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```
  
#### Push和Pull  
Push:直译过来就是「推」的意思,什么意思呢?如果你本地代码有更新了,那么就需要把本地代码推到远程仓库,这样本地仓库跟远程仓库就可以保持同步了  
```bash
git push origin master   #把本地代码推到远程master分支
```
Pull:直译过来就是「拉」的意思,如果别人提交代码到远程仓库,这个时候你需要把远程仓库的最新代码拉下来,然后保证两端代码的同步  
```bash
git pull origin master   #把远程最新的代码更新到本地
```
<span style="color:#ff0000;">注意:一般我们在push之前都会先pull,这样不容易冲突</span>  
  
#### 提交代码  
在提交代码之前需要先要设置下自己的用户名与邮箱,这些信息会出现在所有的commit记录里,执行以下代码就可以设置:  
```bash
git config  —global user.name   "teaper"
git config  —global user.email  "www@teaper.cn"
```
以上进行了全局配置,当然有些时候我们的某一个项目想要用特定的邮箱,这个时候只需切换到你的项目,以上代码把--global参数去除,再重新执行一遍就ok了  
  
添加 SSH key 成功之后,我们就有权限向GitHub上我们自己的项目提交代码了,而提交代码有两种方法: 
Clone自己的项目我们以我在GitHub上创建的test项目为例,执行如下命令:  
```bash
git clone git@github.com:TEAPER/test.git     #克隆远程仓库到本地
```
这样就把test项目clone到了本地,你可以把clone命令理解为高级点的复制,这个时候该项目本身就已经是一个git仓库了,不需要执行git init进行初始化,而且甚至都已经关联好了远程仓库,我们只需要在这个test目录下任意修改或者添加文件,然后进行commit,之后就可以执行:  
```bash
git push origin master  或  git push -u origin master
```
至于怎么获取项目的仓库地址呢?如下图:  
![ssh地址](http://ww1.sinaimg.cn/large/006kWbIogy1g18u7kpjgqj30rp09c3zn.jpg)  
如果我们本地已经有一个完整的git仓库,并且已经进行了很多次commit,这个时候第一种方法就不适合了  
  
假设我们本地有个test2的项目,我们需要的是在 GitHub上建一个test的项目,然后把本地test2上的所有代码commit记录提交到GitHub上的test项目  
第一步就是在GitHub上建一个test项目,这个想必大家都会了,就不用多讲了  
第二步把本地test2项目与GitHub上的test项目进行关联,切换到test2目录,执行如下命令:  
```bash
git remote add origin git@github.com:TEAPER/test.git  #添加一个远程仓库orgin,地址是:git@github.com:TEAPER/test.git
```
origin是给这个项目的远程仓库起的名字,名字你可以随便取,只不过大家公认的只有一个远程仓库时名字就是origin  
```bash
git remote  -v   #查看当前项目有哪些远程仓库
```
接下来,我们本地的仓库就可以向远程仓库进行代码提交了:
```bash
git push -u origin master
```
  
#### 创建忽略文件  
通常情况下，您将拥有一类您不希望Git自动添加甚至将您展示为未跟踪的文件  
```bash
sudo gedit .gitignore #创建一个隐藏文件.gitignore
---------------------------------.内容如下-------------------------------------
*.a            ＃忽略所有.a文件
!lib.a         ＃但是跟踪lib.a，即使你忽略了上面的.a文件
/TODO          ＃仅忽略当前目录中的TODO文件，而不是subdir / TODO
build/         ＃忽略build /目录中的所有文件
doc/*.txt      #＃忽略doc / notes.txt，而不是doc / server / arch.txt
doc/**/*.pdf   #＃忽略doc /目录及其任何子目录中的所有.pdf文件
------------------------------------------------------------------------------
```
<span style="color:#ff0000;">注：上述文件编辑规则等同与Java正则表达式</span>  
  
#### alas  
我们知道我们执行的一些Git命令其实操作很频繁的类似有:  
> git commit  
> git checkout  
> git branch  
> git status  
> ......  
  
这些操作非常频繁,每次都要输入完全是不是有点麻烦,有没有一种简单的缩写输入呢?比如我对应的直接输入以下:  
> git c  
> git co  
> git br  
> git s  
> ......  
  
是不是很简单快捷啊?这个时候就用到了alias配置了,翻译过来就是别名的意思,输入以下命令就可以直接满足了以上的需求  
```bash
git config --global alias.co checkout        #别名
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.br branch
```
当然以上别名不是固定的,你完全可以根据自己的习惯去定制,除此之外还可以设置组合,比如:  
```bash
git config --global alias.psm 'push origin master'
git config --global alias.plm 'pull origin master'
```
之后经常用到的git push origin master和git pull origin master直接就用git psm和git plm代替了,是不是很方便?  
告诉大家一个比较屌的日志输出命令  
```bash
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
```
这样以后直接输入git lg就行了  
  
#### 其他配置  
当然还有一些其他有用的配置,默认情况下 git 用的编辑器是vi,如果不喜欢可以改成其他编辑器,比如我习惯vim  
```bash
git config  --global    core.editor "vim"       #设置Editor使用vim
```
你们如果喜欢其他编辑器可自行搜索配置,前提是本机有安装  
有些人纳闷我的终端怎么有各种颜色,自己却不是这样的,那是因为你们没有开启给Git着色,输入如下命令即可:  
```bash
git config --global color.ui true
```
还有些其他的配置如:  
```bash
git config --global core.quotepath false   #设置显示中文文件名
```
以上基本所有的配置就差不多了,默认这些配置都在 ~/.gitconfig文件下的,你可以找到这个文件查看自己的配置,也可以输入 `git config -l` 命令查看  
  
#### diff  
diff命令算是很常用的,使用场景是我们经常在做代码改动,但是有的时候2天前的代码了,做了哪些改动都忘记了,在提交之前需要确认下,这个时候就可以用diff来查看你到底做了哪些改动,举个例子,比如我有一个a.md的文件,我现在做了一些改动,然后输入git diff就会看到如下:  
![diff](http://ww1.sinaimg.cn/large/006kWbIogy1g18u86o0a1j30bd03kgll.jpg)  
红色的部分前面有个-代表我删除的,绿色的部分前面有个+代表我增加的,所以从这里你们很一目了然的知道我到底对这个文件做了哪些改动  
值得一提的是直接输入git diff只能比较当前文件和暂存区文件差异,什么是暂存区?就是你还没有执行git add的文件  
当然跟暂存区做比较之外,他还可以有其他用法,如比较两次commit之间的差异,比较两个分支之间的差异,比较暂存区和版本库之间的差异等,具体用法如下:  
```bash
git diff <$id1>  <$id2>          #   比较两次提交之间的差异
git diff <branch1>..<branch2>    #   在两个分支之间比较
git diff --staged            #   比较暂存区和版本库差异
```
  
#### checkout  
我们知道checkout一般用作切换分支使用,比如切换到develop 分支,可以执行:  
```bash
git checkout develop
```
但是checkout不只用作切换分支,他可以用来切换tag,切换到某次commit,如:  
```bash
git checkout v1.0
git checkout ffd9f2dd68f1eb21d36cee50dbdd504e95d9c8f7    #后面的一长串是commit_id,可以根据git log看到
```
除了有“切换”的意思,checkout还有一个撤销的作用,举个例子,假设我们在一个分支开发一个小功能,刚写完一半,这时候需求变了,而且是大变化,之前写的代码完全用不了了,好在你刚写,甚至都没有 git add进暂存区,这个时候很简单的一个操作就直接把原文件还原:  
```bash
git checkout a.md
```
这里稍微提下,checkout命令只能撤销还没有add进暂存区的文件  
  
#### stash  
假设我们正在一个新的分支做新的功能,这个时候突然有一个紧急的bug需要修复,而且修复完之后需要立即发布。当然你说我先把刚写的一点代码进行提交不就行了么?这样理论上当然是ok的,但是这会产品垃圾commit,原则上我们每次的commit都要有实际的意义,你的代码只是刚写了一半,还没有什么实际的意义是不建议就这样commit的,那么有没有一种比较好的办法,可以让我暂时切到别的分支,修复完bug再切回来,而且代码也能保留的呢?  
  
这个时候stash命令就大有用处了,前提是我们的代码没有进行commit,哪怕你执行了add 也没关系,我们先执行  
```bash
git stash  #暂时保存工作区
```
命令,什么意思呢?意思就是把当前分支所有没有 commit 的代码先暂存起来,这个时候你再执行git status你会发现当前分支很干净,几乎看不到任何改动,你的代码改动也看不见了,但其实是暂存起来了。执行  
```bash
git stash list    #查看暂存记录
```
你会发现此时暂存区已经有了一条记录。  
这个时候你可以切换会其他分支,赶紧把bug修复好,然后发布。之后一切都解决了,你再切换回来继续做你之前没做完的功能,但是之前的代码怎么还原呢?  
```bsah
git stash apply   #还原工作区
```
你会发现你之前的代码全部又回来了,就好像一切都没发生过一样,紧接着你最好需要把暂存区的这次stash记录删除,执行:  
```bash
git stash drop      #删除暂存记录
```
就把最近一条的stash记录删除了,是不是很方便?其实还有更方便的,你可以使用:  
```bash
git stash pop    #还原工作区并删除暂存记录
```
来代替apply命令,pop跟apply的唯一区别就是pop不但会帮你把代码还原,还自动帮你把这条stash记录删除,省的自己再drop list命令来确认是不是已经没有记录了  
最后还有一个命令介绍下:  
```bash
git stash clear   #清空所有暂存记录
```
就是清空所有暂存区的记录,drop是只删除一条,当然后面可以跟stash_id参数来删除指定的某条记录,不跟参数就是删除最近的,而clear是清空  
  
#### merge 和 rebase  
我们知道merge分支是合并的意思,我们在一个featureA分支开发完了一个功能,这个时候需要合并到主分支master上去,我们只需要进行如下操作:  
```bash
git checkout master
git merge featureA
```
其实rebase命令也是合并的意思,上面的需求我们一样可以如下操作:  
```bash
git checkout master
git rebase featureA
```
rebase跟merge的区别你们可以理解成有两个书架,你需要把两个书架的书整理到一起去,第一种做法是merge,比较粗鲁暴力,就直接腾出一块地方把另一个书架的书全部放进去,虽然暴力,但是这种做法你可以知道哪些书是来自另一个书架的;第二种做法就是rebase,他会把两个书架的书先进行比较,按照购书的时间来给他重新排序,然后重新放置好,这样做的好处就是合并之后的书架看起来很有逻辑,但是你很难清晰的知道哪些书来自哪个书架的  
  
只能说各有好处的,不同的团队根据不同的需要以及不同的习惯来选择就好  
  
#### 解决合并冲突  
假设这样一个场景,A和B两位同学各自开了两个分支来开发不同的功能,大部分情况下都会尽量互不干扰的,但是有一个需求A需要改动一个基础库中的一个类的方法,不巧B这个时候由于业务需要也改动了基础库的这个方法,因为这种情况比较特殊,A和B都认为不会对地方造成影响,等两人各自把功能做完了,需要合并的到主分支master的时候,我们假设先合并A的分支,这个时候没问题的,之后再继续合并B的分支,这个时候想想也知道就有冲突了,因为A和B两个人同时更改了同一个地方,Git 本身他没法判断你们两个谁更改的对,但是这个时候他会智能的提示有conflicts,需要手动解决这个冲突之后再重新进行一次commit提交。我随便在项目搞了一个冲突做下示例:  
```bash
~/test$ git merge feature  #合并feature分支发生冲突,在a.md文件上
Auto-merging a.md
CONFLICT (content): Merge conflict in a.md
Automatic merge failed; fix conflicts and then commit the result.
```
  
#### 什么是分支  
我们知道孙悟空有七十二变,可以幻化万千猴子,每只猴子都和本体一模一样,本体也就是默认分支master,而从master上创建的分支develop的内容和master是一样的,也可以简单理解成复制,当猴子们打败了天兵之后,就会重新回到真身,我们把这个过程称之为合并  
  
#### 分支的常用操作  
我们默认都会有一个主分支叫 master,下面我们先来看下关于分支的一些基本操作:  
```bash
git branch  develop     #新建一个叫develop的分支
```
这里稍微提醒下大家,新建分支的命令是基于当前所在分支的基础上进行的,即以上是基于mater分支新建了一个叫做develop的分支,此时develop分支跟 master分支的内容完全一样。如果你有A、B、C三个分支,三个分支是三位同学的,各分支内容不一样,如果你当前是在B分支,如果执行新建分支命令,则新建的分支内容跟B分支是一样的,同理如果当前所在是C分支,那就是基于C分支基础上新建的分支  
```bash
git checkout develop    #切换到develop分支
```
如果把以上两步合并,即新建并且自动切换到develop分支:  
```bash
git checkout -b develop    #创建并切换到新分支
```
把develop分支推送到远程仓库  
```bash
git push origin develop
```
如果你远程的分支想取名叫develop2,那执行以下代码:  
```bash
git push origin develop:develop2
```
<span style="color:#ff0000;">但是强烈不建议这样,这会导致很混乱,很难管理,所以建议本地分支跟远程分支名要保持一致</span>  
```bash
git branch          #查看本地分支列表
git branch -r       #查看远程分支列表
git branch -d develop     #删除本地分支
git branch -D develop     #强制删除本地分支
git push origin :develop    #删除远程分支
```
如果远程分支有个develop,而本地没有,你想把远程的develop分支迁到本地:  
```bash
git checkout develop origin/develop
```
同样的把远程分支迁到本地顺便切换到该分支:  
```bash
git checkout -b develop origin/develop
```
  
#### 团队协作流程  
一般来说,如果你是一个人开发,可能只需要master、develop两个分支就ok了,平时开发在develop分支进行,开发完成之后,发布之前合并到master分支,这个流程没啥大问题  
如果你是3、5个人,那就不一样了,有人说也没多大问题啊,直接可以新建A、B、C三个人的分支啊,每人各自开发各自的分支,然后开发完成之后再逐步合并到master分支  
然而现实却是,你正在某个分支开发某个功能呢,这时候突然发现线上有一个很严重的bug ,不得不停下手头的工作优先处理bug,而且很多时候多人协作下如果没有一个规范,很容易产生问题,所以多人协作下的分支管理规范很重要,就跟代码规范一样重要,以下就跟大家推荐一种我们内部在使用的一种分支管理流程Git Flow  
  
#### Git Flow  
Git Flow是一种比较成熟的分支管理流程,我们先看一张图能清晰的描述他整个的工作流程:  
![git flow](http://ww1.sinaimg.cn/large/006kWbIogy1g18u8v1z5ej30gz0mn47e.jpg)  
第一次看上面那个图是不是一脸懵逼?跟我当时一样,不急,我来用简单的话给你们解释下  
  
一般开发来说,大部分情况下都会拥有两个分支master和develop,他们的职责分别是:  
> **master**:永远处在即将发布(production-ready)状态   
> **develop**:最新的开发状态   
  
确切的说master、develop分支大部分情况下都会保持一致,只有在上线前的测试阶段develop比master的代码要多,一旦测试没问题,准备发布了,这时候会将develop合并到master上  
  
但是我们发布之后又会进行下一版本的功能开发,开发中间可能又会遇到需要紧急修复bug,一个功能开发完成之后突然需求变动了等情况,所以Git Flow除了以上master和develop两个主要分支以外,还提出了以下三个辅助分支:  
> **feature**:开发新功能的分支,基于develop,完成后merge回develop  
> **release**:准备要发布版本的分支,用来修复bug,基于develop,完成后merge回develop和master  
> **hotfix**:修复master上的问题,等不及release版本就必须马上上线.基于master,完成后merge回master和develop  
  
什么意思呢?  
举个例子,假设我们已经有master和develop两个分支了,这个时候我们准备做一个功能A,第一步我们要做的,就是基于develop分支新建个分支:  
```bash
git branch  feature/A    #创建功能A分支
```
看到了吧,其实就是一个规范,规定了所有开发的功能分支都以feature为前缀  
但是这个时候做着做着发现线上有一个紧急的bug需要修复,那赶紧停下手头的工作,立刻切换到master分支,然后再此基础上新建一个分支:  
```bash
git branch  hotfix/B    #创建修复B分支
```
代表新建了一个紧急修复分支,修复完成之后直接合并到develop和master,然后发布  
  
然后再切回我们的feature/A分支继续着我们的开发,如果开发完了,那么合并回develop分支,然后在develop分支属于测试环境,跟后端对接并且测试的差不多了,感觉可以发布到正式环境了,这个时候再新建一个release分支:  
```bash
git branch  release/1.0
```
这个时候所有的 api、数据等都是正式环境,然后在这个分支上进行最后的测试,发现bug直接进行修改,直到测试ok达到了发布的标准,最后把该分支合并到develop和master然后进行发布  
  
以上就是Git Flow的概念与大概流程,看起来很复杂,但是对于人数比较多的团队协作现实开发中确实会遇到这么复杂的情况,是目前很流行的一套分支管理流程,但是有人会问每次都要各种操作,合并来合并去,有点麻烦,哈哈,这点Git Flow早就想到了,为此还专门推出了一个Git Flow的工具,并且是开源的:  
  
GitHub开源地址:[https://github.com/nvie/gitflow](https://github.com/nvie/gitflow)  
各系统具体[安装方法](https://github.com/nvie/gitflow/wiki/Installation)  
  
简单点来说,就是这个工具帮我们省下了很多步骤,比如我们当前做了一个项目,需要用git进行管理,首先就要init初始化一个项目,然后要在默认的master分支上创建develop分支及其他辅助分支,分支之间又要合并,十分麻烦,容易混乱,有了Git Flow直接如下操作完成了:  
```bash
语法:git flow init [-d]    #-d标志将接受所有默认值
 
-----------------------------------------运行------------------------------------------
~/test$ git flow init      #使用flow初始化git仓库
Initialized empty Git repository in /home/teaper/Desktop/Untitled Folder/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master]      #询问你是否创建master分支,一路回车即可
Branch name for "next release" development: [develop]
 
How to name your supporting branch prefixes?
Feature branches? [feature/]
Bugfix branches? [bugfix/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
Hooks and filters directory? [/home/teaper/Desktop/Untitled Folder/.git/hooks]
~/test$ git br   #查看分支,会发现已经创建并切换到了develop分支
* develop
  master
```
假如我们当前处于develop或master分支,如果想要开发一个新的功能,第一步切换到develop分支,第二步新建一个以feature开头的分支名,有了Git Flow直接如下操作完成了:  
```bash
git flow feature start A     #创建功能A分支
```
这个分支完成之后,需要合并到develop分支,然而直接进行如下操作就行:  
```bash
git flow feature finish A      #将功能A分支合并到develop,并且删除功能A分支
```
<span style="color:#ff0000;">注意:创建功能A分支后,还是要把A分支内项目commit到版本库,才能执行git flow feature finish A ;如果是hotfix或者release分支,只需要把上面命令中的feature替换即可,最后会自动帮你合并到develop、master两个分支</span>  
  
如果需要将功能分支推/拉到远程存储库，请使用：  
```bash
git flow feature publish <name>
git flow feature pull <remote> <name>
```
<span style="color:#ff0000;">这个我们一般不会去用它,都是等功能合并到master后一并推送</span>  

















