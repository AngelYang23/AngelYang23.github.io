---
title: 操作手册之建立博客
date: 2023-12-13
updated: 2024-03-07
categories:
- Study
- blog_create
tags:
- blog
- config
description: 本篇博客记录我从零到一创建博客的所有操作过程：包括git相关操作，设置图床，还有hexo框架的搭建和使用。
copyright: true
toc: true
---

本人很懒，不多介绍

<!-- more -->

## 1.git代理

第一步首先处理好git网络问题。

### 代理配置

1.对http和https代理：

```
# http and https
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890

# socks5
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890

# unset
git config --global --unset http.proxy
git config --global --unset https.proxy
```

2.对ssh代理：

```
sudo apt install connect-proxy
# (or) https://github.com/larryhou/connect-proxy
vim ~/.ssh/config

# socks5
Host github.com
User git
ProxyCommand connect -S 127.0.0.1:7890 %h %p

# http || https
Host github.com
User git
ProxyCommand connect -H 127.0.0.1:7890 %h %p
```

3.打开 Git 的配置文件 `~/.gitconfig`，在文件末尾添加以下内容：

```
[http]
   proxy = http://代理地址:端口号
[https]
   proxy = https://代理地址:端口号
```

如果你的代理需要账号密码验证，可以使用以下内容：

```
[http]
   proxy = http://用户名:密码@代理地址:端口号
[https]
   proxy = https://用户名:密码@代理地址:端口号
```

```
[https]
	proxy = http://127.0.0.1:7890
[http]
	proxy = http://127.0.0.1:7890
```

