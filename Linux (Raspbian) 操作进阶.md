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
- [进程并行化](#parallel-process)
	- [最简单的并行化方法：&+wait](#simplest-wait)
- [解压](#decompress)
- [启用树莓派root用户](#active-root)

<h1 name="title">Linux进阶操作</h1>

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
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ jessie main ui
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


参考资料：

(1) [Raspberry 3.5inch RPi LCD-4](http://blog.lxx1.com/2457/raspberry-3-5inch-rpi-lcd-4)

(2) [ubuntu 16.04 英文版命令行安装中文语言包](http://blog.csdn.net/sweettool/article/details/70224459)

(3) [ubuntu16.4安装中文输入法](https://www.cnblogs.com/zhaopengcheng/p/6040605.html)

(4) [如何让树莓派显示中文？](http://shumeipai.nxez.com/2016/03/13/how-to-make-raspberry-pi-display-chinese.html)

(5) [树莓派开箱配置之更改键盘布局](http://shumeipai.nxez.com/2017/11/13/raspberry-pi-change-the-keyboard-layout.html)

(6) [树莓派3B+ 软件源更改](#http://blog.csdn.net/kxwinxp/article/details/78370980)

(7) 小伊老师Linux课程课件

(8) [Bash脚本实现批量作业并行化](https://www.linuxidc.com/Linux/2015-01/112363.htm)

(9) [树莓派 - 修改pi账号密码,开启root账号](http://blog.csdn.net/yoie01/article/details/45115067)
