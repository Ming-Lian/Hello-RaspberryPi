<a name="content">目录</a>

[Linux进阶操作](#title)
- [安装中文语言包](#install-chinese)
	- [CentOS (yum)](#centos)
	- [Ubuntu (apt)](#debian)

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


参考资料：

(1) [ubuntu 16.04 英文版命令行安装中文语言包](http://blog.csdn.net/sweettool/article/details/70224459)

(2) [ubuntu16.4安装中文输入法](https://www.cnblogs.com/zhaopengcheng/p/6040605.html)

