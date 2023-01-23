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

使用git status命令后，git提示你(use "git restore --staged <file>..." to unstage)，即使用"**git restore -- staged 文件名**"去删除文件，删除后再次使用git 再次使用git status查看当前状态发现被删除的**文件变为红色**.

## 暂存区到本地库 git commit -m

通过' **git commit -m "文件描述"** '上传至本地仓库

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

## 分支

### 分支命令

1. "**git branch 分支名**" 	创建分支
2. "**git branch -v**" 	查看分支
3. "**git checkout 分支名**" 	切换分支
4. "**git merge 分支名**" 	把指定的分支合并到当前分支上(将b合并给a,需要在a分支上运行命令是复制分支代码,而不是剪切）
5. git checkout -b      分支名称：创建并切换分支
6. git branch -d        分支名称：删除分支（代码合并完后再删）
7. git branch -D       分支名称：强制删除，不管你保没保存，没提示的删除（慎用）
8. 通过索引值前进/后退版本
           git reset --hard [index]
           git reset --hard HEAD^    往后退一个版本
           git reset --hard HEAD~3   往后退三个版本
9. 比较文件(不带文件名比较多个文件)
           git diff [file name]            默认跟暂存区比较
           git diff [index] [file name]    跟本地库中某个版本进行比较

#### 实验：正常合并

##### 步骤一：创建一个目录和txt文件，内容随意

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122183533002.png" alt="image-20230122183533002" style="zoom:50%;" />

##### 步骤二：在目录中使用git bash here，将文件加入到本地库中

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122183727367.png" alt="image-20230122183727367" style="zoom: 50%;" />

##### 步骤三：创建分支并查看 git branch

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122183919580.png" alt="image-20230122183919580" style="zoom:50%;" />

##### 步骤四：切换分支并查看 git checkout

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122184051282.png" alt="image-20230122184051282" style="zoom:50%;" />

此时创建的分支：MyBranch高亮显示

##### 步骤五：修改文本内容并提交本地库

先简单把第一行给改了

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122184452452.png" alt="image-20230122184452452" style="zoom:50%;" />

此时用git status命令查看状态，发现文件报红，需要我们手动提交到本地库

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122184545130.png" alt="image-20230122184545130" style="zoom:50%;" />

手动添加到本地库

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122184740541.png" alt="image-20230122184740541" style="zoom:50%;" />

##### 步骤六：切换到master分支，并 合并分支

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122185027632.png" alt="image-20230122185027632" style="zoom:50%;" />

合并分支："**git merge "MyBranch**"

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122185146444.png" alt="image-20230122185146444" style="zoom:50%;" />

##### 步骤七：查看合并后的文件以及分支情况

下图为合并后的txt文件，可以看到**分支中的MyBranch覆盖了之前的FanYi**

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122185341865.png" alt="image-20230122185341865" style="zoom:50%;" />

此时的MyBranch分支还存在，如果需要删除，可以使用**git branch -d**命令删除

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122185446797.png" alt="image-20230122185446797" style="zoom:50%;" />

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122185559521.png" alt="image-20230122185559521" style="zoom:50%;" />

**说明：**以上的合并内容为**正常合并**

#### 实验：合并冲突

创建分支方面与正常合并的步骤一样，所以合并冲突从步骤五开始，以下内容为master分支中的原始内容，将其提交到本地库中

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122190006089.png" alt="image-20230122190006089" style="zoom:50%;" />

##### 步骤五：修改文本内容并提交本地库

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122192935253.png" alt="image-20230122192935253" style="zoom:50%;" />

我在原本的基础上又添了一行，并且把原来的MyNameIsYao给删除掉，此时将文件上传至分支的本地库

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122190443517.png" alt="image-20230122190443517" style="zoom:50%;" />

##### 步骤六：切换到master分支，并 合并分支

​			切换分支：

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122191037306.png" alt="image-20230122191037306" style="zoom:50%;" />

此时我们修改master分支内的txt内容，如下

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122191151679.png" alt="image-20230122191151679" style="zoom:50%;" />

重新提交到本地仓库

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122191246081.png" alt="image-20230122191246081" style="zoom:50%;" />

合并分支

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122192256343.png" alt="image-20230122192256343" style="zoom:50%;" />

此时git不敢帮我们随意合并了，在命令行后面也有(master|MERGING)，即：master分支正在合并中，此时我们打开txt文件

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122192409858.png" alt="image-20230122192409858" style="zoom:50%;" />

```
<<<<<<< HEAD
This is Test TXT
This is Test TXT	MyNameIsYao
This is Test TXT	WhoYouAre???
=======
This is Test TXT	I don't Know What Are You Doing!!
This is Test TXT	
This is Test TXT	MyNemeIsFei Halo!!
>>>>>>> MyBranch
```

可以发现文本已经改变了，git帮我们把变化通过<<<<<<< HEAD	=======	>>>>>>> MyBranch列了出来，我们此时只要删除这几个符号，然后把想要的留下，不想要的去掉即可

假如我想要MyBranch的第一行和第三行内容，master中的第二行内容，我就可以把除了这几行之外的东西删除掉

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122192733724.png" alt="image-20230122192733724" style="zoom:50%;" />

记得重新排序哦！

完成保存后，重新提交到本地仓库即可

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122192836111.png" alt="image-20230122192836111" style="zoom:50%;" />

##### 总结

冲突合并的原因是因为两个分支都对本地仓库产生了变化，而且影响到了同一行的代码，git无法决定取用哪种，交给人工处理

# github

## 新建仓库

### 步骤一：New repository

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230123142120089.png" alt="image-20230123142120089" style="zoom:50%;" />

### 步骤二：按步骤创建

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230123142650711.png" alt="image-20230123142650711" style="zoom:50%;" />

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230123142800865.png" alt="image-20230123142800865" style="zoom:50%;" />

### 步骤三：上传本地文件

根据github给的步骤走就可以了

在需要上传的文件中右键，点击git bash here

以下步骤为不新建 READEME.md 文件的步骤

- 第一步： git init 初始化本地仓库
- 第二步： git add . 把所有文件上传至暂存区
- 第三步： git commit -m "描述" 把暂存区文件上传至本地库,双引号里面的内容是本次提交的注释，但是必须填写
- 第四步： **git branch -M main**  把分支改名为main
- 第五步： ' **git remote add origin** 链接 ' 链接仓库地址
- 第六步： ‘ **git push -u origin main** ’ 上传到仓库

## 远程连接

### 将远程地址记录到本地配置

​        git remote add origin [远程地址]

### 推送到远程

​        git push -u origin [分支名]

### 拉取远程更新

​        git fetch origin [分支名]       抓取远程文件到本地的origin/master，并不会更改本地文件
​        git merge origin/[分支名]       合并远程文件到本地文件
​        git pull                       抓取且合并

### 邀请其他成员加入团队(授予推送权限)

​        项目所有者在github或gitee上邀请

### 远程冲突解决

​        同一个文件，A用户更改了并且推送到远程，B用户没有拉取最新的情况下也更改了此文件，B用户推送时就会产生冲突
​        git pull origin [分支名]        先拉取最新版本到本地
​        再手动编辑文件，删除特殊符号，修改到满意的程度
​        git add [file name]
​        git commit -m "message"
​        git push origin [分支名]