---
layout: post
title:  "Linux commands"
date:   2016-10-07 11:41:22 +0800
categories: Linux
catalog: true
tags:
    - Linux
---


## commands 

command | description | description2
---|--- | ---
.         |代表此层目录|
..        |代表上一层目录|
-         |代表前一个工作目录|
~         |代表『目前使用者身份』所在的家目录|
~account  |代表 account 这个使用者的家目录(account是个帐号名称)|
cd|变换目录 Change Directory|
pwd|显示目前的目录 Print Working Directory|
mkdir|创建一个新的目录 make directory|
rmdir|删除一个空的目录|
df -h | 查看磁盘空间 | 
cat > foo.txt | 创建新文件， press CTRL+D to save file| 
cat foo.txt | display the file| 
sudo apt-get install open-vm-tools-lts-trusty | install vm tools | 
sudo apt-get update | downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.| 
sudo apt-get install open-vm-tools-desktop | install vm tools| 
cd .. | 返回上一级目录| 
print $ANDROID_HOME/ | 输出环境变量值| 
mkdir my-blog | 创建一个my-blog的文件夹| 
mkdocs serve | 运行mkdocs| 
apt-cache search linux-source | 搜索支持的内核代码| 
cd /|如果root 后 ls没有显示任何东西，切换到根目录| 
useradd cindy | Linux增加用户| 
test –d /home/cindy|为用户创建主文件夹输入命令| 
grep cindy /etc/passwd /etc/shadow /etc/group |在/etc/passwd里面创建一行与账号相关的数据，包括创建UID/GID/主文件夹等。在/etc/shadow里面将此账号的密码相关参数填入。在/etc/group里面加入一个与账号名称一样的组名| 
passwd cindy | 确认后会提示输入新的密码，再次确认密码后。若密码一致，提示更改成功。若两次输入密码不一致，提示更改不成功 |

## knowledge

### 编译第一个module
http://www.linuxidc.com/Linux/2012-12/75891.htm
[注意，根据这篇文章，后面要进入已经下载的src再make oldconfig]

### Linux文件属性
- 第一栏代表这个文件的类型与权限(permission)：
- 第二栏表示有多少档名连结到此节点(i-node)：
- 第三栏表示这个文件(或目录)的『拥有者账号』
- 第四栏表示这个文件的所属群组
- 第五栏为这个文件的容量大小，默认单位为bytes；
- 第六栏为这个文件的建档日期或者是最近的修改日期：
- 第七栏为这个文件的档名

### 文档权限
如：[-][rwx][r-x][r--]
        1  234  567  890
- 1 为：代表这个文件名为目录或文件，本例中为文件(-)；
    - 当为[ d ]则是目录，例如上表档名为『.gconf』的那一行； 
    - 当为[ - ]则是文件，例如上表档名为『install.log』那一行； 
    - 若是[ l ]则表示为连结档(link file)； 
    - 若是[ b ]则表示为装置文件里面的可供储存的接口设备(可随机存取装置)； 
    - 若是[ c ]则表示为装置文件里面的串行端口设备，例如键盘、鼠标(一次性读取装置)。
- 234为：拥有者的权限，本例中为可读、可写、可执行(rwx)；
- 567为：同群组用户权力，本例中为可读可执行(rx)；
- 890为：其他用户权力，本例中为可读(r)


### linux磁盘与文件管理
- ext2 :  Linux second extended file system, ext2fs
- superblock: superblock：记彔此 filesystem 癿整体信息，包括 inode/block 癿总量、使用量、剩余量， 以 及文件系统癿格式不相关信息等；
- inode：记彔档案癿属性，一个档案占用一个 inode，同时记彔此档案癿数据所在癿 block 号 码；
- block：实际记彔档案癿内容，若档案太大时，会占用多个 block 。
