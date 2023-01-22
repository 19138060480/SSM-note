# Git

图解：

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\233" alt="我不好说" style="zoom:150%;" />

## 创建本地仓库

先在目录文件右键，打开Git Bash

输入**git init**命令来创建本地仓库

## 工作区到暂存区

使用  "git add 文件名"  命令将文件加入到暂存区，"**git add .**"代表将当前目录下所有文件都加入到暂存区

可以通过**git status**来查看当前暂存区状态

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122133412753.png" alt="image-20230122133412753" style="zoom:50%;" />

红色文字的文件是当前未被跟踪的文件，下面git也给有提示：未添加任何内容以提交，但存在未跟踪的文件（使用"git add"命令去跟踪）

使用"git add ."后，git将文件添加进暂存区，再次使用git status后文字变为绿色说明添加成功

<img src="D:\JAVALearning\SSM框架\SSM框架笔记(含Springboot+MP)\assets\image-20230122133712300.png" alt="image-20230122133712300" style="zoom:50%;" />

## 暂存区到本地库

通过' **git commit -m "文件描述"** '上传至本地仓库



**注意：**添加到本地库后就会产生一个历史版本