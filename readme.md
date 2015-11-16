Git使用教程与基本原理
---------------------


[TOC]

## 认识Git

Q: 什么是GIT?

> Git是一个强调速度的分布式版本控制软件和源代码管理系统（SCM，source code management）。由Linus Torvalds为方便分布式开发linux而设计的管理软件。每一个Git工作目录都是一个带有完全历史记录和版本信息的仓库，不依赖网络和中央服务器。Git遵从GNU v2协议。

Q: 为什么要用GIT?

* 更顺畅的工作流程，开发过程中，完全可以离线操作
* 快速，Git分布式架构使得本地仓库包含所有的历史版本信息，你可以在不同的版本之间快速切换
* 弹性的本地分支，在SVN下，你建一个分支需要把源代码复制到另一文件夹下，在Git上你只需要一个命令
* 内容控元数据方式存储，所有的版本信息都位于`.git`目录下
* 完整性好，更易于协作开发
* 用户群大，现在已经有成千上万个开源项目采用Git来做项目管理，github上更是有无数个代码仓库

## 安装及初步使用

```bash
sudo apt-get install git;安装

git config --global user.name "xxx";设置用户名
git config --global user.email "xxx@xx.com";设置用户邮箱
git config --global core.editor vim;指定编辑器
git config --global merge.tool vimdiff;指定比对工作
git config --list;查看配置


ssh-keygen -t rsa;生成ssh连接公密钥，windows下请将文件放在git安装目录下的.ssh目录下
ssh git@github.com;测试ssh是否配转置成功
```


## Git仓库

Git做为一个资源管理和跟踪系统，如果想要把自己的文件托管在Git上，那么你得先让Git知道你需要管理的文件在哪。通过`cd`进入指定目录后，运行`git init`命令，就会在当前目录下生成`.git`目录，Git用来进行版本控制和内容跟踪的所有文件都在该文件夹下。
处于Git跟踪下的文件只有三种状态：

* Modified(working directory): 被修改过的文件
* Staged(staging area): 通过git add添加到暂存区域的文件
* Committed(git directory): 通过git commit提交到仓库的文件

所以，一般的git工作流程可能是这样：修改过某些文件，然后把这些文件都添加到暂缓区，再提交到仓库中形成一个版本或快照。最后提交到Git服务器上。而在中间，可能伴随分支管理，分支切换，撤消与合并。

可能有些人会觉得很奇怪，为什么Git会有暂存区域这个概念，直接提交到仓库中这就OK了。其实这是Git为了做版本控制用的。试想如果没有暂存区域，每修改一个文件，就会形成一个版本，太过频繁，不易于管理。暂存区域其实就是下一个版本的文件清单，你可以自由控制该往仓库中提交什么文件，这也可以避免在一个版本中包含一些中间文件，比如编译后的文件。

![git状态](git1.png)
![git状态](git2.png)
![git状态](git3.png)


## Git基本操作

### 初始化仓库

```bash
git init;//创建新仓库
git clone [url];//从远程仓库克隆
git clone [path];//从本地仓库克隆，如git clone /home/www/test
```

### 添加文件

```bash
git add filename1 filename2;按文件名添加
git add -A .;添加所有
git add filename1;在文件被修改后，需要再次使用git add才会被commit
```

### 添加一个版本

```bash
git commit -m "commit message";只commit本次add过的文件
git commit -am "commit message";//将所有以前添加过并修改过的文件commit进去
```
> 关于`-a`参数的意义

> 其实`git add`命令只是指定文件的当前状态添加暂存区域，并不代表一个文件一时旦添加，就会一直存在每个版本中。如果你添加一个文件后对这个又进行了修改，在你`commit`时候，只会`commit`这个文件添加时的状态，不会把之后的修改也`commit`进去，除非你再次添加

### 推送变更

在你commit完之后，你可能想把自己的代码提交到github或者其他git服务器上，与他人交流共享，这时候就需要和远程服务器打交道了。

如果你是在本地建立起的仓库，默认情况下是没有任何服务器地址的，如果你是从其他服务器复制过来的仓库，这个服务器地址会自动添加到你的仓库中，你可以这样查看：

```bash
git remote -v;
git remote;只会列出服务器的别名，不会列出地址
```

一个仓库可以有多个服务器地址，这就意味着，你可以从不同的人手中复制同一个仓库，但这并不会打乱你自己的分支。


合并其他代码到你的版本中，可以这样做：

```bash
git remote add code_a git@github.com:xxx/xxxx
git fetch code_a;也可以用pull code_a master
git merge code_a/master;fetch并不会合并代码
git push origin master
```

### 创建并管理分支

在做项目的时候，你可能会想写一些扩展性的功能，或者做一些小实验，但是你又不想影响你现在的项目。这个时候，你可以创建一个分支，然后在这个分支里写东西，当觉得不好的时候，你可以把这个分支删除掉，对你之前的主分支没有任何影响。或者你觉得这个新特性超出了自己的预想，可以合并到主分支里，这时候你只要把工作转回主分支，然后合并分支，最后删除分支，然后就跟没有那个分支一样。具体操作如下：

