<a name="content">目录</a>

[树莓派服务器使用说明](#title)
- [raspi-server简单介绍](#introduction)
- [目前已经搭建的服务项目](#server-items)
	- [ssh远程登录服务](#ssh)
	- [Web服务器](#web)
	- [其他服务](#another)
- [针对《生物信息学应用》课程的使用说明](#bioinfo-course)



<h1 name="title">树莓派服务器使用说明</h1>

<a name="introduction"><h3>raspi-server简单介绍 [<sup>目录</sup>](#content)</h3>

这是一个搭建在树莓派上的Linux服务器，安装的Linux操作系统为raspbian（基于Debian-8-jessie开发的树莓派定制版），想了解什么是树莓派，请点 [这里](http://shumeipai.nxez.com/intro-faq)

由于服务器配置不高 —— 配置还略低于目前普通的安卓机，所以请不要在该服务器上处理较消耗服务器资源的任务，为了服务器的稳定运行，**请不要在服务器上作妖**

<a name="server-items"><h3>目前已经搭建的服务项目 [<sup>目录</sup>](#content)</h3></a>

<a name="ssh"><li>ssh远程登录服务</li></a>

可以用ssh终端登录服务器，前提是你已经有服务器的账号密码

不知道什么是ssh？我教不了你，告辞...

推荐一个好用的ssh终端：[secure shell client](http://ultra.pr.erau.edu/~jaffem/tutorial/SSH_secure_shell_client.htm) ，`secure shell client = putty + winscp` 一个顶俩，还是开源免费的工具，**强烈安利**

<a name="web"><li>Web服务器</li></a>

基于Apache2搭建的Web服务器，其**工作目录**在服务器的绝对路径为：`/var/www`

目前工作目录下的所有文件，任何人都可以通过浏览器访问，但是只有拥有服务器账号，且属于Web-server组的用户才有写权限，如果当你在往该目录下写入文件失败，可能是你没有该目录的写权限，检查一下自己是否属于于Web-server组

```
$ groups
```

<a name="another"><li>其他服务</li></a>

目前正在搭建wordpress博客网站，敬请期待

<a name="bioinfo-course"><h3>针对《生物信息学应用》课程的使用说明 [<sup>目录</sup>](#content)</h3></a>

服务器IP在微信群里发布，如果IP失效，请向管理员索要新IP

ssh终端登录服务器，端口为22，即ssh终端默认端口，不需要修改

数据库文件的目录为`/var/www/bioinfo-course`，请在该文件夹下新建一个属于自己的文件夹，并修改新建文件夹的权限，去除组内其他用户的写权限

```
$ mkdir /var/www/bioinfo-course/your_new_dir
$ chmod g-w /var/www/bioinfo-course/your_new_dir
```

要用浏览器访问自己目录下的html等文件，请在浏览器地址栏输入：`服务器IP:服务器web服务端口(9000)/bioinfo-course/your_new_dir/your_file`
