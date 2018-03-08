<a name="content">目录</a>

[Linux (Raspbian) 进阶操作](#title)
- [安装中文语言包](#install-chinese)
	- [CentOS (yum)](#centos)
	- [Ubuntu (apt)](#debian)
	- [Raspbian](#raspbian)
- [更改键盘布局](#change-keyboard)
- [更改软件源](#change-source)
	- [2017-11-29-raspbian-stretch(基于Debian9)](#stretch)
	- [2017-06-21-raspbian-jessie(基于Debian8)](#jessie)
- [文本处理双剑客: sed & AWK](#operate-txt)
	- [sed](#sed)
	- [AWK](#awk)
- [进程并行化](#parallel-process)
	- [最简单的并行化方法：&+wait](#simplest-wait)
- [解压](#decompress)
- [启用树莓派root用户](#active-root)

<h1 name="title">Linux进阶操作</h1>

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

<a name="stretch"><h4>2017-11-29-raspbian-stretch(基于Debian9)</h4></a>

更改/etc/apt/source.list文件：

```
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi

deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
```

接着更改/etc/apt/source.list.d/raspi.list:

```
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ stretch main ui
```

<a name="jessie"><h4>2017-06-21-raspbian-jessie(基于Debian8)</h4></a>

更改/etc/apt/source.list文件：

```
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main contrib non-free rpi

deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main contrib non-free rpi
```

接着更改/etc/apt/source.list.d/raspi.list:

```
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ jessie main ui
```

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

(1) [ubuntu 16.04 英文版命令行安装中文语言包](http://blog.csdn.net/sweettool/article/details/70224459)

(2) [ubuntu16.4安装中文输入法](https://www.cnblogs.com/zhaopengcheng/p/6040605.html)

(3) [如何让树莓派显示中文？](http://shumeipai.nxez.com/2016/03/13/how-to-make-raspberry-pi-display-chinese.html)

(4) [树莓派开箱配置之更改键盘布局](http://shumeipai.nxez.com/2017/11/13/raspberry-pi-change-the-keyboard-layout.html)

(5) 小伊老师Linux课程课件

(6) [Bash脚本实现批量作业并行化](https://www.linuxidc.com/Linux/2015-01/112363.htm)

(7) [树莓派 - 修改pi账号密码,开启root账号](http://blog.csdn.net/yoie01/article/details/45115067)
