---
title: building a podcast from zero
tags:
  - projects
date: 2025-01-15
summary: 本文为本人通过astro搭建博客的全过程。
---

本文记录了本人从零开始利用astro框架，gyoza主题将博客部署在GitHub pages上的全过程。部署之前本人对javascript有关项目架构，workflow流程一无所知，对git有不多的基础，对github pages一无所知。部署过程中经历了许多弯路，特此将学到的东西记录下来。为了方便，图片没有设置图床，但在自己的笔记内有完整的备份。如果有兴趣交流可以通过email来获取完整笔记。

# basic stuff you should know

## wget&curl

两者都是从某个链接下载东西和向某个链接上传内容需要用到的工具。wget是Linux等系统里具有的，而curl是macOS自带的。两者都支持使用不同的协议，比如HTTP等来下载。

## apt get&brew

前者是Linux系统的包管理器，后者是macOS系统常用的包管理器，全称叫Homebrew。

## npm&yarn&pnpm

两者都是js的包管理工具。yarn存在是为了弥补npm的缺点，下载的源头是一样的，还是从npm的仓库里获取资源。
pnpm是performance npm 可以理解为对前两者的优化，速度更快。![[attachments/Pasted image 20250112153350.png]]

## homebrew terminology

### formula and cask

理解为recipe，是下载一个软件时homebrew需要做的recipe。![[attachments/Pasted image 20250112224526.png]]![[attachments/Pasted image 20250112215732.png]]这就是一个formula。本质是一个ruby语言写的configuration file。
cask是GUI软件的formula，因为GUI软件和CLI软件的流程是不同的。

### cellar and prefix

cellar类似于warehouse，prefix类似于front desk。当用homebrew下载软件的时候，会创建两个东西，一个是在warehouse中的实际的文件，还有一个是在prefix里面的symbolic link，类似于指向warehouse中的实际的文件的指针，这样别的软件就可以通过这个指针来调用某个其他的软件。

- keg-only: when a formula is keg-only, Homebrew only puts it in the Cellar without creating those helpful signs in the prefix. This means other programs won't automatically know where to find it. 这么做的目的是系统可能具有不同版本的该软件，如果都被symlink会出现conflict。
  但是如果使用homebrew下载其它的软件有关于keg-only的dependencies，则homebrew会自动使用keg only的版本而不是系统中自带的版本。
  可以通过将文件的路径添加到PATH中进而方便调用，或者自己创造symbolic links。
- 当下载cask的时候，就类似于macOS里面的drag，直接将软件下载到了./applications 里面。
  > [!note]
  > 更详细的解释：
  > ![[attachments/Pasted image 20250112225611.png]]

对于astro需要用到的node.js而言，如果下载的node是keg-only的，则node自带的npm yarn等都无法直接使用。

### corepack

这是js环境下的packet manager manager，作用是确保对于同一个project使用同一类同一版本的packet manager。因为js环境下常用的packet manager有npm yarn pnpm等，所以如果使用不同的版本会出现问题。
![[attachments/Pasted image 20250112232221.png]]

## git process

- git clone url->git add .->git commit -m "message"->git push
- git init->git add .->git commit -m "message"->git remote add origin url->git push
  如果从本地的repository向remote repository push更改，需要先建立和remote repo的联系，通过git remote add xxx xxx。如果是git clone，就不用手动建立，系统自动建好了。
  还会涉及到身份验证，因为GitHub现在不支持password authentication，所以只能通过personal access token等方法来验证。<https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls>

## 由于DNS pollution git push在proxy情况下仍然无法成功

![[attachments/Pasted image 20250114110513.png]]
以上原因是用http协议来和GitHub建立连接的时候会面临的问题。所以接下来的解决方法是用SSH来建立连接。

### 官方文档

