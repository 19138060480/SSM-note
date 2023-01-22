# Git

图解：

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\233" alt="我不好说" style="zoom:150%;" />

## 创建本地仓库 git init

先在目录文件右键，打开Git Bash

输入**git init**命令来创建本地仓库

## 工作区到暂存区 git add

使用  "git add 文件名"  命令将文件加入到暂存区，"**git add .**"代表将当前目录下所有文件都加入到暂存区

可以通过**git status**来查看当前暂存区状态

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122133412753.png" alt="image-20230122133412753" style="zoom:50%;" />

红色文字的文件是当前未被跟踪的文件，下面git也给有提示：未添加任何内容以提交，但存在未跟踪的文件（使用"git add"命令去跟踪）

使用"git add ."后，git将文件添加进暂存区，再次使用git status后文字变为绿色说明添加成功

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122133712300.png" alt="image-20230122133712300" style="zoom:50%;" />

### 删除暂存区文件

使用git status命令后，git提示你(use "git restore --staged <file>..." to unstage)，即使用"**git restore -- staged 文件名**"去删除文件，删除后再次使用git 再次使用git status查看当前状态发现被删除的文件变为红色.

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122135406279.png" alt="image-20230122135406279" style="zoom:50%;" />

## 暂存区到本地库 git commit -m

通过' **git commit -m "文件描述"** '上传至本地仓库

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122134359029.png" alt="image-20230122134359029" style="zoom:50%;" />

上传至本地仓库后，使用git status查看状态，最后一行会显示"nothing to commit, working tree clean"，即 没有需要提交的东西，工作树很干净，说明**你当前文件下的内容与本地库中的内容一致**.

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122135614429.png" alt="image-20230122135614429" style="zoom:50%;" />

**注意：**添加到本地库后就会产生一个历史版本,可以通过**git reflog**或者**git log(查看详细日志)**命令来查看历史版本

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122140001859.png" alt="image-20230122140001859" style="zoom:50%;" />

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122140155816.png" alt="image-20230122140155816" style="zoom:50%;" />

```git
cb24cc4 (HEAD -> master) HEAD@{0}: commit: GitTestTwo
```

这一行中有蓝色的"HEAD"指向master说明是这一条日志更新的内容将会提交到master里

## 更新版本

把以上命令重复一遍

## 版本穿梭 git reset --hard <版本号>

先使用git reflog命令查看版本，在使用git reset --hard <版本号>命令来回溯版本，再次使用git reflog命令后可以查看HEAD指针已经指向了第二次提交的版本

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122141833183.png" alt="image-20230122141833183" style="zoom:50%;" />