---
layout: post
title: 折腾Octopress的一点心得
date: '2015-11-20 16:51:05 +0800'
comments: true
abbrlink: 49040
categories:
---
折腾这款博客框架已经来来回回有一个多月了，网上的“教程”鱼龙混杂，以至于其中走了不少弯路（其实也让我对Ruby和Git的一些机制产生更加深刻的理解）。趁着这次出差事情不多，粗略的记录一下安装过程以及详细的爬坑过程吧。

##### 环境：Ubuntu14.04 LTS 
##### Ruby版本：1.9.3（官方推荐的是1.9.2，但是小版本的变动应该无伤大雅，至少在这个项目上暂时没发现什么坑）
##### Gem版本：2.4.8
##### Git版本：1.9.1

<!--more-->
网上的Octopress有很多都是附带讲解了如何安装Ruby及相关，但是个人情况不同，所以有很多不同问题出现让人困惑。这里建议直接去RubyChina的论坛中查找安装ruby环境的教程，那里的更加细致。[RubyChina：如何快速正确的安装 Ruby, Rails 运行环境](https://ruby-china.org/wiki/install_ruby_guide) （这里不是缺点的缺点有两个。首先，你必须是Ubuntu或者Mac；其次，里面有一些安装库的地址已经变了，你得自己去论坛里问别人，但这也利于你培养学习的习惯。）

（安装完环境之后记得把gem的更新源换成国内的，这样速度很好很多，也不容易被和谐）
```
gem sources -a https://ruby.taobao.org/
gem sources -r https://rubygems.org/
gem sources -l
```
如果出现
```
*** CURRENT SOURCES ***

https://ruby.taobao.org/
```
那么说明更换成功。
（有些教程上是http，可能是以前能访问，但是现在更新某些东西时无法访问）

等你的环境都准备好了就可以开始从git上拿下来Octopress的项目了。
### 1. 克隆项目。
找到你想克隆的文件夹，输入命令：
``` 
    git clone git://github.com/imathis/octopress.git  octopress
```
后面的octopress可以随便写，这是项目clone下来存在于文件系统的名称。（你可以理解为clone下来的文件夹名称）
### 2. 更换使用的ruby版本以及项目的Gem更新地址。
- 进入octopress，进来的第一件事是使用rvm（或者你安装的ruby版本管理器是rbenv）命令把正在只用的ruby换成这个项目需要的版本，我的是
```
    rvm use 1.9.3@rails31
```
再次声明此项目官方文档中是需要用ruby1.9.2，也就是是说这条命令需要你根据自己安装的环境自己定制。

- 第二件事是更换掉Gemfile里面的源，打开Gemfile把最上面那行的地址也换成`https://ruby.taobao.org/`，原因与上面换Gem的源一样。
### 3.安装依赖。
    依次输入以下命令：
```
    gem install bundler
    bundle update
```
在安装依赖的时候肯定会有一些红色或者黄色的提示一些父类依赖没有安装以致项目所需的依赖无法安装，那么遵循给出的依赖名称在命令行输入bundler intall xxxx(依赖名称)，然后再一次执行bundle update。这样一直到出现绿色的提示Bundle updated!为止。
### 4.生成站点和博客。依次执行。
```
    rake install
    rake generate
```
- 第一个命令是用来安装Octopress默认主题的。
- 第二个命令是生成站点静态页面的。

有兴趣的同学可以看这两个命令执行时rake都帮我们执行了哪些命令来创建这些东西。当然你要是觉得眼花缭乱可以选择不让它输出，但是命令我不会告诉你，自己去找。
### 5.生成测试博客。
执行到这里你的站点基本搭建就已经完毕了，接下来你可以生成一片测试博客以供一会预览看到效果。
```
    rake new_post['ThisIsTestBlog']
```
这是生成基本博客模板的命令。通过输出可以看到，在source下创建了_posts文件夹，然后`Creating new post :source/_posts/年-月-日-ThisIsTestBlog.markdown`，这就是你的测试博客文章，要添加文字就在这个文件里。
### 6.预览。到这里你就已经成功了一半，Octopress提供了本地预览（部署）博客命令。
```
    rake preview
```
这个命令我将它理解为启动本地跑Octopress的服务器，接下来你会看到一些输出，（做javaEE开发的可以近似的将之理解为Tomcat启动时的那些基本输出），接着你在浏览器输入地址`localhost:4000/`,如果你的命令执行的没问题，那么你可以立即看到浏览器收到回应开始加载，如果浏览器一直处于请求阶段，没有回应，那么就说明你的命令执行的有问题。（特别提示：我之前一直没有部署成功，仔细检查部署命令后的那些输出，发现很多Error的输出，去stackoverflow才找到的答案，最常见的就是xxxx依赖没有安装。如果你的命令执行完之后出现了很多输出，那就可以断定是有问题，因为成功的部署的输出只有十来行。）
### 7.申请仓库。
你的博客不可能只有你一个人或者只让本地的人看，所以你将之放到Github上去供更多的人浏览，你需要一个放博客的Repo。
在Github申请仓库，这部对于经常玩Github的同学应该很熟了。（怎样申请Github不是本文的重点，比注册邮箱还要简单）。但是重点来了：

- 这也是之前一直让我困恼很久的问题！申请仓库的格式必须是：github用户名+github.io。比如你在github叫zhangsan，那么你的仓库名称应该是：`zhangsan.github.io`，一定是这个名称，不然你输入你博客的域名永远都是404！（最开始有些人说是部署有时间延迟，不会立即生效，要等一段时间，结果我等了一个星期，等来的却是404。正确的部署之后延迟只是几分钟，至少我后来部署了不下于5遍，没有一次延迟超过五分钟）

- 仓库名称一定是以io结尾。有些老教程上说一定不要以io结尾，但是github后来好像有一次改版，把这种项目的名称都限定以io结尾，并且octopress的官方部署教程也更新为要以io结尾,如果你不用这种命名方式，等你的依然是404。[Octopress官方文档](http://octopress.org/docs/deploying/github/)

### 8.关联。这是成功前的倒数第二步，关联你的本地博客和仓库。
```
    rake setup_github_pages
```
接着把你的仓库地址粘贴上去，可以SSL的那个仓库地址，也可以用HTTP的那个仓库地址。
这部产生的输出是强烈建议去研究一下的。项目首先将git的默认地址改为你的仓库地址（之前默认的是octopress官方Github仓库地址），这样推送就是往你的仓库里推了，然后将原先的master分支（原先仓库地址的master分支）改名为source（这个source是给你以后放源代码用的）。
####9.把你的博客部署到Github上去。
```
    rake deploy
```
查看输出，发现其实部署的内容都放在了一个项目根下一个叫_deploy的文件夹中，然后在_deploy中生成了一个master分支，这个master分支不同于上一步的原生master分支，是专门放你部署的内容的，你当然不用手动利用git命令给这个分支推送，因为rake deploy命令就是干这个的。
我有个一直的疑惑就是，第一次和远程库关联这个master分支会出现提示输出`....no tracking information , if you want to specify ...., fix it with --set-upstream-to branch origin/<branch>`。（大概就是这些，我记不太清了）。读了廖雪峰老师的[Git新手教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013760174128707b935b0be6fc4fc6ace66c4f15618f8d000)的多人协作部分，我的理解是，这个命令的部署的机制是，先把远程库里这个分支的最新内容拉下来合并（多人协作开发为了避免冲突都是这样）。而此时的远程库中并没有这个分支，所以出现了这个分支没有上游的提示。以后就没有了。（不知道我的理解对不对，欢迎讨论）。之后再把新生成的东西一并推送到远程库中。
再过五分钟，你就可以访问你在Github上的博客了，域名就是你的仓库名称：`http://zhangsan.github.io`
### 10.将源码及配置文件放到Github上去。
```
    git add .
    git commit -m 'your annotation'
    git push origin source
```
如果你不想你的源代码无故丢失，那么每次写完文章或者配置了新的属性那么将源文件及时推送到Github上去是个好习惯。

### 总结
好了，到这里本次Octopress配置就要告一段落了，剩下的就只是优化，网上的教程有很多。提升访问速度就是去掉跟谷歌和Twitter的相关设置以及将ajax的源文件换成国内的链接；美化的教程更是数不胜数，就不在这里多赘述了。通过研究这个项目最重要的是让我对Ruby和Git有了更深的了解。最后祝本文的读者在Octopress玩的开心。^.^...

### 参考

- 修改_config.yml文件以及ajax源提升访问速度：[使用 Octopress+GitHub Pages 搭建个人博客](http://www.leichunfeng.com/blog/2014/11/11/use-octopress-plus-github-pages-to-setup-a-personal-blog/)

- 美化Octopress：[Octopress博客发布后的基本配置、继续阅读、自动打开、代码高亮....](http://blog.lessfun.com/blog/2013/12/05/config-the-octopress-blog-after-deployed/)


