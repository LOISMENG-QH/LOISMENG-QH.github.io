---
layout:       post  
title:        github连接小指南
subtitle:     生成ssh、连接github、建立仓库
date:         2023-05-24
auther:       Lois
header-img:   img/wallpaper20230524.jpeg
catalog:      true  
tags:  
    - SSH
    - Github

---

> "日复一日"

# github连接小指南

几个小时总不能白干吧！很久以前配过一次都忘记了，这次请狠狠记住...

[TOC]

## 前提准备

1. 一个注册好的github账号
2. git工具（原来MacOS默认安装了git！感叹一下 win输一半）
3. 经验or耐心 为了一劳永逸请忍耐

## 具体连接步骤

1. 设置username和email

   

   设置username和email相当于是给机器打上了标签记号，这样在提交代码的时候就能够记录这一次提交行为的发起人，可以很好地帮助追溯查看！而email则可以接收git服务器的通知和邮件提醒，比如代码变更啦。

   

   --global参数表示这台机器上这个配置针对所有的git仓库，当然也可以根据git仓库进行设置。这里先不扩展看了，一人一机的话这样就可以啦。
   
   
   
   引号替换自己的username和邮箱。
   
   ```
   git config --global user.name "loismeng"
   git config --global user.email "loismengqiuhua@outlook.com"
   ```
   
2. 生成ssh密钥

   

   参考官方文档：

   [Generating a new SSH key and adding it to the ssh-agent](!https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

   不得不说还是官方文档靠谱，遇事不决请官方文档，具体里面已经说的很详细了，这里主要总结一下操作步骤。
   
   

   ①在终端输入, 引号替换自己的GitHub 电子邮件地址

   ```
ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

   
   
   确认路径之后按enter键，之后会要求输入两次密码，不需要设置的话直接按enter就好
   
   ![image-20230524004547543](https://github.com/LOISMENG-QH/LOISMENG-QH.github.io/blob/master/img/image-20230524004547543.png?raw=true)
   

   

（此处无图别管 要求输入密码的代码提示：）

   ```
   > Enter passphrase (empty for no passphrase): [Type a passphrase]
   > Enter same passphrase again: [Type passphrase again]
   ```

   

   ②查看对应路径下的.ssh目录是否存在config文件，如果没有就自己建一个，在我的终端路径为~/.ssh/config。打开config文件配置以下信息

 ```
   Host github.com
     AddKeysToAgent yes 
     UseKeychain yes  #如果没有设置密码的话这一栏去掉
  	 IdentityFile ~/.ssh/id_ed25519 #根据自己的路径和名称更改这一行
 ```

   

  		③将私钥添加到ssh-agent

   		如果没有设置密码去掉--apple-use-keychain	

   ```
		ssh-add --apple-use-keychain ~/.ssh/id_ed25519
   ```

   

3. 配置ssh密钥

   打开.ssh里面的id_ed25519.pub（根据自己设定的名称和路径变化）文件，将里面的内容复制到github->setting->....还是看图吧

   ![image-20230524010259206](https://github.com/LOISMENG-QH/LOISMENG-QH.github.io/blob/master/img/image-20230524010259206.png?raw=true)

   

   也可以用命令行获取内容

   ```
   pbcopy < ~/.ssh/id_ed25519.pub
   ```

   

3. 验证github绑定成功

   终端输入

   ```
   ssh -T git@github.com		
   ```

   回车之后输入yes，（如果有密码还要输入密码）



​			大功告...没成，真正使用的时候要涉及到git数据库



值得一提的是，由于我的电脑先前已经有过上面一系列的操作，config文件中已经有配置信息。在这种情况下如果想要进行多用户的话可以参考

[Git配置多用户 & reset 和 revert](!https://blackdn.github.io/2022/10/04/Git-Advance-2022/)

~~麻烦晓黑看到给我打广告费~~

## 远程仓库和本地仓库

Git的数据库分为远程数据库和本地数据库的两种。



远程数据库: 配有专用的服务器，为了多人共享而建立的数据库， 代码上传到云端就不用怕丢失啦。



本地数据库: 为了方便用户个人使用，在自己的机器上配置的数据库。



先说说**本地仓库**

### 本地仓库

①选一个自己喜欢的目录作为git管理的目录

②在这个目录路径下终端执行git init命令，就可以将这个目录变为git管理的啦

![image-20230524213815348](https://github.com/LOISMENG-QH/LOISMENG-QH.github.io/blob/master/img/image-20230524213815348.png?raw=true)



相关命令有

git status：查看工作树和索引状态

git add：将文件加入到索引，之后就可以被跟踪到更改

git commit -m "message": 将文件提交到本地数据库

git log: 查看提交记录

git reset HEAD~n: 撤回提交记录 n是代表回到n次提交以前的状态



### 远程仓库

**远程仓库**稍微复杂一些

①在github上创建一个新的仓库

②将本地仓库推送到远程



添加远程仓库的名字和url

   ```
git remote add origin git@github.com:LOISMENG-QH/OCLearning.git
   ```

（注意这个命令后面的地址是要选ssh！不然会报错：

**The requested URL returned error: 403**）

![image-20230524011903835](https://github.com/LOISMENG-QH/LOISMENG-QH.github.io/blob/master/img/image-20230524011903835.png?raw=true)



![image-20230524213851971](https://github.com/LOISMENG-QH/LOISMENG-QH.github.io/blob/master/img/image-20230524213851971.png?raw=true)



用branch命令创建一个名字为main的分支

```
git branch -M main
```



用push命令将本地的main分支推送到GitHub上，可以使用 -u 参数指定一个默认主机，这样后面就可以不加任何参数使用git push，即将本地的main分支与GitHub上新的main分支相关联。

```
git push -u origin main
```



origin是默认的远程仓库的名称

main是默认的分支名



如果想查看已经绑定的远程仓库的信息，可以通过

```
git remote -v
```

![image-20230524211425410](https://github.com/LOISMENG-QH/LOISMENG-QH.github.io/blob/master/img/image-20230524211425410.png?raw=true)



添加远程数据库的时候如果信息写错了，可以把共享数据库信息删掉再重新添加

```
git remote rm origin
```

就可以删掉origin和本地的连接噜

## 一些报错原因：

①连接远程仓库的时候在github上地址选择ssh的，不然会**The requested URL returned error: 403**

②在使用**git push -u origin master**的时候，报错：**error: 源引用规格 master 没有匹配**



原因是旧项目和新项目的默认分支名是不一样的，以前是master， 现在是main，可以用**git branch -a**查看分支情况



也可以用**git push --set-upstream origin master**命令为推送当前分支并建立与远程上游的跟踪



晚安！