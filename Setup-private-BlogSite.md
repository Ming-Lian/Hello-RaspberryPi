<a name="content">目录</a>

[Hexo + GitHub Homepage搭建个人博客](#title)
- [1. 实现原理](#principle)
- [2. 搭建本地博客网站](#setup-local-website)
	- [2.1. 搭建Hexo博客网站](#setup-hexo-website)
	- [2.2. 编辑博客](#edit-blog)
	- [2.3. 修改Hexo主题](#change-theme)
- [3. 部署本地网站至GitHub](#deploy-to-github)
- [番外](#extern)
	- [本地图片加载](#load-local-image)






<h1 name="title">Hexo + GitHub Homepage搭建个人博客</h1>

<a name="principle"><h2>1. 实现原理 [<sup>目录</sup>](#content)</h2></a>

看GitHub某一个页面的URL：

```
https://github.com/Ming-Lian/NGS-analysis/blob/master/Stat-on-RNAseq.md
```

从它的URL里就可以看出服务器端的文件夹组织形式，`https://github.com`对应于其web服务器的家目录，以`~`表示：

```
~
|---- User1 # 用户名
		|---- repo1 # 仓库名
				|---- blob # 固定文件夹，意义不明
						|---- branch1 # 仓库下的分支名，默认分支为master
						|---- branch2
						...
		|---- repo2
		|---- repo3
		...
|---- User2
|---- User3
...
```

一般情况下，GitHub对每个仓库地下的文件是有一些限制的，对于纯文本形式的脚本文件可以以纯文本形式进行查看，若提交的是以`*.md`形式命名的文本，会进行Markdown语法解析，若是图片、pdf这样的文件也能进行查看，但是如果提交的是类似于可执行脚本或二进制程序，那么它是禁止它们执行的

但是若你建立的仓库是作为GitHub Homepage，GitHub服务器会给你破个例，这个仓库对应的文件夹下的文件，若是php、js这样的脚本文件，允许你执行（当然还是会有一些限制的）——这就好办了，那我就可以像在本地搭建网站那样，在GitHub服务器上搭建一个属于自己的网页，只需要在我的GitHub账号下创建一个GitHub Homepage类型的仓库，然后将我的这个网站需要的文件统统上传到这个仓库下就可以了

所以，如果想要利用GitHub Homepage搭建属于自己的博客，问题就变成了：

- 如何在本地把我的博客网站搭好？
- 如何把我在本地搭好的网站提交到GitHub上？

<a name="setup-local-website"><h2>2. 搭建本地博客网站 [<sup>目录</sup>](#content)</h2></a>

<a name="setup-hexo-website"><h3>2.1. 搭建Hexo博客网站 [<sup>目录</sup>](#content)</h3></a>

大家常用与博客网站搭建的开源框架就是WordPress了，但是WordPress比较大，这里我选择了用Hexo框架，Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

- **安装Hexo**

	Hexo 基于 Node.js，因此需要先安装 Node.js
	
	```
	$ https://raw.github.com/creationix/nvm/v0.33.11/install.sh
	$ sh install.sh
	```
	
	安装完成后，重启终端并执行下列命令即可安装 Node.js
	
	```
	$ nvm install stable
	```
	
	所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo
	
	```
	$ npm install -g hexo-cli
	```

- **Hexo使用**

	首先初始化博客
	
	```
	$ hexo init myBlog
	```
	
	会在工作目录下生成myBlog文件夹，该文件夹下的内容如下图所示：
	
	<p align="center"><img src=./picture/Setup-private-BlogSite-1.png width=600 /></p>
	
	接下来，进入文件夹 myBlog，输入
	
	```
	$ hexo s # hexo start的简写形式，两种写法都可以
	```
	
	这样你已经搭好了本地的博客网站，且启动了本地预览服务，在浏览器地址栏中输入`localhost:4000`即可查看
	
	<p align="center"><img src=./picture/Setup-private-BlogSite-2.png width=600 /></p>

	到这里，基于Hexo的博客网站就搭好了，剩下的就是如何编辑博客，以及如何修改Hexo主题

<a name="edit-blog"><h3>2.2. 编辑博客 [<sup>目录</sup>](#content)</h3></a>

博客文章保存在`myBlog/source/_post`文件夹下，注意文章类型得是**md格式**

可以执行下列命令来创建一篇新文章

```
$ hexo new [layout] <title>

例如：
$ hexo new testBlog
INFO  Created: ~/myBlog/source/_posts/testBlog.md
```

生成的md文件会自动生成以下的头信息：

```
---
title: testBlog
date: 2019-01-30 20:31:50
tags:
---
```

默认文章的标题与文件名一致，若想改动，比如在标题里加上一些汉字，则可以在头信息的`title`栏进行修改

编辑文章就可以直接使用vim编辑器对这个md文件进行修改

<a name="change-theme"><h3>2.3. 修改Hexo主题 [<sup>目录</sup>](#content)</h3></a>

<a name="deploy-to-github"><h2>3. 部署本地网站至GitHub [<sup>目录</sup>](#content)</h2></a>

首先，你得有一个GitHub账号，登录后，新建一个命名为`<username>.github.io`的仓库



<a name="extern"><h2>番外 [<sup>目录</sup>](#content)</h2></a>

<a name="load-local-image"><h3>本地图片加载 [<sup>目录</sup>](#content)</h3></a>

虽然在官方语法中，Markdown插入图片的格式是这样的：

```
![Alt text](/path/to/img.jpg)
```

然而，如果你用了Hexo框架，那你得小心了。

首先，你不可能在网页里用绝对路径，这样怎么部署到服务器上呢？所以必定是相对路径。Markdown本来的语法中，只要img和md文件的相对路径是对的就行，然而Hexo不知道对路径做了什么处理，你需要在source文件夹中新建一个images文件夹，然后把图片放在images文件夹里。路径也必须是这样的：

```
![Alt text](../../images/img.jpg)
```

---

参考资料：

(1) [【五分钟学算法】【新手向】从零开始搭建一个酷炫免费的个人博客](https://mp.weixin.qq.com/s/uxt3NX760gdNN-xld7fmZA)

(2) [Hexo中如何用Markdown插入本地图片](https://www.cnblogs.com/ccf-fly/p/4873379.html)
