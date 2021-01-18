# linux规划学习
之前学习的linux比较杂而乱，基本是需要什么百度什么，系统的学习并没有总结出较好的笔记和规律。再上这门课的时候，我也不知道自己的基础是什么样子的。既然不知道底细，那么就从零开始快速学习吧

## 第一次使用linux的基本操作
第一次登陆，我们不能总是使用root，我们需要一个账号相对安全的使用系统
```shell
$useradd -m username # 添加用户
$passwd username # 添加密码
```
接下来我们删除二手系统中的原账号
```powershell
# 如果你已经切换到了新账号，你会发现你还没有足够的权限删除别人的账号，我们先切换回root账号
$su root
$userdel -r username
```
我们发现刚创建的账号的权利级别是很低的，要让我们的账号更加有权利，我们就需要把自己加入一个定义好的组
```powershell
$groups # 先查看所有的组别
$usermod -G groupName username # 添加组
```
基本的账号创建完毕了，我们就来检查一下自己的系统
```powershell
$uname (-a) # 查看系统信息，-a可以看到更多的内容
```
然后我们开始愉快的实验内容，以下命令为基本命令，在常规操作中使用（f,f1,f2等代表文件的路径）
实现效果|代码
:---|:--:
你想要看一下目录下面的文件有哪些|`ls -a包括隐藏文件/t按照时间排序/F查看哪些是文件夹/lg或者l都是看详细信息/迭代显示目录内容包括子文件夹中文件以及隐藏文件`或者`ll 简单看文件名`
快速浏览文件内容|`cat f`
复制不需要粘贴|`cp f1 f2`
删除|`rm (-rf) f 括号内是强制删除`
查看文件内容属性|`file f`
查找目录中文件，你需要学一下正则表达式|`find path regx`
实在不会了想看帮助|`man codename`
查看进程|`ps`
干掉进程|`kill p`
安装卸载驱动器（虽然我没咋用过）|`mount`和`umount`
查看日期和时间，还可以设置，注意系统时间可能是某自由国家的时间|`date`
清屏|`clear`

好了，实验做完了，你发现你忘记安装图像化界面的同时发现追番又更新了。此时我们就安装一下图形化（如果你在安装时没有安装图形化洁面的话）
为了演示和验证，我特地删除了我的图形化界面（手动狗头）
> 这是我删除的过程，了解一下就好
```powershell
$yum groupremove "X Window System" -y # X Window
$yum groupremove "GNOME Desktop Environment" -y #  GNOME
$yum groupremove "KDE (K Desktop Environment)" # KDE

$vi /etc/inittab
# Default runlevel. The runlevels used by RHS are: 

# 0 - halt (Do NOT set initdefault to this)           --停机
# 1 - Single user mode            --单用户模式
# 2 - Multiuser, without NFS (The same as 3, if you do not havenetworking)           --多用户模式，不支持NFS
# 3 - Full multiuser mode           --多用户模式     
# 4 - unused           --没有使用 
# 5 - X11           --图形界面方式 
# 6 - reboot (Do NOT set initdefault to this)           --重新启动
# 
id:5:initdefault:      --默认运行等级是5

#直接把5改成3就可以
```
安装图形化界面，想要登录管理员的账号，检查yum命令能否使用
```powershell
$yum grouplist
```
安装"GNOME Desktop"
```powershell
$yum grouplist "GNOME Desktop"
```
安装完成以后呢，直接键入`startx`进入图形化界面。
然而我们要方便一点，每次开机都进入图形化界面的化，就需要特地设置一下了
```powershell
# 命令行改为图形界面模式
$systemctl set-default graphical.target
# 图形界面模式改为命令行模式
$systemctl set-default multi-user.target
# 查看当前启动模式
$systemctl get-default
```
至此，第一次上机的内容就结束了。

## 进一步学习Linux
对我个人来说，我是一个不折不扣的颜控，我装了国产最新的优麒麟系统，真是不错哦，感觉非常丝滑
![1](https://s1.ax1x.com/2020/09/27/0FrsJA.png)