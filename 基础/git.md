# git

![git](https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=2731384769,1190549896&fm=26&gp=0.jpg)

git是一个版本控制工具，svn是集中式版本控制工具，git是分布式版本控制工具。

集中式版本控制工具都有一个单一的集中管理服务器，所有的文件和代码都保存在服务器里，缺点在于中央服务器的单点故障，服务器一旦发生故障，可能导致数据永久丢失且宕机期间所有人都不能工作。

分布式版本控制工具的思想是去中心化，每一台工作的电脑都是服务器，分布式不只是提取客户端最新版本的文件快照，而是把代码仓库完整的镜像下来，如果任何一处服务器发生故障时候都可以用任何一个镜像出来的本地仓库恢复，并且普通的分布式版本控制在管理版本时存储的是不同项目之间的差异，存储的是索引用于节省硬盘空间，但缺点在于回滚时需要一个一个版本的回退，速度很慢。

**git为了回滚时速度更快，每次版本都存储的完整的项目代码，git的压缩算法可以使得整个存储空间只比集中式版本工具和存储差异的分布式多一点，所以每个客户端都可以放下每个版本的完整代码，而不是存储版本差异。而且git支持非线性开发模式，可以有上千个并行开发的分支（客户端）。**



## github

github是一个网络远端的代码仓库，可以让不同地点的人协同工作和储存代码。

使用github进行团队协作，哪怕github挂了，每个客户端保存的也都是整个完整的项目和历史记录。



## git安装

- 下载

- Git Gui Here       图形化界面git

  **Git Bash Here	命令行操作git**

  **Git的命令行采取linux命令，所以单独使用一个命令行窗口，而不是dos命令行窗口**

- 命令`git--version`  查看git版本         



## git初始化配置

一般我们都需要配置一下本地git客户端的工作环境，修改下配置，git提供一个`git config`的命令修改配置

配置文件一共储存在项目三个地方

​		`/etc/gitconfig`  文件：**系统中对这个操作系统所有用户都普遍适用的通用配置**，会存储进操作系统，如果使用`git config`时使用`--system`选项，就读写这个文件。优先级第三。

​		`~/.gitconfig`文件：**用户目录下的配置文件只适用于该用户**，会存储进操作系统的用户文件夹，如果使用`git config`时使用`--global`选项，就读写这个文件。优先级第二，一般配置用户配置即可。

​		`.git/config`文件：当前项目的git目录中的配置文件（也就是工作目录下的`.git/config`文件），这个配置仅对当前项目有效。优先级最高。

```
提交代码留下的用户名
$ git config --global user.name "user.name"
提交代码留下的邮箱
$ git config --global user.email "user.email"
检查已有配置
git config --list
```



## 区域与对象

- 区域

  ​	工作区：本地的代码工作的沙箱环境

  

  ​	暂存区：暂时存放的代码，还未提交到版本库，待彻底完成后提交

  

  ​	版本库：存放完整的历史记录的代码



- 对象

  **git对象**

  ​	git的核心部分是一个简单的键值对数据库，你可以向该数据库插入任意类型的内容，他会返回一个兼职，通过该键值可以在任意时刻再次检索该内容

  
  
  **树对象**
  
  ​	对象类型是一个树对象，树对象里面的内容是暂存区的快照（项目的快照）
  
  ​        
  
  **提交对象**
  
  本质就是给树对象做一层包裹包含项目的基础信息，真正代表一个版本的是一个提交对象（数据和基本信息）这是一个链式的，暴露在外的只有提交对象



一个完整的流程最少包含一个git对象，一个树对象和一个提交对象

add的文件先生成一个git对象，暂存区中的快照生成一个树对象，提交时加上各种信息（如姓名，邮箱）生成这个版本的提交对象。

一次提交，树对象和提交对象只能有一个，git对象可以有很多，每次add一个修改文件就会生成一个git对象，并且不会覆盖之前这个文件的git对象





# git原理

git通过计算哈希来验证文件

哈希算法会给一个文件得到一串字符串，git通过计算hash来验证这个文件有没有被更改。



# linux命令

git命令行可以使用linux命令进行操作文件

所有linux开头都有`$`符号

- $ clear ： 清除屏幕



- $ echo 'test content'  :  往控制台输出信息



- $ mkdir 文件夹名  ：创建一个文件夹



