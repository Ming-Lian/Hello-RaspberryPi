<a name="content">目录</a>

[Linux (Raspbian) 进阶操作](#title)
- [安装LCD驱动](#install-lcd)
	- [2017-03-02-raspbian-jessie以前版本](#lcd-old-version)
	- [2017-03-02-raspbian-jessie版本](#lcd-new-version)
	- [LCD和HDMI相互切换](#switch-between-lcd-and-hdmi)
- [安装中文语言包](#install-chinese)
	- [CentOS (yum)](#centos)
	- [Ubuntu (apt)](#debian)
	- [Raspbian](#raspbian)
- [更改键盘布局](#change-keyboard)
- [安装Gnome图形操作界面](#install-gnome-gui)
- [更改软件源](#change-source)
	- [raspbian-stretch(基于Debian9)](#stretch)
	- [raspbian-jessie(基于Debian8)](#jessie)
- [软件管理：APT/dpkg](#software-management)
	- [APT](#apt)
	- [dpkg](#dpkg)
- [升级及报错处理](#update-and-solve-err)
	- [update 出错](#update-err)
	- [upgrade 出错](#upgrade-err)
- [文本处理双剑客: sed & AWK](#operate-txt)
	- [sed](#sed)
	- [AWK](#awk)
		- [AWK基础](#basic-knowledge-of-awk)
		- [AWK进阶](#awk-advanced)
	- [补充：find & grep](#find-and-grep)
	- [${}，##和%%的使用](#use-special-sytax)
- [硬链接与软链接](#hardlink-and-softlink)
- [进程并行化](#parallel-process)
	- [最简单的并行化方法：&+wait](#simplest-wait)
- [解压](#decompress)
- [启用树莓派root用户](#active-root)
- [搭建树莓派Linux服务器](#setup-linux-server)
- [解决Secure SSH Client登录服务器失败](#solve-login-err-use-sshClient)
- [Shell编程](#shell-programing)
	- [参数传递](#shell-programing-pass-parameters)
		- [传统方法：使用$](#pass-parameters-use-doller)
		- [使用getopt](#pass-parameters-use-getopt)
		- [使用getopts](#pass-parameters-use-getopts)
	- [输出重定向](#redirect-output)


<h1 name="title">Linux进阶操作</h1>

<p align="center"><img src=./picture/Linux-advanced-raspi-logo.jpg width="500"></p>

<a name="install-lcd"><h3>安装LCD驱动 [<sup>目录</sup>](#content)</h3></a>

<h4 name="lcd-old-version">2017-03-02-raspbian-jessie以前版本</h4>

在/boot目录下，下载 **LCD-show-161112.tar.gz** : 

`http://blog.lxx1.com/wp-content/uploads/2017/03/LCD-show-161112.tar.gz`

然后解压，并赋予./LCD-show/LCD-show可执行权限

```
chmod +x ./LCD-show/LCD35-show
```

安装完成后，树莓派会重启，然后3.5寸屏幕上就可以正常显示了。

<h4 name="lcd-new-version">2017-03-02-raspbian-jessie版本</h4>

安装过程同上，安装包地址：

`wget http://blog.lxx1.com/wp-content/uploads/2017/03/LCD-show-170309.tar.gz`

<h4 name="switch-between-lcd-and-hdmi">LCD和HDMI相互切换</h4>

在正常使用LCD的情况下，外接HDMI是没有显示的，如需启用HDMI输出，需执行以下命令，树莓派会自动重启。再等待约30秒，HDMI显示屏开始显示。

```
./LCD-show/LCD-hdmi
```

如需切换回LCD显示方式，则需执行以下命令：

```
./LCD-show/LCD35-show
```

<a name="install-chinese"><h3>安装中文语言包 [<sup>目录</sup>](#content)</h3></a>

<h4 name="centos">CentOS (yum)</h4>

先查看语言包是否存在

```
$ locale -a
```
如果发现没有中文，则安装一个

```
$ sudo yum install kde-l10n-Chinese
$ sudo yum reinstall glibc-common
```

<h4 name="debian">Ubuntu (apt)</h4>

Ubuntu中的中文语言包:
- language-pack-zh-hans 简体中文
- language-pack-zh-hans-base
- language-pack-zh-hant 繁体中文
- language-pack-zh-hant-base

安装中文语言包

```
$ sudo apt-get install  language-pack-zh-han* 
```

在中文语言包安装完成后，就需要安装ibus输入法了，先安装ibus框架，需要在Terminal中输入：

```
$sudo apt-get install ibus ibus-clutter ibus-gtk ibus-gtk3 ibus-qt4
```

切换到ibus框架

```
$ im-config -s ibus
```

再安装拼音引擎

```
$ sudo apt-get install ibus-pinyin
```

再调出ibus Preference来添加该拼音输入法：

```
$ sudo ibus-setup
```

在弹出对话框中，添加Chinese-Pinyin输入法。

<img src=/picture/Linux-advanced-ibus-config.png width="800" />

<h4 name="raspbian">Raspbian</h4>

先安装
```
# 中文字体
sudo apt-get install ttf-wqy-zenhei
# 中文输入法
sudo apt-get install scim-pinyin
```

设置默认的字体

```
sudo raspi-config
```

然后选择change_locale，在Default locale for the system environment:中选择zh_CN.UTF-8,配置完成之后，输入命令`sudo reboot`

切换输入法：ctrl+space

<a name="change-keyboard"><h3>更改键盘布局 [<sup>目录</sup>](#content)</h3></a>

树莓派的默认键盘布局是英国(GB)，而我们用的键盘布局一般是美国(US)的。

通过 raspi-config 进入。

```
sudo raspi-config
```
进入后，选择：`5 Internationalisation Options` -> `Change Keyboard LaySet` -> `Generic 101-key PC` -> `English(US, alternative international)`

<a name="install-gnome-gui"><h3>安装Gnome图形操作界面 [<sup>目录</sup>](#content)</h3></a>

像安装CentOS，官方提供了较大的DVD IOS版的安装包，也提供了简化的Minimal IOS版的，如果选择的是Minimal版的，安装完之后一般只有命令行操作模式，不预装GNOME这样的GUI操作界面

这时如果想安装GNOME图形操作界面怎么实现？（如果一开始就知道有GUI的需求，最好直接下载完整版的

首先安装X(X Window System)

```
# yum groupinstall "X Window System"
```

由于这个软件组比较大，安装过程会比较慢，安装完成会出现complete！

检查一下我们已经安装的软件以及可以安装的软件

```
# yum grouplist
```

<p align="center"><img src=./picture/Linux-advanced-install-gnome-gui-1.png width=600 /></p>

然后安装我们需要的图形界面软件，GNOME(GNOME Desktop)

一定要注意：名称必须对应

```
# yum groupinstall "GNOME Desktop"
```

安装完成后我们可以通过命令 `startx` 进入图形界面，第一次进入会比较慢，请耐心等待

<a name="change-source"><h3>更改软件源 [<sup>目录</sup>](#content)</h3></a>

**由于树莓派软件官方源在国外，所以连接不稳定，且速度慢，所以安装初次进入系统后，一定要修改一下软件源。**

国内软件源推荐（中科大）：http://mirrors.ustc.edu.cn/raspbian/raspbian/

查看当前所用的Debian版本：

```
lsb_release -a
```
直接看到 Codename

<a name="stretch"><h4>raspbian-stretch (基于Debian9)</h4></a>

更改/etc/apt/source.list文件：

```
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi

deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
```

接着更改/etc/apt/source.list.d/raspi.list:

```
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ stretch main ui
```

<a name="jessie"><h4>raspbian-jessie (基于Debian8)</h4></a>

更改/etc/apt/source.list文件：

```
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main contrib non-free rpi

deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main contrib non-free rpi
```

接着更改/etc/apt/source.list.d/raspi.list:

```
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ jessie main ui
```

<a name="software-management"><h3>软件管理：APT/dpkg [<sup>目录</sup>](#content)</h3></a>

<a name="apt"><h4>APT: APT——Advanced Package Tool</h4></a>

|	功能	|	具体语句	|
|:---|:---|
|	软件源设置	|	/etc/apt/sources.list	|
|	更新软件源数据	|	apt-get update	|
|	更新已安装软件	|	apt-get upgrade	|
|	更新系统版本	|	apt-get dist-upgrade	|
|	通过安装包或卸载包来修复依赖错误	|	apt-get -f install	|
|	搜索软件源数据	|	apt-cache search foo	|
|	解压安装软件包	|	apt-get install foo	|
|	重新安装软件包	|	apt-get --reinstall install foo	|
|	删除软件包释放的内容	|	apt-get remove foo	|
|	卸载软件，同时清除该软件配置文件	|	apt-get --purge remove foo	|
|	删除不需要的包	|	apt-get autoclean	|
|	删除所有已下载的包	|	apt-get clean	|
|	自动安装编译一软件所需要的包	|	apt-get build-dep foo	|

<a name="dpkg"><h4>dpkg: package manager for Debian</h4></a>

|	功能	|	具体语句	|
|:---|:---|
|	显示DEB包信息	|	dpkg -I xx.deb	|
|	显示DEB包文件列表	|	dpkg -c xx.deb	|
|	安装DEB包	|	dpkg -i xx.deb	|
|	安装DEB包（指定根目录）	|	dpkg --root=<directory> -i xx.deb	|
|	显示所有已安装软件	|	dpkg -l	|
|	显示已安装包信息	|	dpkg -s foo	|
|	显示已安装包文件列表	|	dpkg -L foo	|
|	卸载包	|	dpkg -r foo	|
|	卸载软件包并删除其配置文件	|	dpkg -P foo	|
|	重新配置已安装程序	|	dpkg-reconfigure foo	|

<a name="update-and-solve-err"><h3>升级及报错处理 [<sup>目录</sup>](#content)</h3></a>

一般升级更新只需要运行以下两条命令即可：

```
sudo apt-get update # 更新软件源数据
sudo apt-get upgrade # 更新已安装软件
```

但是在实际操作过程中会出现一些问题：

<a name="update-err"><h4>update 出错 [<sup>目录</sup>](#content)</h4></a>

update过程出错一般是软件源地址无法连接，导致更新包无法下载到本地导致的，所以解决方法：[更改软件源](#change-source)

<a name="upgrade-err"><h4>upgrade 出错 [<sup>目录</sup>](#content)</h4></a>

如果update过程顺利，但是到执行`apt-get upgrade`时报错：

**E: The value 'stable' is invalid for APT::Default-Release as such a release is not available in the sources**

即系统指定的包管理工具的版本"stable"无效，需要更改为合适的版本，如**"wheezy"**或**"jessie"**

```
# 寻找相应的配置文件
grep -r "stable" /etc/apt/*

# 得到输出：/etc/apt/apt.conf.d/99openmediavault-release:APT::Default-Release "stable";
# 说明配置文件为：/etc/apt/apt.conf.d/99openmediavault-release

# 修改该文件，树莓派下vi/vim不太好用，建议使用nano。把"stable"改成"jessie"
nano /etc/apt/apt.conf.d/99openmediavault-release
```

然后再执行`apt-get upgrade`就没问题了

<a name="operate-txt"><h3>文本处理双剑客: sed & AWK [<sup>目录</sup>](#content)</h3></a>

<a name="sed"><h4>sed [<sup>目录</sup>](#content)</h4></a>

<a name="awk"><h4>AWK [<sup>目录</sup>](#content)</h4></a>

<a name="basic-knowledge-of-awk"><h5>AWK基础 [<sup>目录</sup>](#content)</h5></a>

脚本结构

```
awk

	'
	BEGIN {actions}
		/pattern1/{actions}
		...
		/patternN/{actions}
	END {actions}
	'

InputFile
```

- BEGIN{actions} 和END{actions} 是可选的
- /pattern/和{actions} 可以省略，但不能同时省略
	- /pattern/省略时表示对所有的输入行执行指定的actions
	- {actions} 省略时表示打印整行


- 编辑命令（一个、一组命令或一个命令文件）
	- 模式：只编辑与模式相匹配的记录行；没有提供模式时，匹配所有行
	- 命令：具体执行的编辑命令；没有指定命令时，打印整个记录行

特殊变量

|变量|描述|
|:---|:---|
|$n|当前记录的第n个字段，字段间由FS分隔|
|$0|完整的输入记录|
|ARGC|命令行参数的数目|
|ARGIND|命令行中当前文件的位置（从0开始算）|
|ARGV|包含命令行参数的数组|
|CONVFMT|数字转换格式（默认值为%.6g)|
|ENVIRON|环境变量关联数组|
|ERRNO|最后一个系统错误描述|
|FIELDWIDTHS|字段宽度列表（用空格键分隔）|
|FILENAME|当前文件名|
|FNR|同NR，但相对于当前文件|
|FS|字段分隔符（默认是任何空格）|
|IGNORECASE|如果为真，则进行忽略大小写的匹配|
|NF|当前记录中的字段数|
|NR|当前记录数|
|OFMT|数字的输出格式（默认值是%.6g)|
|OFS|输出字段分隔符（默认值是一个空格）|
|ORS|输出记录分隔符（默认值是一个换行符）|
|RLENGTH|由match函数所匹配的字符串的长度|
|RS|记录分隔符（默认是一个换行符）|
|RSTART|由match函数所匹配的字符串第一个位置|
|SUBSEP|数组下标分隔符（默认值是\034）|

<a name="awk-advanced"><h5>AWK进阶 [<sup>目录</sup>](#content)</h5></a>

- 格式化输出

	awk的格式化输出，和C语言的printf没什么两样：

	```bash
	$ awk '{printf "%-8s %-8s %-8s %-18s %-22s %-15s\n",$1,$2,$3,$4,$5,$6}' netstat.txt
	```

- 指定分隔符

	```bash
	$  awk  'BEGIN{FS=":"} {print $1,$3,$6}' /etc/passwd
	```

	上面的命令也等价于：（-F的意思就是指定分隔符）

	```bash
	$ awk  -F: '{print $1,$3,$6}' /etc/passwd
	```

	如果你要指定多个分隔符，你可以这样：`awk -F '[;:]'`

- **折分文件**

	```bash
	$ awk 'NR!=1{print > $6}' netstat.txt
 
	$ ls
	ESTABLISHED  FIN_WAIT1  FIN_WAIT2  LAST_ACK  LISTEN  netstat.txt  TIME_WAIT
	
	$ cat ESTABLISHED
	tcp        0      0 coolshell.cn:80        110.194.134.189:1032        ESTABLISHED
	tcp        0      0 coolshell.cn:80        123.169.124.111:49809       ESTABLISHED
	tcp        0      0 coolshell.cn:80        123.169.124.111:49829       ESTABLISHED
	tcp        0   4166 coolshell.cn:80        61.148.242.38:30901         ESTABLISHED
	tcp        0      0 coolshell.cn:80        110.194.134.189:4796        ESTABLISHED
	tcp        0      0 coolshell.cn:80        123.169.124.111:49840       ESTABLISHED
	
	$ cat FIN_WAIT1
	tcp        0      1 coolshell.cn:80        124.152.181.209:26825       FIN_WAIT1
	
	$ cat FIN_WAIT2
	tcp        0      0 coolshell.cn:80        61.140.101.185:37538        FIN_WAIT2
	tcp        0      0 coolshell.cn:80        116.234.127.77:11502        FIN_WAIT2
	tcp        0      0 coolshell.cn:80        117.136.20.85:50025         FIN_WAIT2
	
	$ cat LAST_ACK
	tcp        0      1 coolshell.cn:80        208.115.113.92:50601        LAST_ACK
	
	$ cat LISTEN
	tcp        0      0 0.0.0.0:3306           0.0.0.0:*                   LISTEN
	tcp        0      0 0.0.0.0:80             0.0.0.0:*                   LISTEN
	tcp        0      0 127.0.0.1:9000         0.0.0.0:*                   LISTEN
	tcp        0      0 :::22                  :::*                        LISTEN
	
	$ cat TIME_WAIT
	tcp        0      0 coolshell.cn:80        124.205.5.146:18245         TIME_WAIT
	tcp        0      0 coolshell.cn:80        183.60.215.36:36970         TIME_WAIT
	tcp        0      0 coolshell.cn:80        183.60.212.163:51082        TIME_WAIT
	```

<a name="find-and-grep"><h4>补充：find & grep [<sup>目录</sup>](#content)</h4></a>

（1）find

```
find . -name "*.jpg" -exec rm -fv {} \;
```

其中，

- `{}`：代表find过滤出的以`.jpg`结尾的文件；

- `\;`：`;`是`-exec`参数指定的command结束符，`\`对后面的分号进行转义；

（2）grep

<a name="use-special-sytax"><h4>${}，##和%%的使用 [<sup>目录</sup>](#content)</h4></a>

假设我们定义了一个变量为：

```
file=/dir1/dir2/dir3/my.file.txt
```

可以用`${ }`分别替换得到不同的值：

```
${file#*/}：删掉第一个/ 及其左边的字符串：dir1/dir2/dir3/my.file.txt
${file##*/}：删掉最后一个/  及其左边的字符串：my.file.txt
${file#*.}：删掉第一个.  及其左边的字符串：file.txt
${file##*.}：删掉最后一个.  及其左边的字符串：txt
${file%/*}：删掉最后一个 /  及其右边的字符串：/dir1/dir2/dir3
${file%%/*}：删掉第一个/  及其右边的字符串：(空值)
${file%.*}：删掉最后一个 .  及其右边的字符串：/dir1/dir2/dir3/my.file
${file%%.*}：删掉第一个 .   及其右边的字符串：/dir1/dir2/dir3/my
```

记忆的方法为：
\# 是 去掉左边（键盘上\#在 \$ 的左边）
\% 是去掉右边（键盘上\% 在\$ 的右边）
单一符号是最小匹配；两个符号是最大匹配

```
${file:0:5}：提取最左边的5 个字节：/dir1
${file:5:5}：提取第5 个字节右边的连续5个字节：/dir2
```

也可以对变量值里的字符串作替换：

```
${file/dir/path}：将第一个dir 替换为path：/path1/dir2/dir3/my.file.txt
${file//dir/path}：将全部dir 替换为path：/path1/path2/path3/my.file.txt
```

`${#var}` 可计算出变量值的长度：
`${#file}` 可得到27 ，因为`/dir1/dir2/dir3/my.file.txt` 是27个字节

<a name="hardlink-and-softlink"><h3>硬链接与软链接 [<sup>目录</sup>](#content)</h3></a>

文件由两部分构成：文件数据 + 文件元数据

**文件数据**：文件储存在硬盘上，硬盘的最小存储单位叫做”扇区”（Sector）。每个扇区储存512字节（相当于0.5KB）。操作系统读取硬盘的时候，不会一个个扇区地读取，这样效率太低，而是一次性连续读取多个扇区，即一次性读取一个”块”（block）。这种由多个扇区组成的”块”，是文件存取的最小单位。”块”的大小，最常见的是4KB，即连续八个 sector组成一个 block。

**文件元数据**：文件数据都储存在”块”中，那么很显然，我们还必须找到一个地方储存文件的元信息，比如文件的创建者、文件的创建日期、文件的大小等等。这种储存文件元信息的区域就叫做inode，中文译名为”索引节点”。

inode包含文件的元信息：

> - 文件的字节数
>
> - 文件拥有者的User ID
>
> - 文件的Group ID
>
> - 文件的读、写、执行权限
>
> - 文件的时间戳，共有三个：ctime指inode上一次变动的时间，mtime指文件内容上一次变动的时间，atime指文件上一次打开的时间。
>
> - 链接数，即有多少文件名指向这个inode
>
> - 文件数据block的位置

<p align="center"><img src=./picture/Linux-advanced-hardlinks-and-softlinks-1.png width=600 /></p>

**Unix/Linux系统内部不使用文件名，而使用inode号码来识别文件**。对于系统来说，文件名只是inode号码便于识别的别称或者绰号。表面上，用户通过文件名，打开文件。

实际上，系统内部这个过程分成三步：首先，系统找到这个文件名对应的inode号码；其次，通过inode号码，获取inode信息；最后，根据inode信息，找到文件数据所在的block，读出数据。

**硬链接**：

> 一般情况下，文件名和inode号码是”一一对应”关系，每个inode号码对应一个文件名。但是，Unix/Linux系统允许，多个文件名指向同一个inode号码。这意味着，可以用不同的文件名访问同样的内容；对文件内容进行修改，会影响到所有文件名；但是，删除一个文件名，不影响另一个文件名的访问。这种情况就被称为”硬链接”（hard link）。

**软链接**：

> 文件A和文件B的inode号码虽然不一样，但是文件A的内容是文件B的路径。读取文件A时，系统会自动将访问者导向文件B。因此，无论打开哪一个文件，最终读取的都是文件B。这时，文件A就称为文件B的”软链接”（soft link）或者”符号链接（symbolic link）。
> 
> 文件A依赖于文件B而存在，如果删除了文件B，打开文件A就会报错：”No such file or directory”。这是软链接与硬链接最大的不同：文件A指向文件B的文件名，而不是文件B的inode号码，文件B的inode”链接数”不会因此发生变化。

<a name="parallel-process"><h3>进程并行化 [<sup>目录</sup>](#content)</h3></a>

<a name="simplest-wait"><h4>最简单的并行化方法：&+wait [<sup>目录</sup>](#content)</h4></a>

利用Bash的后台运行&和wait函数，可实现最简单的批量作业并行化。

格式为：

```
do
	{
	code1;
	code2;
	...
	coden;
	} &
done
wait
```

<a name="active-root"><h3>启用树莓派root用户 [<sup>目录</sup>](#content)</h3></a>

树莓派使用的linux是debian系统，所以树莓派启用root和debian是相同的。

debian里root账户默认没有密码，但账户锁定。

当需要root权限时，由默认账户经由sudo执行，Raspberry pi 系统中的Raspbian

默认用户是pi 密码为raspberry

重新开启root账号，可由pi用户登录后，在命令行下执行

```
sudo passwd root
```

执行此命令后系统会提示输入两遍的root密码，输入你想设的密码即可

<a name="setup-linux-server"><h3>搭建树莓派Linux服务器 [<sup>目录</sup>](#content)</h3></a>

首先，你需要一根网线，将树莓派连接到外网

然后，开启树莓派的ssh服务

```
$ sudo raspi-config
```

进入设置界面后选择：`Advanced Options` => `SSH` => `enable`

接着查看你的树莓派当前被分配的ip

```
$ ifconfig -a
```

查看命令输出的头几行的其中一项"inet addr"就可以知道ip值了

现在Linux服务器就搭建好了，你只需要在你的电脑上安装上ssh终端工具即可登录到该服务器，是不是太简单了

<a name="solve-login-err-use-sshClient"><h3>解决Secure SSH Client登录服务器失败 [<sup>目录</sup>](#content)</h3></a>

登录失败报错信息
> Server responded"Algorithm negotiation failed"
Key exchange with the remote host failed. This can happen for
example computer does not support the selected algorthms.

登录失败原因：

> Because i had past updated server, it’s that means i had updated the sshd algorithm negotiation on server meanwhile, but the sshd algorithm negotiation in client is the old, so server cannot compatible the algorithm negotiation from client.

解决方法：

在/etc/ssh/sshd_config文件中添加以下内容

```
Ciphers aes128-cbc,aes192-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr,3des-cbc,arcfour128,arcfour256,arcfour,blowfish-cbc,cast128-cbc  
MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160,hmac-sha1-96,hmac-md5-96  
KexAlgorithms diffie-hellman-group1-sha1,diffie-hellman-group14-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group-exchange-sha256,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group1-sha1,curve25519-sha256@libssh.org  
```
重启sshd服务后，即可正常连接:

```
sudo /etc/init.d/ssh restart
```

<a name="shell-programing"><h2>Shell编程 [<sup>目录</sup>](#content)</h2></a>

<a name="shell-programing-pass-parameters"><h3>参数传递 [<sup>目录</sup>](#content)</h3></a>

<a name="pass-parameters-use-doller"><h4>传统方法：使用$ [<sup>目录</sup>](#content)</h4></a>

在shell脚本中，命令$后加一些特殊的标记可以取脚本的传入参数，如$0取脚本输入的第一个参数，$1取第二个参数等：

|	参数	|	描述	|
|:---|:---|
|	$0 	|	命令本身，类似c的argv[0]	|
|	$1、$2……	|	第1个参数、第2个参数……	|
|	$#	|	参数的个数，不包括$0	|
|	$@	|	以列表的形式返回参数列表，不包括$0	|
|	$?	|	最后运行的命令结束代码	|

<a name="pass-parameters-use-getopt"><h4>使用getopt [<sup>目录</sup>](#content)</h4></a>

getopt不是标准的unix命令，但它在大多数的发行版中都会带有，getopt虽然是带个get但此函数其实主要不是获取而是规范

getopt后面接受-o选项表示程序可接受的短选项 如`-o ab:c::`，表示程序参数后面可接受的选项为`-a -b -c` 其中-a后面不接受参数，-b后面必须接受参数(:)，-c后面参数可选(::)

getopt后面接受--long选项表示程序可接受的短选项 如`--long along,blong:,clong::`，长选项用逗号分隔开，后面接参数的标记号和短选项一致 

在对参数进行解析前先通过getopt进行解析

```
ARGS=`getopt -o ab:c: --long along,blong:,clong: -- "$@"`
#判断是否执行成功，没执行成功退出
if [ $? != 0 ] ; then echo "Terminating..." >&2 ; exit 1 ; fi
#重新设置参数
eval set -- "$ARGS"
```

进行参数解析

```
while true;do
    case "$1" in
        -a)
            echo "a"
            shift;;
        -b) 
            echo "b"
            shift;;
        -c) 
            echo "c"
            shift;;
        --)
            echo "--"
            shift
            break
            ;;
        *) 
            echo "??"
            shift
            ;;
    esac
done
```


<a name="pass-parameters-use-getopts"><h4>使用getopts [<sup>目录</sup>](#content)</h4></a>

注意： getopts只能接受短参数，即一个字母作为一个参数

```
getopts [option[:]] [DESCPRITION] VARIABLE
```

> option：表示为某个脚本可以使用的选项
> 
> `":"`：如果某个选项（option）后面出现了冒号（":"），则表示这个选项后面可以接参数（即一段描述信息DESCPRITION）
> 
> VARIABLE：表示将某个选项保存在变量VARIABLE中

```
while getopts ":a:b:c:" opt
do
    case $opt in
        a)
        echo "参数a的值$OPTARG"
        ;;
        b)
        echo "参数b的值$OPTARG"
        ;;
        c)
        echo "参数c的值$OPTARG"
        ;;
        ?)
        echo "未知参数"
        exit 1;;
    esac
done
```

<a name="redirect-output"><h3>输出重定向 [<sup>目录</sup>](#content)</h3></a>

当在运行一个程序，或者执行一个脚本时，会在执行过程中输出标准输出/标准错误，不论是标准输出还是标准错误，都是会默认输出到屏幕上，若想将它们输出到文件中，可以执行输出重定向

> - `>`或`1>` 标准输出重定向到文件中，标准错误还是默认输出（输出到屏幕）
> - `2>` 标准错误重定向到文件夹中，标准输出还是默认输出（输出到屏幕）
> - `1> 2>&1` 将标准输出与标准错误绑定，一起输出到文件中

若在执行`echo`时，想要指定输出的方式则可以采取以下方式：

> - `echo $i >&1` 以标准输出方式输出
> - `echo $i >&2` 以标准错误方式输出





参考资料：

(1) [Raspberry 3.5inch RPi LCD-4](http://blog.lxx1.com/2457/raspberry-3-5inch-rpi-lcd-4)

(2) [ubuntu 16.04 英文版命令行安装中文语言包](http://blog.csdn.net/sweettool/article/details/70224459)

(3) [ubuntu16.4安装中文输入法](https://www.cnblogs.com/zhaopengcheng/p/6040605.html)

(4) [如何让树莓派显示中文？](http://shumeipai.nxez.com/2016/03/13/how-to-make-raspberry-pi-display-chinese.html)

(5) [树莓派开箱配置之更改键盘布局](http://shumeipai.nxez.com/2017/11/13/raspberry-pi-change-the-keyboard-layout.html)

(6) [树莓派3B+ 软件源更改](#http://blog.csdn.net/kxwinxp/article/details/78370980)

(7) 小伊老师Linux课程课件

(8) [AWK 简明教程](https://coolshell.cn/articles/9070.html)

(9) [Linux节点理解](https://blog.csdn.net/jijiahao95/article/details/53398475)

(10) [Bash脚本实现批量作业并行化](https://www.linuxidc.com/Linux/2015-01/112363.htm)

(11) [树莓派 - 修改pi账号密码,开启root账号](http://blog.csdn.net/yoie01/article/details/45115067)

(12) [Ubuntu SSH Algorithm negotiation failed](http://chenqinfeng.com/2016/02/19/Ubuntu%20SSH%20Algorithm%20negotiation%20failed/)

(13) [shell学习 - 处理脚本的多参数输入](https://blog.csdn.net/czyt1988/article/details/79110450)

(14) [shell中脚本参数传递的两种方式](https://blog.csdn.net/sinat_36521655/article/details/79296181)

(15) [【百度经验】Linux CentOS 7的图形界面安装（GNOME、KDE等）](https://jingyan.baidu.com/article/0964eca26fc3b38284f53642.html)
