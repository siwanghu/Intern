# Git笔记（siwang.hu&nbsp;&nbsp;V1.0）  
> + git -- 分布式版本控制工具  
## 安装git  
> + Linux上可以直接执行**sudo apt-get install git**  
> + windows上下载安装&nbsp;&nbsp;[git地址](https://git-scm.com/downloads)  
## 配置git  
> + git是分布式版本控制系统，每个机器都必须规定标识：你的名字和Email地址  
>  
> + **git config --global user.name "Your Name"**  
>  
> + **git config --global user.email "email@example.com"**  
>  
> + **ssh-keygen -t rsa -C "youremail@example.com"** 配置github公钥  
>  
> + 在~/下生成.ssh文件夹，打开id_rsa.pub，复制里面的key。到github 上，进入 Account => Settings，配置公钥  
## 版本提交  
> **git add -A** 将所有文件的改动添加到提交仓库中  
> **git add filename** 将指定文件名的文件改动添加到提交仓库中  
> **git commit -m "message"** 提交所有改动