[Using SSH over the HTTPS port - GitHub Docs](https://docs.github.com/en/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)

### 视频教程

[开启VPN后使用ssh协议连接GitHub失败的解决方法\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1gk4y1M7KZ/?spm_id_from=333.337.search-card.all.click&vd_source=20cb3e7c6ad3d64f0eb2d763ff005080)

### 解决方法

#### 测试

```shell
ssh -T -p 443 git@ssh.github.com
```

#### 克隆

```
git clone ssh://git@ssh.github.com:443/YOUR-USERNAME/YOUR-REPOSITORY.git
```

#### 进行永久配置

编辑/创建文件：~/.ssh/config

```
Host github.com
    Hostname ssh.github.com
    Port 443
    User git
```

测试效果

```shell
$ ssh -T git@github.com
```

另外，[这篇博客](https://www.cnblogs.com/irobotzz/p/18577875)也有更详细切题的解决方法。本人先按照博客的内容完成了对本库的更改，从创建SSH key并保存在设备里，一直到将key和GitHub关联。接着按照上面的永久配置方法添加了config文件。

## .yml file

在github repo的.github/workflow下有许多.yml文件，他们一般都是GitHub action的一部分，目的是告诉GitHub如何自动化运行管理你的project。里面用的是yaml语言。
下面给出一个.yml 文件的实例：
![[attachments/Pasted image 20250114084849.png]]![[attachments/Pasted image 20250114084854.png]]
![[attachments/Pasted image 20250114084956.png]]
这里的jobs分为build和deploy。因此在GitHub action workflow里面就需要完成这两个任务。每个job运行的环境，运行的步骤都写在其中。

## commitlint

一些javascript的project的commit要求符合commitlint的规则，也就是"feat: xxx"这样的格式，否则会有如下报错信息：

```
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]

✖   found 2 problems, 0 warnings
ⓘ   Get help: https://github.com/conventional-changelog/commitlint/#what-is-commitlint
```

# create astro project

## 创建

npm create astro@latest
也可以用yarn create astro@latest. 这里遇到了一个问题，就是yarn v1 无法支持加tag，所以无法正确create。两个解决措施：yarn create astro或者将yarn升级到v2。升级的方法在yarn有专门的step by step。

## project file architecture

![[attachments/Pasted image 20250113085250.png]]

- src：source code
- public：on-code, unprocessed assets
- 其余：configuration files
  ![[attachments/Pasted image 20250113090241.png]]

## using an existing theme

我的theme是gyoza，部署在github pages上。步骤如下：

1. 将theme repo fork进自己的仓库，名字改为用户名.github.io。
2. 将其clone下来，方便更改内容。
3. 配置：按照 theme的doc来更改对应内容即可。一般都是在src里面的config.json里。
4. 部署：按照[astro部署在GitHub pages的官方文档](https://docs.astro.build/en/guides/deploy/github/)来操作。
   > [!attention]
   > astro官方文档里已经给出了astro部署所需的workflow的yml文件，所以在github pages选择workflow的时候就不要选择官方提供的astro.yml。以下为手欠选择了官方的，有2个yml后进行的漫长的debug流程。
   > astro.yml里面只写了当使用npm或yarn作为package manager的时候的操作逻辑和对应命令。如果使用pnpm的话（当前主题就使用的是pnpm）则需要添加对应的counterpart在astro.yml里面。具体来说，job里有好多steps，在detect package manager这一步，只写了yarn和npm以及对应的命令。下文调用上文step的输出也只受限于这两个，所以如果使用pnpm需要逐句更改寻找counterparts。

> [!note]
> js项目的运行逻辑：clone下来后，需要运行npm install或pnpm install。因为clone下来的project里通常都有一个文件叫package.json，里面有两个内容比较重要，dependencies和devDependencies，![[attachments/Pasted image 20250114112310.png]]
> 这两个内容描述了运行当前程序需要用到的dependencies有哪些，和对应的兼容版本号。
> ![[attachments/Pasted image 20250114112442.png]]
> install做的事情就是根据 package.json文件，生成依赖树，将依赖添加到node_modules，并且根据使用的package manager不同，再生成一个新的对应文件。npm会生成package-lock.json，yarn会生成yarn.lock，pnpm生成pnpm-lock.yaml。这三个文件里面是根据node-modules生成的详细的dependency的信息和版本号，类似于一个screenshot。
> 在GitHub action configuration file里的cache-dependency-path: \${{ env.BUILD_PATH }}/${{ steps.detect-package-manager.outputs.lockfile }}目的就是检查package manager生成的lockfile。检查完lockfile后做的事情是：![[attachments/Pasted image 20250114113043.png]]
> 这几个命令的特点就是执行的时候会temporarily下载这些dependencies，优化内存和时间，具体原理无需深究。
> 当项目中已有package-lock.json 文件，在安装项目依赖时，将以该文件为主进行解析安装指定版本依赖包，而不是使用 package.json 来解析和安装模块。因为 package.json 指定的版本不够具体，而package-lock 为每个模块及其每个依赖项指定了版本，位置和完整性哈希，所以它每次的安装都是相同的。package-lock.json 文件主要作用有以下两点：1.当删除 node_module 目录时，想通过 npm install 恢复所有包时，提升下载速度。2.锁定版本号，防止自动升级新版本。正因为有了 package-lock.json 文件锁定版本号，所以当你执行 npm install 的时候，node 不会自动更新 package.json 文件中的模块，必须用 npm install packagename（自动更新小版本号）或者npm install packagename@x.x.x（指定版本号）来进行安装才会更新，package-lock.json 文件中的版本号也会随着更新。

5. 在deploy.yml里面添加上package manager的信息，否则会报错。
6. 根据主题对应的repo的config.json来更改自己的网站。
