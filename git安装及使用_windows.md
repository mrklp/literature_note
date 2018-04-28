##git安装及使用(windows)

1. 从官网下载安装程序：<https://git-scm.com/download/>
2. 配置全局name和email.

```bash
$ git config --global user.name "mrklp"
$ git config --global user.email "2017201005@njau.edu.cn"
```

3. 创建本地版本库.

```bash
$ mkdir /f/Documents/Git/literature_note 
$ cd  /f/Documents/Git/literature_note 
$ git init
```

4. 身份验证(ssh) .

```bash
$ ls -al ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "2017201005@njau.edu.cn"
Generating public/private rsa key pair.
Enter file in which the key is (/c/Users/you/.ssh/id_rsa):
Created directory '/c/Users/you/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/you/.ssh/id_rsa.
Your public key has been saved in /c/Users/you/.ssh/id_rsa.pub.
The key fingerprint is:
The key's randomart image is:
$ eval $(ssh-agent -s)    #避免每次push都要输入密码
$ ssh-agent
$ ssh-add ~/.ssh/id_rsa
$ clip < ~/.ssh/id_rsa.pub    #将复制的秘钥加入github
```

5. 添加远程库.

```bash
$ git remote add origin git@github.com:mrklp/literature_note.git
```
6. 文件提交.

新建一个`README.md`文件，放到`/f/Documents/Git/literature_note`目录下，然后命令行输入：

```bash
$ git add README.md
$ git commit -m "Create README.md"
[master (root-commit) 686f7f4] wrote a readme file
 0 file changed, 0 insertions(+)
 create mode 100644 README.md
$ git push -u origin master
$ git pull origin master
```

7. 时光穿梭.

```bash
$ git status
$ git diff readme.txt
$ git log --pretty=oneline    #查看提交历史
$ git reset --hard HEAD^    #回退到上一个版本
$ git reflog    #查看命令历史
$ git diff HEAD -- readme.txt    #查看工作区和版本库里面最新版本的区别
$ git checkout -- readme.txt    #撤销工作区的修改或删除
$ git reset HEAD readme.txt    #撤销暂存区，重新放回工作区
$ git rm test.txt
$ git commit -m "remove test.txt"
```
8. 分支管理.

```bash
$ git checkout -b dev    #创建并且换分支dev
$ git branch dev    #创建分支dev 
$ git checkout dev    #切换分支dev
$ git branch    #查看当前分支
$ git merge dev    #快速合并指定分支到当前分支
$ git branch -d dev    #删除dev分支
$ git log --graph --pretty=oneline --abbrev-commit   #查看分支合并图
$ git merge --no-ff -m "merge with no-ff" dev    #普通合并，git log有记录
```
9. 标签管理.

```bash
$ git branch
$ git checkout master
$ git tag v1.0    #标签打在最新提交的commit上
$ git tag    #查看所有标签
$ git log --pretty=oneline --abbrev-commit
$ git tag v0.9 6224937
$ git show v0.9
$ git tag -a v0.1 -m "version 0.1 released" 3628164    #创建带有说明的标签
$ git tag -d v0.1
$ git push origin v1.0
$ git push origin --tags
$ git tag -d v0.9
$ git push origin :refs/tags/v0.9
```