- $ touch 完整文件名  ： 创建一个文件



- $ ll  ： 将当前子文件&子目录平铺展示在控制台



- $ find 目录名  ： 将对应目录下的子孙文件&子孙目录平铺在控制台



- $ find 目录名 -type f   ：   将对应目录下的文件平铺在控制台



- $ rm 文件/目录名 参数   :   删除文件

  ​				参数列表

  ​				-d：直接把欲删除的目录的硬连接数据删除成0，删除该目录；
  ​                -f：强制删除文件或目录；
  ​                -i：删除已有文件或目录之前先询问用户；
  ​                -r或-R：递归处理，将指定目录下的所有文件与子目录一并处理；
  ​                --preserve-root：不对根目录进行递归操作；
  ​                -v：显示指令的详细执行过程；

- $ mv 源文件  重命名  ： 重命名文件



- $ cat 文件的url  ： 查看对应文件



- $ vim文件的url   ：进入vim文件编辑器

  ​	(英文模式下)按 i 进入插入模式，进行文件的编辑

  ​	(英文模式下)按 esc键&按 . 键，进行以下命令的执行

  ​									 q!   强制退出（不保存）

  ​									 wq  保存并退出

  ​									 set nu 设置行号										





# 初始化新仓库

```
$ git init
$ git config --global user.name "wtw"
$ git config --global user.email 1021868716@qq.com
$ git config --list
```

会在运行命令的目录下创建一个隐藏的目录`.git`，这个就是最终的版本库