```bash
git branch test;创建一个test分支
git checkout test;切换到test分支,这两步操作也可以用git checkout -b test实现
;edit commit debug util merge

git checkout master;切换回master
git merge test;将test合并进master
git checkout -b test2;创建test2分支，并转到test2分支
git branch -d test;删除分支test
git branch;列出所有分支
git branch -v;列出所有分支及当前commit信息
```

> `git merge`实质是把两个版本合并在一起，然后在当前分支创建一个新的`commit`，如果你在两个分支的同一个文件的同一地方都做了修改，这时候`merge`就会失败，git就不会自动创建一个`commit`，而是直接停住。你需要手动修改这些冲突的文件，选择这两个分支中的一个版本，或者自己重写这个部分，然后手动添加这些文件到暂存区域，再`commit`就ok了。要查看哪些文件冲突了，可以用`git status`查看。

### 撤消改动

如果你提交太早，忘了添加某些文件，你可以这样做：
```bash
git commit -m "add something"
git add file1
git commit --amend -m "fix some bug";修改上一次的commit log
```

如果你添加了不该添加的文件，你可以这样挽回：

```bash
git add -A .
git reset HEAD readme.md;将reame.md中踢出Stage area
```

如果你编译错了一个文件，你想把它恢复到上一个版本的状态，你可以这样做：

```bash
git checkout -- readme.md;撤消这个文件的修改
git reset --hard HEAD^;滚回上一个版本
git reset --hard 版本号;滚回指定版本
```


## 高级用法

### 在Git中忽略文件

如果需要让Git忽略特定文件或目录，只需要简单的创建一个`.gitignore`文件，然后列出你不想让Git跟踪的文件和目录即可。你可以使用感叹号`!`来指出例外的情况。

```git
*.pyc
*.exe
my_db_config/
!main.pyc
```

### 查看代码改动情况

```bash
git blame filename
```

### 回顾仓库历史

可以使用`git log`,它有三个选项，你应该了解。

* `--oneline`   把每次提交间显示的信息压缩成只有hash和提交message的信息，在一行显示
* `--graph`     该选项会在输出界面的左手边用一种基于文本的图形表示法显示历史。如果你只是浏览一个单独分支的历史，那么这个功能是没有用的
* `--all`       显示全部分支的历史

### 绝不丢失一个提交信息

如果你提交了一个你不想要的代码，并且在之后你通过使用`git reset`使其回到了之前的状态。那么`git log`上的信息可能会丢失。而`git reflog`可以记录你的所有操作。

`git reflog`显示的是所有`head`移动的信息。记住，它是在本地的，而不是你仓库的一部分，不会包含推送`(push)`和合并中`(merge)`。

### 合并多次提交

当你提交你的代码进行审核并创建一个`pull request`时，你经常会在代码被采纳之前，要求修改一些代码。你进行了一些修改，而在下一次审核中，又会被要求进行另外的修改。你不知道还有多少次修改等着你，在你知道之前，你进行了多次额外的提交。理想的状态是，你可以使用`rebase`命令，把他们都合并成一次提交。

```bash
git rebase -i HEAD~2;合并最后两次提交，之后会进入交互界面，按提示操作
```

### 保存尚未提交的改动

如果你正在解决一个BUG或是添加某个新功能，这时你突然被要求展示你的工作。而你当前的工作还没完成到进行提交的地步，而且这个阶段你也没办法展示你的工作（如果不回退所有变化的话），那么`git stash`可以帮到你。stash本质上是**保存你全部的改动以供将来使用**。保存你的改用，你只需这样做：

```bash
git stash;暂存工作
git stash list;查看暂存列表
git stash apply;恢复这些改动
git stash apply stash@{2}恢复指定的序列号
```

### 检查丢失的提交

尽管reflog是一种查看丢失提交的方法，但是它在大型仓库中行不能。这时就该fsck(file system check)出场了。

```bash
git fsck --lost-found
```
这里你可以看到丢失的提交，你可以`git show [commit hash]`来查看这些提交所包含的改动或者是用`git merge [commit_hash]`来恢复它。

`git fsck`比`git reflog`有一个优势，如果你删除了一个远端分支并且克隆了仓库，使用`git fsck`可以搜索并恢复该远端分支。


### cherry-pick命令

`cherry-pick`是指从不同的分支里选择某次提交并且把它合并到当前的分支来。如果你在并行的开发某两个或多个分支，你可能会注意到有一个bug存在于所有的分支中。如果你在一个分支中解决了它，你可以使用`cherry-pick`来把这次提交合并进其它的分支而不会搞乱其他的文件或是提交。

用法：
```bash
git cherry-pick [commit_hash]
```


## 参考&拓展资料

http://backlogtool.com/git-guide/cn/

http://selfcontroller.iteye.com/blog/1786644

http://gitbook.liuhui998.com/index.html

http://blog.csdn.net/qinjienj/article/details/7816047

http://blog.chinaunix.net/uid-9185047-id-445215.html

http://blog.csdn.net/herbert5069/article/details/24197825

http://roclinux.cn/?p=622

http://blog.jobbole.com/75348/

http://blog.csdn.net/wengpingbo/article/details/8985132