参考：[Git配置代理和取消的两种方法 - 掘金 (juejin.cn)](https://juejin.cn/post/7216147029230993445)

### git clone 链接（免密登录 git push无需密码）

git可以使用四种主要的协议来传输资料: 本地协议（Local），HTTP 协议，SSH（Secure Shell）协议及 git 协议。其中，本地协议由于目前大都是进行远程开发和共享代码所以一般不常用，而git协议由于缺乏授权机制且较难架设所以也不常用。

最常用的便是SSH和HTTP(S)协议。git关联远程仓库可以使用http协议或者ssh协议。

**ssh：**

- 一般使用22端口；
- 通过先在本地生成SSH密钥对再把公钥上传到服务器；
- 速度相较慢点

**https：**

- 一般使用443端口；
- 通过用户名/密码授权，可用性比较高；
- 速度相较快点

一般企业防火墙会打开80和443这两个http/https协议的端口，因此在架设了企业防火墙的时候使用http就可以很好的绕开安全限制使用git了，很方便；而对于ssh来说，企业防火墙很可能没打开22端口。

**【使用区别】**

**clone项目：**

　　**使用ssh方式时，需要配置ssh key，即要将生成的SSH密钥对的公钥上传至服务器；
**

　　**使用http方式时，没有要求，可以直接克隆下来。**

**push项目：**

　　**使用ssh方式时，不需要验证用户名和密码，\**\*\*之前配置过ssh key，(如果你没设置密码)\*\**\*直接push即可；**

　　**使用http方式时，需要验证用户名和密码。**

 **总结：**

HTTPS利于匿名访问，适合开源项目，可以方便被别人克隆和读取(但没有push权限)；

SSH不利于匿名访问，比较适合内部项目，只要配置了SSH公钥极可自由实现clone和push操作。

#### 1.**【github上切换SSH/HTTP方式】**

<img src="https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122011305.png" alt="image-20231211222839311" style="zoom:67%;" />

#### 2.**【如何生成SSH密钥以及上传】**

<img src="https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122030728.png" alt="image-20231211222811442" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122031340.png" alt="image-20231211222925339-17023049665181" style="zoom:67%;" />

![image-20231211222941917](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122033088.png)

```
git config --global user.name 'yourname' 
git config --global user.email 'youremail'

// 如果之前已经设置了 那就不要加后面的yourname/youremail 直接获取即可
输入指令：ssh-keygen -t rsa -C “youremail”；
```

检验配置成功：

```
ssh -T git@github.com
```

![image-20231212211408011](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122114045.png)

**注意：**

按回车即表示默认这个文件路径名，接着又会提示你输入两次密码（**该密码是你push文件的时候要输入的密码，而不是github管理者的密码**），当然，你也可以不输入密码，直接按回车。那么push的时候就不需要输入密码，直接提交到github上了。

#### 3.**【http如何保存凭证信息】**

![image-20231211223158533](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122033897.png)

参考：[【git】git中使用https和ssh协议的区别以及它们的用法 - WANNANANANA - 博客园 (cnblogs.com)](https://www.cnblogs.com/wannananana/p/12059806.html)

## 2.git操作

常用操作：

```
git pull name branch
git add .
git commit -m "message"
git push name branch

git remote add name url(github repositories)
git remote remove name
git remote -v

git status
```

初始化git仓库（标准化流程）：

```
echo "# realVG" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:AngelYang23/realVG.git
git push -u origin main
```

```
git remote add origin git@github.com:AngelYang23/realVG.git
git branch -M main
git push -u origin main
```

Git commit规范：

```
<type>(<scope>): <subject>
```

```
type(必须)

用于说明git commit的类别，只允许使用下面的标识。

feat：新功能（feature）。

fix/to：修复bug，可以是QA发现的BUG，也可以是研发自己发现的BUG。

fix：产生diff并自动修复此问题。适合于一次提交直接修复问题
to：只产生diff不自动修复此问题。适合于多次提交。最终修复问题提交时使用fix
docs：文档（documentation）。

style：格式（不影响代码运行的变动）。

refactor：重构（即不是新增功能，也不是修改bug的代码变动）。

perf：优化相关，比如提升性能、体验。

test：增加测试。

chore：构建过程或辅助工具的变动。

revert：回滚到上一个版本。

merge：代码合并。

sync：同步主线或分支的Bug。

scope(可选)

scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

例如在Angular，可以是location，browser，compile，compile，rootScope， ngHref，ngClick，ngView等。如果你的修改影响了不止一个scope，你可以使用*代替。

subject(必须)

subject是commit目的的简短描述，不超过50个字符。

建议使用中文（感觉中国人用中文描述问题能更清楚一些）。

结尾不加句号或其他标点符号。
根据以上规范git commit message将是如下的格式：
fix(DAO):用户查询缺少username属性 
feat(Controller):用户查询接口开发
```

https://zhuanlan.zhihu.com/p/182553920

## 3.markdown图片上传

typora与[picgo](https://so.csdn.net/so/search?q=picgo&spm=1001.2101.3001.7020)下载与安装（想办法）

[PicGo](https://github.com/Molunerfinn/PicGo/releases)

### github操作：

1）**github创建新仓库作为图床**

1、如果没有GitHub的账号的话可以申请注册一个

2、登陆以后，点击右上角的 **+** 号，然后点击 new repository 新建仓库（这个仓库作为你的图床）

3、新建仓库时，仓库名必填（仓库名尽量不要有空格），私有性选择公开，然后点击 create repository

2）**新建token令牌**作为picgo的配置输入

1、点击右上角头像列表中 **Setting** 选项

2、进入后点击最后一项： **Developer setting**

3、进入后点击右边的最后一项，并且创建一个新的 **token**

4、在 Note 填写你的token的名字，将 repo 打上勾就行，其余的不用管，然后直接点击 绿色图标 Generate token。

5、完成后，一定要保存此token，因为只出现一次，一定要保存！！！！

![img](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122131035.png)

![img](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122131254.png)

![image-20231212213233077](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122132122.png)

![img](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122134388.png)

### PicGo操作：

1、下载完成后打开PicGo

2、首次使用时进入PicGo，点击设置Server，将监听窗口设为：36677。可保持默认。

3、点击左边图床设置进入GitHub图床。

4、设定仓库名，填写格式：你的GitHub名/新建的仓库名

5、设定分支名，填写自己想要填写的分支，也可以填写默认的 main

6、设定 Token，填写刚刚保存的 token令牌。

7、指定存储路径，默认为img/，这个路径是你GitHub的新建仓库中某一个文件夹的路径

8、自定义域名：这里使用 https://raw.githubusercontent.com/你的GitHub名/仓库名/master，这里的 master 是你的仓库下的一个分支

9、最后点击确定和设为默认图床。

设置时间戳命名图片

设置日志文件日志记录等级  全部-all

![image-20231212213748535](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122137567.png)

<img src="https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122138857.png" alt="image-20231212213834808" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122139649.png" alt="image-20231212213942607" style="zoom:67%;" />

### Typora操作：

1、设置完PicGo之后，打开Typora，找到文件中的 偏好设置。

2、插入图片第一项选择上传图片。

3、点击 图像 ，前三项 以及 最后一项 打钩。

4、将 上传服务改为PicGo（app）。

5、PicGo路径改为你的本地PicGo安装目录中的PicGo.exe文件

6、最后点击验证图片上传来验证。

7、查看 PicGo 相册 和 GitHub 同步的上传

8、在Typora中插入图片时，图片可以通过PicGo直接上传到GitHub仓库中，以后传输Typora文件时便不用再将图片一起打包上传。

9、配置代理

![image-20231212214311060](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122143113.png)

![image-20231212234527745](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122345866.png)

![image-20231212234156906](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312122341214.png)

```
"flags": [["proxy-server", "http://127.0.0.1:7890"]]
```

参考：[typora 设置代理 – SXJ的小站 (shenxiaojian.com)](https://shenxiaojian.com/set-proxy-for-typora/)

## 4.hexo

### hexo安装

安装hexo之前，需安装node.js，git

安装命令：

```
npm install -g hexo-cli --registry=https://registry.npm.taobao.org
```

检验是否安装成功：

```
node -v
npm -v
hexo -v
```

![image-20231213102451323](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312131024423.png)

**补充**(加速)

1.npm镜像网址：

```
npm 官方原始镜像网址是：https://registry.npmjs.org/
淘宝 NPM 镜像：https://registry.npm.taobao.org
阿里云 NPM 镜像：https://npm.aliyun.com
腾讯云 NPM 镜像：https://mirrors.cloud.tencent.com/npm/
华为云 NPM 镜像：https://mirrors.huaweicloud.com/repository/npm/
网易 NPM 镜像：https://mirrors.163.com/npm/
中科院大学开源镜像站：http://mirrors.ustc.edu.cn/
清华大学开源镜像站：https://mirrors.tuna.tsinghua.edu.cn/
```

设置镜像加速：

```
npm config set registry + 对应的镜像网址
```

查看镜像：

```
npm config get registry
```

2.npm安装命令：

```
npm install -g xxx --registry=镜像网址
```

3.nrm 是一个 npm 源管理器，允许你快速地在 npm 源间切换。（本人感觉很局限）

```
npm install -g nrm
nrm ls    
nrm use taobao/tencent
nrm test
```

![image-20231213110305278](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312131103322.png)

### hexo本地搭建博客

```
# First create
npm install hexo-cli -g(已做)
hexo init blog/hexo
cd blog/hexo
npm install
hexo server
# Existing project open
cd blog/AngelYang23.github.io
npm install
hexo server/hexo s
```

![image-20231213111908094](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312131119284.png)

![image-20231213112039870](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312131120931.png)

![image-20231213112557520](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312131125575.png)

参考：[Hexo](https://hexo.io/zh-cn/)

[Hexo 搭建与部署指南 - 掘金 (juejin.cn)](https://juejin.cn/post/7120189037104594980)

[2022 Hexo 博客搭建和使用教程(Windows) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/547520780)

### hexo样式设计

#### hexo项目目录

```
hexo-install-directory
├── CNAME
├── _config.yml  //Hexo的配置文件，可以配置主题、语言等
├── avatar.jpg
├── db.json
├── debug.log
├── node_modules
├── public	     //执行hexo g命令后，生成的内容会在这里，包括所有的文章、页面、分类、tag等.
├── scaffolds    //保存着默认模板，自定义模板就是修改该目录下的文件
│   ├── draft.md //默认的草稿模板
│   ├── page.md  //默认的页面模板
│   └── post.md  //默认的文章模板
├── source 		 //Hexo存放编辑页面的地方，可以使用vim或其他编辑器编辑这里的内容
│   ├── 404.html //自定义404页面，可以使用腾讯公益404页面
│   ├── Staticfile 				
│   ├── _drafts  //存放所有的草稿文件的目录
│   ├── _posts	 //存放所有的文章文件的目录，用的最多，比如执行hexo n "post_name"之后，post_name这篇文章就存放在这个目录下
│   ├── categories
└── themes		 //Hexo的所有主题
    ├── landscape //原始hexo主题
    ├── next     //这是我目前用的主题
```



**Front-matter** 是文件最上方以 `---` 分隔的区域，用于指定个别文件的变量，举例来说：（注意---隐藏）

```
---
title: 操作手册之建立博客
date: 2023-12-13  
categories:
- Study
- blog_create
tags:
- blog
- config
description: 本篇博客记录我从零到一创建博客的所有操作过程：包括git相关操作，设置图床，还有hexo框架的搭建和使用。
copyright: true
---
```

注意：hexo 报错YAMLException: end of the stream or a document separator is expected

可能是空格问题，冒号以后需要加空格

**hexo 分类和标签**

**1.YAML格式**

YAML中允许表示三种格式，分别是常量值，对象和数组

使用#作为注释，YAML中只有行注释。

基本格式要求：
1，YAML大小写敏感；
2，使用缩进代表层级关系；
3，缩进只能使用空格，不能使用TAB，不要求空格个数，只需要相同层级左对齐（一般2个或4个空格）

**分类(categories)**具有顺序性和层次性。Hexo 不支持指定多个同级分类。

**标签(tags)**没有顺序和层次。

```
categories:
- Diary
- Life
tags:
- PS3
- Games
```

```
categories:
- 分类
- 子分类
- 子子分类
```

**2.JSON Front-matter**

除了 YAML 外，你也可以使用 JSON 来编写 Front-matter，只要将 `---` 代换成 `;;;` 即可。

```
"title": "Hello World",
"date": "2013/7/13 20:46:25"
;;;
```

**文章截断**

在需要截断的地方加入：

```markdown
<!-- more -->
```

[Front-matter | Hexo](https://hexo.io/zh-cn/docs/front-matter)

[Hexo 搭建个人博客网站 (qq.com)](https://mp.weixin.qq.com/s/2Spv9KHO6mLHui0CFBmCnQ)



![image-20231213222240581](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312132222632.png)

![image-20231213223022175](https://raw.githubusercontent.com/AngelYang23/Picture/master/Pic/202312132230217.png)

#### 常见命令

```
hexo new 文章文件名                新建文章
hexo new draft 草稿文件名          新建草稿
hexo new page 导航选项页文件名      新建导航选项页界面
hexo new [layout] <title>        新建新文章或者新的页面
hexo publish 文章文件名			  发布草稿
```

#### 布局（Layout）

Hexo 有三种默认布局：`post`、`page` 和 `draft`。在创建者三种不同类型的文件时，它们将会被保存到不同的路径；而您自定义的其他布局和 `post` 相同，都将储存到 `source/_posts` 文件夹。

通过修改`_config.yml`的`default_layout`参数修改。

```
default_layout: post
```

在博客的`scaffolds`目录下有三个`md`文档。分别为`post.md` ， `draft.md`和`page.md`。

当您新建文章时，Hexo 会根据 scaffold 来建立文件。Hexo的模板是指在新建的文章文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。

`hexo`最大的优势就是可以结合`GitHub page`来搭建一个免费的个人博客系统，将文章都托管到`GitHub`上，在也不用担心服务器过期的问题了，而且可以自己买一个域名，解析了`GitHub`上就可以直接使域名访问了。很多小伙伴给自己个博客添加了很多其他的功能，但是每次写文章的时候都需要在文章的开头去写一遍，这简直就是重复造轮子。其实，你可以自己在`scaffolds`中去修改属于你自己的模板

#### 配置

在 Hexo 中有两份主要的配置文件，其名称都是 `_config.yml`。其中，一份位于站点根目录下，主要包含 Hexo 本身的配置；另一份位于主题目录下，这份配置由主题作者提供，主要用于配置主题相关的选项。

为了描述方便，在以下说明中，将前者称为 **`站点配置文件`**， 后者称为 **`主题配置文件`**。

Hexo 默认使用的主题是 landscape，对应 ./themes 目录下的 landscape 文件夹。可切换成其他 主题，选好之后，先将对应的主题 clone 到或下载好拷贝到 ./themes 目录下，再启用即可。建议使用 clone ，使用 clone ，假如有一天这个主题更新了，只需要 pull 一下就可以获取到最新样式了。

我用的主题：**Icarus** 

##### _config.yml

**网站**

```
title: Yang's log
subtitle: ''
description: 'Welcome to my blog, I'm AngelYang'
keywords: The good old days
author: Yang
language: zh-CN
timezone: ''
```

**文章**



[超全Windows10快捷键大全 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/127755045)

[VS Code 的常用快捷键 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/44044896)

[VSCode 快捷键（整理） - 悦光阴 - 博客园 (cnblogs.com)](https://www.cnblogs.com/ljhdo/p/13373208.html)

[SSH 三步解决免密登录_免密登录ssh-CSDN博客](https://blog.csdn.net/jeikerxiao/article/details/84105529)

[hexo-theme-icarus | Easy Hexo 👨‍💻](https://easyhexo.com/2-Theme-use-and-config/2-12-hexo-theme-icarus/#安装)

[Archives - Lei Mao's Log Book](https://leimao.github.io/archives/)

https://www.mad-coding.cn/2019/08/02/%E8%87%AA%E5%AE%9A%E4%B9%89hexo%E5%88%9B%E5%BB%BA%E6%96%87%E7%AB%A0%E7%9A%84%E6%A8%A1%E6%9D%BF/#0x02-%E4%BF%AE%E6%94%B9%E6%AD%A5%E9%AA%A4

https://shmilybaozi.github.io/2018/11/05/hexo%E6%96%87%E7%AB%A0%E6%A8%A1%E6%9D%BF%E8%AE%BE%E7%BD%AE/

https://www.cnblogs.com/eryoyo/p/16678333.html

[Hexo 主题配置 - Icarus (qq.com)](https://mp.weixin.qq.com/s?__biz=MzA4MzA2NDUxOQ==&mid=2247484078&idx=1&sn=2e2c698e3c17b62f46d668c278955867&chksm=9ffd6106a88ae810da69f20e204b2a9ec2f5020b5fbe58586333a02ce769b5091fefcc0c1e30&cur_album_id=1339610139661484036&scene=189#wechat_redirect)

[Hexo 主题配置 - Icarus_hexo icalus-CSDN博客](https://blog.csdn.net/cxy35/article/details/104854763)

https://wyh0517.github.io/2020/06/24/rule/

https://leimao.github.io/faq/
