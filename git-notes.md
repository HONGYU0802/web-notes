# Git 使用教程 [^1]
[^1]:[git 官网](https://git-scm.com)

我使用的是 v2.32.1, 2022.8.4 最新的是 2.37.0

## 基本概念

### git 与 svn

|          git           |      svn       |
| :--------------------: | :------------: |
|     分布式版本控制     | 集中式版本控制 |
|  基于本地仓库进行提交  | 拥有全局版本号 |
| 不依赖网络和中央服务器 |   依赖服务器   |
|       按数据存储       |   按文件存储   |
|        公网访问        |  公司内网访问  |

### 仓库

|  工作区   | 索引区 |   栈区    |       本地仓库        |      远程仓库      |
| :-------: | :-----------: | :-------: | :-------------------: | :----------------: |
| working tree |  index  |   stash   |   local repository    | remote repository  |
|      ctl s     |    git add    | git stash | git commit、git clone | git push、git fork |

### stash 栈区

| 作用       | 可以将工作区和索引区未提交修改保存到栈区                              |
| :---------- | :--------------------------------------------------------------------- |
| 跨分支存储 | 所有分支都操作同一个栈区                                              |
| 应用场景   | 工作没完成不想提交，但是需要去处理临时bug、开发过程中发现处于错误分支 |



### 远程仓库比较（基于web的git仓库 ）

|      github      |            gitlab            |       码云       |
| :--------------: | :--------------------------: | :--------------: |
|   私人仓库付费   | 私人仓库免费、更多的控制权限 |   服务器在国内   |


### status 状态

|   untracked    |        modified        |       staged       |
| :------------: | :--------------------: | :----------------: |
| 文件没有被追踪 | 文件已修改，但是没保存 | 文件在暂存区已保存 |

### branch 分支

| master | release | develop | feature | hotfix |  main  | trunk |
| :----: | :-----: | :-----: | :-----: | :----: | :----: | :---: |
|  线上  | 预上线  |  开发   |  模块   |  bug   | 主分支 | 分支  |

```
创建分支
git branch [feature]
切换分支
git checkout [feature]
创建新分支并切换到新分支
git checkout -b [feature]
git checkout -b totallyNotMain o/main
强制切换制定的分支到指定的提交上
git branch -f main HEAD~3

查看所有本地分支
git branch 
查看所有远程分支
git branch -r
查看所有分支
git branch -a

重命名分支 move
git branch -m [feature] [newFeatureName]
删除本地分支 delete 
git branch -d [feature]
删除远程分支
git push origin -d [feature]
```

### 帮助文档

```
查看常用命名
git help
查看全部的命令
git help -a
查看某个命令下具体的子命令 subcommand 的使用
git help <command>
git help git
git help add 
```

### .gitignore

```
node_modules        // 忽略这个文件夹
.vscode             // 忽略以 vscode 结尾的所有文件
*.md                // 忽略以 md 结尾的所有文件
!README.md          // 例外的，不被忽略的文件
doc/*.txt           // 忽略 doc 目录下所有 .txt 文件
doc/**/*.txt        // 忽略 doc 目录及其子孙目录下的所有 .txt 拓展名的文件
```


### 解决冲突

| 原因       | 修改了公共方法的代码                                                                              |
| :---------- | :------------------------------------------------------------------------------------------- |
| 触发操作   | git merge、git pull、git rebase                                                             |
| 解决方法     | 将本地代码保存在栈区 git stash，拉取远程代码 git pull，将栈区代码拉出解决冲突 git stash apply |
| 自己的代码 | <<<<<<<HEAD、=======                                                                        |
| 冲突的代码 | =======、\>>>>>>>                                                                 

## 命令

简写的 option 使用一个 -，完整的单词则使用 --

#### 我想存储我对文件的修改

```
拍取当前项目文件内容的快照，这个快照将被存储在一个名为索引的临时存储区域。
git add [file]
把索引区临时存储的内容持久化的存储到本地仓库里，这会产生一个项目的版本。
git commit -m 'description this commit here'
前两个提交的聚合写法
git commit -am 'description this commit here'
把本地仓库里的修改推送到远程仓库存储起来。
git push origin [feature]
本地的分支来源和去向分支不同
git push origin <source>:<destination>
```

#### 我不想提交工作区和索引区的修改，但又要切换到别的分支处理任务

```
将工作区，索引区的修改保存在栈上，并将工作区恢复成修改前的版本
git stash push  'msg'           // git stash 默认一个提交信息
应用栈中最近一次存储的内容，没有冲突则会在栈中删除该记录（工作区必须为空，有冲突则需要解决，解决后不会删除这条记录）
git stash pop         
应用栈中指定的存储的内容，不会在栈中删除    
git stash apply stash@{0}    

查看栈中的所有记录
git stash list
展示当时推入栈中的修改内容
git stash show  

删除指定的存储记录
git stash drop stash@{0}
清空栈中的记录
git stash clear
```

#### 我想查看仓库的提交记录

```
显示当前分支最近的一次提交记录，以及和上次提交内容的不同
git show
只显示当前分支的提交记录
git log
显示指定分支的日志，排除指定分支的日志
git log foo bar ^baz
显示全部分支的全部提交记录
git log --reflog
通过图形展示当前分支的提交历史
git log --graph
通过图形显示全部分支的提交历史
git log --oneline --graph --all
```
#### 我想查看我 git 操作的历史记录

```
git reflog
HEAD@{2} means "where HEAD used to be two moves ago", 
master@{one.week.ago} means "where master used to point to one week ago in this local repository"
```

#### 我想查看两个提交的版本间有什么不同

```
展示工作区和索引区内容的不同（工作区的修改需要 ctl s）
git diff
展示索引区和上次提交内容的不同
git diff --cached
比较两个分支的最近提交的不同
git diff [branch1] [branch2]
```

#### 我想撤销我对文件的修改

```
从索引区和工作树中删除指定的文件，如果文件没有在索引区和仓库中保存过，那将找不到它，如果文件在工作区有修改需要强制删除
git rm -f [file]


恢复
撤销工作区文件的修改
git restore [file]
撤销索引区文件的修改到工作区
git restore --staged [file]


回滚，会产生新的提交记录，且不会删除提交历史（当前的工作区必须为空）
从当前版本回滚到指定版本，进行冲突解决后提交一个新版本
git revert [hash]
删除2个版本间的所有节点，产生 n 个新提交（如果中间创建了新文件在后续用到了会失败）
git revert [hash]-[hash]
git revert --skip           // 应用当前版本的内容
git revert --abort          // 放弃回滚操作
git revert --continue       // 继续


重置，会删除提交记录（使用 git push -f 进行远程提交历史的覆盖）
重置到指定的版本，清空所有工作区、暂存区内容，不会清空栈区
git reset --hard [hash] 
将指定的版本的修改恢复到暂存区
git reset --soft [hash] 
将指定版本的修改恢复到工作区
git reset --mixed [hash]
```

#### 我想修改提交历史

```
修改最近一次提交的信息（amend：改正、修改），会删掉旧的提交并产生一个新的提交
git commit --amend -m ''
变基，修改多个提交信息，会删掉旧的提交并产生一个新的提交，pick 改 rewrite 修改提交名称（vim 命令）
git rebase -i HEAD~3

变基，合并多个提交，pick 改为 squash，会产生新提交（第一个 pick 不可以修改）
git rebase -i HEAD~3
```

#### 我想合并不同分支上的提交

```
变基，没有分叉，一条线
将当前分支上的每一个提交（从共同祖先开始算），依次应用到指定分支的最新提交上，解决完冲突继续解决下一个，直到当前分支的最新提交解决完冲突。（每解决一次冲突就会在未来的当前分支上产生一个新的提交，默认使用当前分支上的提交信息）
git rebase [branch]

合并，会产生合并节点
将指定的分支上提交的修改应用到自身上，解决完冲突产生一个新提交，默认使用 merge [branch] 提交信息
git merge [branch]

提取指定提交的修改到当前的分支上，解决完冲突产生一个新提交，默认使用提取的提交信息
git cherry-pick [branch]        // 应用分支上最新的提交
git cherry-pick [hash]
git cherry -pick (hash)-[hash]
```

#### 我想从远程仓库拉取代码

```
在空文件夹内，根据远程仓库 URL 拉取
git clone <repository>
拉取远程仓库的所有分支的更新到本地仓库
git fetch 	
拉取远程仓库的指定分支的更新到本地仓库的🈯️定分支上
git fetch origin [branch]:[localBranch]
拉取远程仓库的指定分支的更新到本地仓库的🈯️定分支上，并且通过 merge 合并
git pull origin [branch]:[localBranch]
拉取远程仓库的指定分支的更新到本地仓库的🈯️定分支上，并且通过 rebase 合并
git pull origin [branch]:[localBranch] --rebase 
```


## 安装 Git
### 配置环境变量

```
官网下载 git，安装

选择 gitbash 命令行工具使用 git 或 终端使用

选择 openSSL 建立 https 连接

配置环境变量，把bin目录添加在 path下
```

### 配置密钥

```
远程仓库与本地仓库是通过SSH来连接的
ssh-keygen -t rsa -C hongyu.0802@gmail.com
// -t: type of the key 密钥类型、-C: comment to identify the key 识别这个密钥的注释

默认生成位置 .ssa (mac访问隐藏文件 command shift .)
复制 id_rsa.pub 公钥，添加到github > setting > SSH keys
ssh git@github.com (测试)

配置
git config --global user.name 'macBook'
git config --global user.email 'hongyu.0802@gmail.com'
```

### 创建仓库并关联上远程仓库

```
在新建的文件夹下初始化本地仓库
git init

在远程创建仓库，将本地仓库与远程仓库关联起来
git remote add origin repositoryName
git push -u origin master // 指定默认分支为 master
git push origin master
```

# Git 练习网站 [^2]
[^2]:[中文练习 git 的游戏网页](https://learngitbranching.js.org/?locale=zh_CN)

## 基础篇

1. 把所有的差异打包到一起作为一个提交记录，保证提交记录轻量
2. 即使创建再多的分支也不会造成储存或内存上的开销
3. 分支上的那个星号（*）表示当前所在的分支
4. git checkout 承载的功能太多了，git switch 的新命令会取代部分功能
5. merge 包含进来两个父节点本身及它们所有的祖先
6. rebase 取出一系列的提交记录，“复制”它们(提交的副本)，然后在另外一个地方逐个的放下去

## 相对引用

1.  HEAD 是一个对当前检出记录的符号引用，HEAD 总是指向当前分支上最近一次提交记录。
2.  提交记录的哈希值基于 SHA-1，共 40 位
3.  相对引用: 从一个易于记忆的地方开始计算。使用 ^ 向上移动 1 个提交记录，~3 向上移动多个提交记录

## 修改提交树

1. cherry-pick 可以将提交树上任何地方的提交记录取过来追加到 HEAD 上（只要不是 HEAD 上游的提交就没问题）
2. 先用 git rebase -i 将提交重新排序，然后把我们想要修改的提交记录挪到最前，然后用 git commit --amend 来进行一些小修改，接着再用 git rebase -i 来将他们调回原来的顺序，最后我们把 main 移到修改的最前端
3. git tag v1 C1 永远指向某个提交记录的标识
4. git describe \<ref> 能帮你在提交历史中移动了多次以后找到方向；描述离你最近的标签

```
\<tag>_\<numCommits>_g\<hash>
tag 表示的是离 ref 最近的标签， numCommits 是表示这个 ref 与 tag 相差有多少个提交记录， hash 表示的是你所给定的 ref 所表示的提交记录哈希值的前几位
```

## 远程仓库

1. 远程仓库是一个强大的备份，本地仓库多了一个名为 o/main 的分支, 这种类型的分支就叫远程分支。远程分支反映了远程仓库(在你上次和它通信时)的状态
2. 远程分支有一个命名规范: \<remote name>/\<branch name>，用 git clone 某个仓库时，Git 已经帮你把远程仓库的名称设置为 origin，origin 太长了，简写为 o
3. git fetch 实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态，通过互联网（使用 http:// 或 git:// 协议) 与远程仓库通信，不会修改本地的文件，只是单纯的下载
4. 历史偏离有许多的不确定性，Git 是不会允许你 push 变更的。实际上它会强制你先合并远程最新的代码，然后才能分享你的工作
5. 远程服务器拒绝直接推送(push)提交到main, 因为策略配置要求 pull requests 来提交更新.
6. 克隆时, Git 会为远程仓库中的每个分支在本地仓库中创建一个远程分支（比如 o/main）。然后再创建一个跟踪远程仓库中活动分支的本地分支，默认情况下这个本地分支会被命名为 main

<br>

# Git 术语

```
head
一个分支顶端提交的引用名字

HEAD
当前的分支

working tree 工作树
当前正在查看的文件树，包含 HEAD commit’s tree 的内容和任何本地没有提交的修改。

index 索引
作为对象存储的包含状态信息的文件集合，是工作树的一个存储版本。

master 
默认的开发分支，在创建 Git repository 时自动创建激活。

object
Git 的一个存储单元，通过对内容进行 SHA-1 获得的唯一标识。所以对象不能被改变。

object name = hash
对象通过 SHA-1 获得的 40位十六禁止字符串。

object type
描述对象的类型："commit", "tree", "tag" or "blob"

origin
默认的上游仓库（远程仓库）

fast-forward 快进
特殊类型的 merge，不会生成新的提交，仅更改你的分支指向同一个版本。
            o - A   <-- name1
          /
o - o - o - o - B   <-- name2
      \       /
        o - C       <-- name3
将 name1 指向 name2，那 A 及其前面的提交成了孤魂，会被 gc（reflog 只保存30天）
将 name3 指向 name2，C 及其前面的提交仍可以访问到，name3 只是顺着已有的合并线移动到 name2, 属于一次 fast-forward，即在移动后还能触碰到之前的提交。

常发生在远程仓库，因为它只接受 fast-forward 得来的结果。
若加上--no-ff选项，则会不使用fast-forward进行合并。这个时候feature分支上的更改会做作为一个新的提交，回到master上。
而如果使用--ff选项，若master上没有别人的新提交，那么原本指向master最新的head会直接移动到feature的最新上。
```