`.git\`下有很多子目录和子文件

`.git\hooks目录` hook是钩子的意思，类似于js的回调，在提交代码之前做什么操作，提交代码后要执行什么操作都在这个文件夹里配置，例如代码规范检查等，都写在hooks文件夹下的许多sample文件里

`.git\info目录`    包含一个全局性的排除文件，哪些文件git无需管理的可以在这里进行配置

`.git\logs目录`    保存git日志文件

**`.git\Objects目录 `   仓库区，存储了当前项目所有历史记录代码，储存了所有数据内容**

**`.git\lrefs目录`     存储指向数据的提交对象的指针（分支）**

`.git\config文件`     当前项目的项目config配置

`.git\description文件`    显示当前仓库的描述信息

**`.git\HEAD文件`     文件指示目前被检出的分支**

**`.git\index文件`     保存暂存区信息，在没提交之前，暂存区里的内容都算一个版本**



# git忽略

git目录同级的`.gitignore` 文件中书写的文件夹或者文件都会被git忽略

```
node_modules/
docs/.vuepress/dist/
```

就会忽略node_modules文件夹中的文件以及docs/.vuepress/dist文件夹中的文件



# 增删改查

- **git status**

检测工作目录下的文件

处于什么状态：未跟踪（untracked），已跟踪。已跟踪文件的状态又分为：已提交，已修改和已暂存（staged）。**工作目录下的文件的状态分为未跟踪和已跟踪，当第一次被add进暂存区后，状态就变为已跟踪。**



之后每次再修改这个已跟踪文件，这个文件就会处于已修改状态，再次add就会变为已暂存状态，commit提交后变为已提交状态。



- $ git config --list

查看当前配置信息



- **git init** 

  初始化git仓库



- **git add .**   

  提交全部修改了的工作目录到暂存区，这一步会生成git对象和树对象。

  

  提交全部修改文件转变为git对象并放到版本库，再把git对象从版本库中拿出来放到暂存区

  `.`代表全部修改了的文件，`.`可以替换为文件名提交具体文件，工作目录每修改一个文件就会新生成一个git对象，并且不会覆盖之前的git对象。最后会生成一个暂存区镜像树对象。



- **git commit -m "提交注释"**

  提交修改从暂存区到仓库区

  提交并不会清除暂存区的内容，这一步会生成提交对象，提交之后此时此刻的版本就被留存在历史记录中。

  

- git diff

  查看哪些文件还没暂存

  

- git diff --staged

  查看哪些文件被修改或暂存 但还没被提交

  

- **$ git commit -a**

  从工作目录直接提交修改到仓库区，跳过add到暂存区这一步，没有被跟踪过的文件不能使用这个命令，需要先被add过一次跟踪过以后才能使用这个命令直接提交

  

- $ git log

  打印git操作日志，并输出个版本的注释和哈希索引值
  
  - $ git log --pretty=online
  
  - $ git log --online
  
    这两个命令是更简洁的方式去打印git操作日志
  
  - **$ git reflog** （**推荐使用这个打印日志**）
  
    打印日志的同时记录从当前版本回到该版本需要几步
  
  
  
- $ git rm 文件

  提交文件到暂存区并删除工作目录中的文件

  

- $ git rm --cached [file]

  停止追踪指定文件（从暂存区中删除文件），但该文件会保留在工作区

  

- $ git mv 原文件名 新文件名

  将工作目录的文件名更换为新文件名，并提交到缓存区






# HEAD指针

HEAD指针指向我们当前浏览的版本，我们可以通过操作HEAD指针来控制版本的前进或回退，HEAD指针切换，工作目录中的文件就会切换到对应版本。



HEAD指针有三种操纵方式，**基于索引值**（推荐），使用^符号，使用~符号

- $ git reset --hard hash

  HEAD指针切换到哈希值所代表的版本（各版本的索引值可以通过打印日志来获取）

- $ git reset --hard HEAD^ 

  这个方式切换版本只能往后不能往前进，表示往后退一个版本，有几个^表示回退几个版本

- $ git reset --hard HEAD~1

  这个方式切换版本只能往后不能往前进，表示往后退1个版本，数字是几回退几个版本



$ git reset不止有--hard这一个参数，一共有三个参数

- --hard    移动HEAD指针，并切换暂存区和工作目录到对应的版本
- --mixed 仅仅只是移动HEAD指针，不会去切换工作目录的版本，会切换暂存区到对应版本
- --soft     仅仅只是移动HEAD指针，不会去切换工作目录和暂存区的版本



## 恢复删除

使用rm命令永久删除了工作目录下的一个文件，只要这个文件已经commit到版本库，就通过切换版本可以恢复

`$ git reset --hard hash`切换到还拥有这个文件的版本，就恢复了这个文件







# 比较文件差异

- $ git diff 文件

  比较文件与暂存区中的该文件之间的差异

- $ git diff 指针/hash 文件

  比较文件与指针或者hash指向的版本中工作区的该文件之间的差异

不指定文件名则会比较工作区中所有文件的差异





# 分支

主分支：master



分支可以把工作从开发主线上分离开来，以免影响主线开发，可以用来团队开发。其他版本控制系统的分支需要将主线下载到本地，创建一个主线源代码副本，及其耗时。

但git的分支系统高效轻量。 他会从主干上复制一份源代码到分支上



分支流程：
开发新需求时创建一个分支，在这个分支上开展工作，如果此时需要修复线上版本的问题，则切换到线上分支，然后为这个问题单独开一个分支并修复这个问题，测试通过后切回线上分支，并合并这个修补分支，最后推送到线上版本，最后再回到原新需求分支开展原本的工作。



分支是一个指向当前项目提交对象的指针，分支所在提交对象更新了他也会更新，永远指向创建他的提交对象的最新版本，默认的分支是master

一个项目可以有多条分支，每条分支做不同的事情，最后合并。

git文件夹下HEAD文件夹就指向目前被检出的分支，refs文件夹下存储指向数据的提交对象的指针（分支）

HEAD指向我们当前访问的提交对象（版本）所在的分支，我们切换分支访问其他版本时，HEAD就会指向其他分支所在版本的提交对象。

**当我们提交版本时，HEAD会带着当前所在分支一起向前移动到最新的提交对象（版本），其他分支不会移动依然指向创建分支所在的提交对象，通过切换分支可以达到切换版本的目的**

分支切换会改变工作目录中的文件，切换到一个旧的分支时，工作目录会回到该分支最后一次提交的样子。所以如果，每次切换分支三个地方会发生改变，HEAD（指向切换到的分支），暂存区和工作目录，**所以我们切换分支之前需要先将工作目录和暂存区的修改提交了，如果当前分支有未暂存的修改或者未提交的暂存，就去切换分支，可能会污染其他分支。**



- $ git branch 分支名

  **创建分支，会在当前所在的提交对象上创建一个分支**



- $ git checkout 分支名

  切换分支
  
  

- $ git checkout -b 分支名

  创建一个分支并切换过去

  

- $ git branch

  获取所有分支列表



- $ git branch -D 分支名

  -d 只能删除空分支
  
  -D 强制删除分支，不管分支是否有内容
  
  删除分支，但不能删除当前所在分支，只能checkout到其他分支后才能删除当前所在分支



- $ git branch -v

  查看每个分支的最后一次提交



- git log --oneline --decorate --graph --all

  查看项目分叉历史



- $ git branch 分支名 commitHash

  新建一个分支并使分支指向commitHash对应的提交对象（版本）

  用于切换版本，可以开一个分支指向之前的版本，获取之前版本的内容

  如果用不上了，可以再将这个分支删除掉



- $ git merge  被合并分支名

  当前分支与被合并分支进行合并

  

## 解决分支冲突

但两个分支出现分歧（具有同一个文件但内容不同）时文件内会把两个文件合并显示出来，我们需要将文件手动编辑成想要的样子，合并之后需要先add添加到缓存区然后commit进行提交





# 配置别名

某些git命令太长了，可以通过git --config文件来配置别名

- $ git config --global alias 别名 原命令

  ```
  $ git config --global alias co checkout
  将$ git checkout 的别名设置为$ git co
  ```








# git常用命令

## 一、新建代码库

> ```bash
> # 在当前目录新建一个Git代码库
> $ git init
> 
> # 新建一个目录，将其初始化为Git代码库
> $ git init [project-name]
> 
> # 下载一个项目和它的整个代码历史
> $ git clone [url]
> ```

## 二、配置

Git的设置文件为`.gitconfig`，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

> ```bash
> # 显示当前的Git配置
> $ git config --list
> 
> # 编辑Git配置文件
> $ git config -e [--global]
> 
> # 设置提交代码时的用户信息
> $ git config [--global] user.name "[name]"
> $ git config [--global] user.email "[email address]"
> ```

## 三、增加/删除文件

> ```bash
> # 添加指定文件到暂存区
> $ git add [file1] [file2] ...
> 
> # 添加指定目录到暂存区，包括子目录
> $ git add [dir]
> 
> # 添加当前目录的所有文件到暂存区
> $ git add .
> 
> # 添加每个变化前，都会要求确认
> # 对于同一个文件的多处变化，可以实现分次提交
> $ git add -p
> 
> # 删除工作区文件，并且将这次删除放入暂存区
> $ git rm [file1] [file2] ...
> 
> # 停止追踪指定文件（从暂存区中删除文件），但该文件会保留在工作区
> $ git rm --cached [file]
> 
> # 改名文件，并且将这个改名放入暂存区
> $ git mv [file-original] [file-renamed]
> ```

## 四、代码提交

> ```bash
> # 提交暂存区到仓库区
> $ git commit -m [message]
> 
> # 提交暂存区的指定文件到仓库区
> $ git commit [file1] [file2] ... -m [message]
> 
> # 提交工作区自上次commit之后的变化，直接到仓库区，跳过暂存区
> $ git commit -a
> 
> # 提交时显示所有diff信息
> $ git commit -v
> 
> # 使用一次新的commit，替代上一次提交
> # 如果代码没有任何新变化，则用来改写上一次commit的提交信息
> $ git commit --amend -m [message]
> 
> # 重做上一次commit，并包括指定文件的新变化
> $ git commit --amend [file1] [file2] ...
> ```

## 五、分支

> ```bash
> # 列出所有本地分支
> $ git branch
> 
> # 列出所有远程分支
> $ git branch -r
> 
> # 列出所有本地分支和远程分支
> $ git branch -a
> 
> # 新建一个分支，但依然停留在当前分支
> $ git branch [branch-name]
> 
> # 新建一个分支，并切换到该分支
> $ git checkout -b [branch]
> 
> # 新建一个分支，指向指定commit
> $ git branch [branch] [commit]
> 
> # 新建一个分支，与指定的远程分支建立追踪关系
> $ git branch --track [branch] [remote-branch]
> 
> # 切换到指定分支，并更新工作区
> $ git checkout [branch-name]
> 
> # 切换到上一个分支
> $ git checkout -
> 
> # 建立追踪关系，在现有分支与指定的远程分支之间
> $ git branch --set-upstream [branch] [remote-branch]
> 
> # 合并指定分支到当前分支
> $ git merge [branch]
> 
> # 选择一个commit，合并进当前分支
> $ git cherry-pick [commit]
> 
> # 删除分支
> $ git branch -d [branch-name]
> 
> # 删除远程分支
> $ git push origin --delete [branch-name]
> $ git branch -dr [remote/branch]
> ```

## 六、标签

> ```bash
> # 列出所有tag
> $ git tag
> 
> # 新建一个tag在当前commit
> $ git tag [tag]
> 
> # 新建一个tag在指定commit
> $ git tag [tag] [commit]
> 
> # 删除本地tag
> $ git tag -d [tag]
> 
> # 删除远程tag
> $ git push origin :refs/tags/[tagName]
> 
> # 查看tag信息
> $ git show [tag]
> 
> # 提交指定tag
> $ git push [remote] [tag]
> 
> # 提交所有tag
> $ git push [remote] --tags
> 
> # 新建一个分支，指向某个tag
> $ git checkout -b [branch] [tag]
> ```

## 七、查看信息

> ```bash
> # 显示有变更的文件
> $ git status
> 
> # 显示当前分支的版本历史
> $ git log
> 
> # 显示commit历史，以及每次commit发生变更的文件
> $ git log --stat
> 
> # 搜索提交历史，根据关键词
> $ git log -S [keyword]
> 
> # 显示某个commit之后的所有变动，每个commit占据一行
> $ git log [tag] HEAD --pretty=format:%s
> 
> # 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
> $ git log [tag] HEAD --grep feature
> 
> # 显示某个文件的版本历史，包括文件改名
> $ git log --follow [file]
> $ git whatchanged [file]
> 
> # 显示指定文件相关的每一次diff
> $ git log -p [file]
> 
> # 显示过去5次提交
> $ git log -5 --pretty --oneline
> 
> # 显示所有提交过的用户，按提交次数排序
> $ git shortlog -sn
> 
> # 显示指定文件是什么人在什么时间修改过
> $ git blame [file]
> 
> # 显示暂存区和工作区的差异
> $ git diff
> 
> # 显示暂存区和上一个commit的差异
> $ git diff --cached [file]
> 
> # 显示工作区与当前分支最新commit之间的差异
> $ git diff HEAD
> 
> # 显示两次提交之间的差异
> $ git diff [first-branch]...[second-branch]
> 
> # 显示今天你写了多少行代码
> $ git diff --shortstat "@{0 day ago}"
> 
> # 显示某次提交的元数据和内容变化
> $ git show [commit]
> 
> # 显示某次提交发生变化的文件
> $ git show --name-only [commit]
> 
> # 显示某次提交时，某个文件的内容
> $ git show [commit]:[filename]
> 
> # 显示当前分支的最近几次提交
> $ git reflog
> ```

## 八、远程同步

> ```bash
> # 下载远程仓库的所有变动
> $ git fetch [remote]
> 
> # 显示所有远程仓库
> $ git remote -v
> 
> # 显示某个远程仓库的信息
> $ git remote show [remote]
> 
> # 增加一个新的远程仓库，并命名
> $ git remote add [shortname] [url]
> 
> # 取回远程仓库的变化，并与本地分支合并
> $ git pull [remote] [branch]
> 
> # 上传本地指定分支到远程仓库
> $ git push [remote] [branch]
> 
> # 强行推送当前分支到远程仓库，即使有冲突
> $ git push [remote] --force
> 
> # 推送所有分支到远程仓库
> $ git push [remote] --all
> ```

## 九、撤销

> ```bash
> # 恢复暂存区的指定文件到工作区
> $ git checkout [file]
> 
> # 恢复某个commit的指定文件到暂存区和工作区
> $ git checkout [commit] [file]
> 
> # 恢复暂存区的所有文件到工作区
> $ git checkout .
> 
> # 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
> $ git reset [file]
> 
> # 重置暂存区与工作区，与上一次commit保持一致
> $ git reset --hard
> 
> # 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
> $ git reset [commit]
> 
> # 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
> $ git reset --hard [commit]
> 
> # 重置当前HEAD为指定commit，但保持暂存区和工作区不变
> $ git reset --keep [commit]
> 
> # 新建一个commit，用来撤销指定commit
> # 后者的所有变化都将被前者抵消，并且应用到当前分支
> $ git revert [commit]
> 
> # 暂时将未提交的变化移除，稍后再移入
> $ git stash
> $ git stash pop
> ```

## 十、其他

> ```bash
> # 生成一个可供发布的压缩包
> $ git archive
> ```