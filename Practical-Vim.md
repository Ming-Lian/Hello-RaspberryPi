<a name="content">目录</a>

[Vim实用技巧](#title)
- [vimrc在哪儿?](#where-is-vimrc)
- [Vim进阶](#vim-advanced)
	- [运行命令](#vim-command)
	- [Vim插件](#vim-plugin)
- [命令大全：mindmap](#mindmap)


<h1 name="title">Vim实用技巧</h1>

<a name="where-is-vimrc"><h3>vimrc在哪儿? [<sup>目录</sup>](#content)</h3></a>

在 Linux 下的 Vim 中输入 :version 命令（可能显示不完全和这里相同）：

```
:version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Jun 26 2017 11:44:34)
Included patches: 1-678
Compiled by Easwy Yang <easwy.......>
   system vimrc file: "$VIM/vimrc"
     user vimrc file: "$HOME/.vimrc"
 2nd user vimrc file: "~/.vim/vimrc"
      user exrc file: "$HOME/.exrc"
  system gvimrc file: "$VIM/gvimrc"
    user gvimrc file: "$HOME/.gvimrc"
2nd user gvimrc file: "~/.vim/gvimrc"
...
```
在上面，我们看到列出了几个 vimrc 文件，有一个系统的 vimrc 文件，还有用户的 vimrc 文件，以及系统和用户 gvimrc 文件。出于和vi兼容的目的，vim也支持vi的exrc配置文件

如果不知道 $HOME 或者 $VIM 具体是哪个目录，可以在 vim 中用下面的命令查看：

```
:echo $VIM
:echo $HOME
```

可以用参数`-u`来指定启用的vim配置文件：

```
$ vim -u filename_vimrc
```

如果用 `vim -u NONE` 命令启动 Vim，则不读取任何 vimrc 文件：当你怀疑你的 vimrc 配置有问题时，可以用这种方式跳过 vimrc 的执行。

<a name="vim-advanced"><h3>Vim进阶 [<sup>目录</sup>](#content)</h3></a>

<a name="vim-command"><h4>运行命令 [<sup>目录</sup>](#content)</h4></a>

|命令|结果|
|:---|:---|
|:!command|在 Vim 中运行命令，按任意键返回 Vim|
|:!ls|在 Vim 中运行 ls 命令|
|:!wc %| 在 Vim 中运行 wc 命令（% 代表当前文件）|
|:r !command|插入命令执行结果|
|:r !date| 插入日期和时间|
|:r !sed -n 1,3p| filename 插入 fielname 文件的 1-3 行内容|


<a name="vim-plugin"><h4>Vim插件 [<sup>目录</sup>](#content)</h4></a>

<a name="mindmap"><h3>命令大全：mindmap [<sup>目录</sup>](#content)</h3></a>

<p align="center"><img src=./picture/Practical-Vim-mindmap.jpg width=900 /></p>
