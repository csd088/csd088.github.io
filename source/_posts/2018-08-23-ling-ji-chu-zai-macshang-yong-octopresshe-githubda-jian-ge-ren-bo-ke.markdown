---
layout: post
title: "零基础在Mac上用Octopress和GitHub搭建个人博客"
date: 2018-08-23 14:03:40 +0800
comments: true
categories: 
---

本文主要是记录一个之前完全没接触过博客的新手，在Mac上使用[Octopress](http://octopress.org)和GitHub搭建个人博客的经历。
本人资质笨拙，参考[玉令天下](http://yulingtianxia.com/blog/2014/04/05/macosx10-dot-9shang-yong-octopresshe-githubda-jian-ge-ren-bo-ke/)、[单刀土豆](https://www.jianshu.com/u/c188a9c836b3)、[Vanbein](https://www.jianshu.com/p/37a6a63c21da)等大神的经验，花了差不多一天时间终于成功构建了一个简陋的个人博客。

<!--more-->
##环境搭建
1、安装Ruby环境，安装RVM同时安装最新Ruby（已安装的可跳过）

	curl -L https://get.rvm.io | bash -s stable --ruby

	
2、安装完，查看Ruby版本

	ruby -v
	
3、如果你的 Ruby 版本不低于 2.3.0，可直接跳下一步进行安装 RubyGems。否则需要执行如下命令

	rvm install 2.5.1
	rvm use 2.5.1
	

##安装Octopress

1、将 Octopress 的项目 clone 到本地，终端执行如下命令：

    git clone git://github.com/imathis/octopress.git octopress
	
	
2、安装完，进入octopress目录：

	cd octopress

3、然后执行（需科学上网）

	sudo gem install bundler
	bundle install
	
4、安装默认主题

	rake install
5、预览效果，这个时候已经搭建好一个简单的Octopress博客了

	rake preview
	
然后打开浏览器输入 [http://localhost:4000/](http://localhost:4000/)
正常结果会出现如下界面

![MacDown Screenshot](https://upload-images.jianshu.io/upload_images/635689-9a554909effc43fe.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

<mark>本地预览过一次发现修改后再次执行预览命令无效解决办法</mark>

终端输入
		
		lsof -wni tcp:4000 #找到进程的PID值
		kill -9 PID值
现在就可以刷新本地预览了
。
##本地Octopress博客部署到GitHub上去
### 1、新建GitHub repositiry

首先需要有个GitHub账号，没有的自行注册，登录GitHub后，新建**GitHub repository** ，项目名称（**Repository name**）命名格式为 **username.github.io** ，username 是你的 Github 用户名（或 organization name，这里和后面我们先不讨论 origanization）。例如我的用户名是 csd088，所以输入 csd088.github.io 即可。点击 **Create repository** 创建。

### 2、配置GitHub Pages
终端执行如下命令：
	
	cd octopress
	rake setup_github_pages

该命令会要求我们输入 Github 仓库的 URL 。复制粘贴下我们新建仓库的 SSH 或 HTTPS URL 即可。（例如：*git@github.com:username/username.github.io.git*），注意此处有坑，若是新手，选择了SSH后面会出现要求配置SSH Key问题，可能会导致很麻烦,主要是需要配置**publickey** 之后才能提交到GitHub

不然提交的时候会提示：
	
	Permission denied (publickey). 	fatal: Could not read from remote respository.
	
###配置publickey的方法

1、生成新的ssh key这里会提示要不要rewrite，键入y，还有提示输入passPhrase，输入空格即可
	
	cd ~/.ssh
	ssh-keygen
	
2、测试一下连接

	ssh -T -v git@github.com
	
不出意外的话这里会报错，最后几行提示信息如下：

	debug1: Offering RSA public key: yourpath/.ssh/id_rsa	debug1: Authentications that can continue: publickey	debug1: Trying private key:yourpath/.ssh/id_dsa	debug1: Trying private key:yourpath/.ssh/id_ecdsa	debug1: Trying private key:yourpath/.ssh/id_ed25519	debug1: No more authentication methods to try.	Permission denied (publickey).
	
提示信息表明路径 yourpath/.ssh下id_rsa文件存在，并且提供public key，然而无法和网站中的ssh-key匹配上。于是程序转而寻找yourpath/.ssh路径下的id_dsa文件，id_ecdsa文件，id_ed25519文件，看它们是不是能提供新的public key。
查看一下yourpath/.ssh，会发现下面根本不存在id_dsa，id_ecdsa，id_ed25519文件。 出错的原因其实在于步骤4更新了本地的ssh key，然而网站上的ssh key还为旧值，所以匹配不上，就报错Permission denied (publickey)

3、打开yourpath/.ssh/id_rsa.pub（.pub为公钥文件，id_rsa为私钥文件），复制里面的内容。 

4、打开[https://github.com/settings/profile](https://github.com/settings/profile)，在SSH key那栏选择New SSH Key，并将复制内容拷贝到其中，选择保存。

5、再次测试$ ssh -T git@github.com，提示 

```
	You've successfully authenticated, but GitHub does not provide shell access.
	
```
**错误排查完毕 ^_^**


到目前为止已经将 Octopress 生成的静态站点与 GitHub 进行绑定了

##创建文章
终端执行指令：

	rake new_post["title"]    #title为你的文章名，可随意更改
	
生成的新文章在source/_post/目录下，文件名构成为时间和标题的拼音。我们可以用Markdown编辑器对文章进行修改。
打开新建的 markdown 文件（我目前用的 MacDown 打开），会发现头文件有如下内容（千万不要删除这段信息）：

```
---
layout: post             #post代表是一篇博文
title: "hello world"
date: 2015-10-14 19:59:22 +0800
comments: true         #是否允许评论
categories:             #分类
---

//这边编写简介
<!--more-->   //这个以下的内容只有详情页才能看到
//这半边编写正文
```

终端用指定软件打开文件命令

 	open -a /Applications/指定程序　指定文件（文件夹）
 	
<mark>不知道软件所在的路径，可以直接把软件拖到终端就可以获取到</mark>


正文写完后，终端执行如下指令即可生成静态站点，：
	
	rake generate
	
如果你想预览本地的站点，可以执行终端指令：

	rake preview

此时，可以使用浏览器打开 localhost:4000 查看效果。如果没有问题可以将静态站点同步到 GitHub 远程仓库中，终端执行指令：

	rake deploy
	
你会发现我们的静态站点已经被 push 到 GitHub仓库的 master 分支上。稍等几分钟，访问博客地址 username.github.io ，就会发现你的个人博客站的第一篇blog已创建成功了。

如果你还想把自己的本地资源文件（如Markdown文件等内容）也同步到 GitHub 中，可以执行以下指令：


	git add .
	git commit -m "comment"  #comment可随意更改
	git push origin source


这样我们的资源文件就会同步到 GitHub 的 source 分支了。


##主要用到的几个命令解释

	rake new_post[’title’]     # 新建博文文件
	rake generate              # 将编辑好的博文生成网页
	rake preview               # 提交前可以进行本地预览
	rake deploy                # 将博文部署到Github上
	git add .		  				# 保存缓存文件
	git commit -m '注释'       # 提交本地更改的文件并注释
	git push origin source     # 将源文件push到Github的source分支





