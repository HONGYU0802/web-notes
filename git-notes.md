# Git

## 基本概念

### git与svn

|          git           |      svn       |
| :--------------------: | :------------: |
|     分布式版本控制     | 集中式版本控制 |
|  基于本地仓库进行提交  | 拥有全局版本号 |
| 不依赖网络和中央服务器 |   依赖服务器   |
|       按数据存储       |   按文件存储   |
|        公网访问        |  公司内网访问  |

### 仓库

|  工作区   | 暂存区/索引区 |   栈区    |       本地仓库        |      远程仓库      |
| :-------: | :-----------: | :-------: | :-------------------: | :----------------: |
| workspace |  stage/index  |   stash   |   local repository    | remote repository  |
|           |    git add    | git stash | git commit、git clone | git push、git fork |

### 远程仓库比较

|      github      |            gitlab            |       码云       |
| :--------------: | :--------------------------: | :--------------: |
| 基于web的git仓库 |       基于web的git仓库       | 基于web的git仓库 |
|   私人仓库付费   | 私人仓库免费、更多的控制权限 |   服务器在国内   |


### stash 栈区

| 作用       | 可以将工作区和暂存区未提交修改保存到栈区                              |
| :---------- | :--------------------------------------------------------------------- |
| 跨分支存储 | 所有分支都操作同一个栈区                                              |
| 应用场景   | 工作没完成不想提交，但是需要去处理临时bug、开发过程中发现处于错误分支 |

```
// 将工作区，缓存区的修改保存在栈上
git stash list
git stash save  'msg'           // 同 git stash 
git stash show                  // 比较当前工作区和最近存储的差异
git stash apply stash@{0}       // 应用某个存储，不会在栈中删除
git stash pop                   // 应用最近一次存储，并在栈中删除
git stash drop stash@{0}        // 移除指定的存储
git stash clear
```

### status 状态

|   untracked    |        modified        |       staged       |
| :------------: | :--------------------: | :----------------: |
| 文件没有被追踪 | 文件已修改，但是没保存 | 文件在暂存区已保存 |

### 分支

| master | release | develop | feature | hotfix |  main  | trunk |
| :----: | :-----: | :-----: | :-----: | :----: | :----: | :---: |
|  线上  | 预上线  |  开发   |  模块   |  bug   | 主分支 | 分支  |

```
git branch [feature]            // 创建分支
git branch --list               // 所有本地分支
git branch -r                   // remote 所有远程分支
git branch -a                   // all 所有分支

git branch -d [feature]         // delete 删除本地分支
git push origin -d [feature] 	// 删除远程分支

git checkout [feature]          // 切换分支
git checkout -b [feature]       // checkout & branch 创建新分支并切换到新分支

git branch -m [feature] [newFeatureName]	//move 重命名分支
```

## 命令

### 查看信息

```
git log                 // 历史提交记录
git reflog              // 历史 git 操作记录
git show [hash]         // 显示指定版本的提交内容
git log -p [file]       // 指定文件的历史提交区别
git diff                // 比较工作区和暂存区文件的差异
```

### 提交到本地仓库

```
git add [file]
git commit -m
git commit -am		// add & commit
git commit -amend	// 修改上一次 commit 提交信息（amend：改正、修改）
```

### 操作远程仓库

```
git push origin [feature]

git fetch 	// 拉取所有分支的更新
git fetch origin [master]:[localFeature]

git pull origin [feature]:[localFeature]	// fetch & merge
```

### 回滚/回退、删除

```
回滚，会产生新的提交记录，且不会删除提交历史
git revert [hash]           // 想要删除的 hash 版本节点
git revert (hash)-[hash]    // 删除 2个 hash版本间的所有节点，产生 n 个新提交

重置，会修改提交历史（使用 git push -f 进行远程提交历史的覆盖）
git reset [hash] --mixed	// hash版本恢复到工作区
git reset [hash] --hard		// 清空所有工作区、暂存区、hash版本的修改
git reset [hash] --soft		// hash版本恢复到暂存区

git rm [file]   // 删除指定文件
```

### 合并

```
变基，没有分叉，一条线
git rebase [branch]
git rebase -i [hash]    // 合并多次提交，p 改 s 为合并提交（vim 命令）
git rebase --continue
git rebase --abort
git rebase --skip

合并，会产生合并节点
git merge [branch]

提取部分代码
git cherry -pick [hash/commitString]
git cherry -pick (hash)-[hash]
```

### 修改历史记录

```
git rebase -i HEAD~3    // p 改 edit 修改提交名称 （vim 命令）
git commit -amend       // 修改上一次 commit 提交信息（amend：改正、修改）
```

## 解决冲突

| 原因       | 修改了公共方法的代码                                                                              |
| :---------- | :------------------------------------------------------------------------------------------- |
| 触发操作   | git merge、git pull、git rebase                                                             |
| 推荐方法   | 放弃本地冲突文件，使用远程文件进行覆盖                                                      |
| 方法二     | 将本地代码保存在栈区 git stash，拉取远程代码 git pull，将栈区代码拉出解决冲突 git stash apply |
| 自己的代码 | <<<<<<<HEAD、=======                                                                        |
| 冲突的代码 | =======、\>>>>>>>                                                                           |

## 安装 Git

```
官网下载git，安装

选择 gitbash 命令行工具使用 git 或 终端使用

选择 openSSL 建立 https 连接

配置环境变量，把bin目录添加在 path下

配置用户信息
git config --global user.email 'hongyu@gmail.com'
git config --global user.name 'hongyu'

在新建的文件夹下初始化本地仓库
git init
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

在远程创建仓库，将本地仓库与远程仓库关联起来
git remote add origin repositoryName
git push -u origin master （指定默认分支为 master）
git push origin master
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


## 工作流程

```
拉取线上最新代码
创建分支
工作
提交上传
合并上传
```

## 帮助

```
git help
git help -a
git help fetch
```

