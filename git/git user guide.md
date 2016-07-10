## Git 安装

* Ubuntu下安装：

      $ sudo apt-get install git

* Windows下安装：

    进入[git-scm.com/downloads](git-scm.com/downloads)下载安装包，按默认选项安装即可,安装完成后在开始菜单中找到"Git"-"Git Bash",出现命令行窗口即可使用

    习惯使用图形界面的可以使用[tortoisegit](https://tortoisegit.org/),有需要的自行研究如何使用

* Mac安装：

    使用homebrew安装git，具体方法参考homebrew文档：[http://brew.sh/](http://brew.sh/)

    PS:本人没有Mac,找机会再补充

* 源码安装：

    自行下载Git源码([传送门](https://github.com/git/git))，下载完成后进入下载目录，依次输入`./config`,`make`,`sudo make install`编译安装


* 简单设置自报家门：

      git config --global user.name "your.name"
      git config --global user.email "example@mail.com"

    可通过`git config --global --list`查看当前的Git设置

    也可以对不同的Git仓库进行不同的配置(可省略--local)：

      git config --local user.name "your.name"
      git config --local user.email "example@mail.com"
    设置完成后在不同目录下进行操作将使用各自配置的用户信息，可通过`git config --local --list`可查看当前目录的Git设置，默认情况下将使用global配置

## Git 常用命令说明
 * `git init`: 创建版本库，本地创建版本库可通过`git remote add origin git@github.com:youname/repository.git`与远程仓库关联，或者可直接使用`git clone git@github.com:youname/repository.git`将你在github上创建的仓库clone到本地并自动关联
 * `git status`: 查看当前仓库的状态，显示文件修改、工作区状态等信息
 * `git add`: 添加修改或新增文件到暂存区，支持通配符，如可以使用`git add *.java`提交目录下的所以java文件
 * `git mv`: 移动或重命名一个文件、目录或符号链接
 * `git rm`:  使用 `git rm --cached <文件>...` 以取消暂存
 * `git commit`: 将已经添加到暂存区的修改提交到仓库，要求添加提交说明，可通过-m指定:`git commit -m 'you comments'`
 * `git log`: 显示提交日志，即git commit日志
 * `git reset`: 版本回退，已经commit的内容可通过`git reset --hard <commit版本号>`，版本号可通过`git log`查看，使用时只需要提供版本号的前几位，`git log`只能显示当前提交之前的日志，如果之前已经产生过回退，可通过`git reflog`命令查看命令历史，以便确定要回到未来的哪个版本； `git reset HEAD file`撤销已经提交到暂存区的文件
 * `git branch`: `git branch -a`查看本地和远程分支； `git brach -d`删除本地分支，强制删除使用-D；`git branch -r`查看远程分支；`git branch --set-upstream master origin/master`设置跟踪远程库和本地库
 * `git checkout`: `git checkout <branch>`切换分支； `git checkout -b <branch>`创建并切换到分支； `git checkout -- <filename>`从版本库中还原工作区文件（没有执行`git add`，已经执行过的通过`git reset HEAD <file>`撤销）
 * `git tag`: 显示tag信息，同`git tag -l`； `git tag <tagname>`新建tag； `git tag -d <tagname>`删除tag
 * `git diff`: 显示提交之间、提交和工作区之间等的差异
 * `git merge`: 合并分支，将当前分支与指定分支合并
 * `git pull`: 从远程库中拉取文件
 * `git push`: 将本地信息推送到远程分支，使用`git push
origin :<branch>`可删除远程分支（慎重使用）； `git push origin <localbranch>:<remotebranch>`将本地分支推送到指定的远程分支； `git push --set-upstream origin <branchname>`关联远程分支； `git push origin <remotebranch>`完成关联后可使用此命令推送
 * `git stash`: 暂存本地工作区修改，不添加到暂存区，一般用于切换分支时暂时保存之前分支的修改，通过`git stash list`查看所有的stash，`git stash pop`恢复最近一次stash，`git stash apply`指定恢复某个stash，`git stash drop`删除
 * 其他： `git config --global core.quotepath false`解决中文名称文件显示问题

## GitHub,GitLib使用
 * 创建SSH key：

       $ ssh-keygen -t rsa -C "youremail@example.com"

    执行过程中可指定生成的ssh key名称，默认在`~/.ssh`目录下生成的`id_rsa`和`id_rsa.pub`两个文件

 * 添加SSH key到github/gitlab上：

    将生成的`id_rsa.pub`文件内容添加到Add SSH key菜单下

 * 同时使用github/gitlib：

    为不同环境生成不同的SSH key并添加到远程accout信息下，在`~/.ssh`目录下创建`config`文件，文件内容参考如下：

        # gitlab
        Host gitlab.com
            HostName gitlab.com
            PreferredAuthentications publickey
            IdentityFile ~/.ssh/id_rsa
        # github
        Host github.com
            HostName github.com
            PreferredAuthentications publickey
            IdentityFile ~/.ssh/id_rsa_github

    注意使时可能需要对不同的仓库使用不同的用户信息，配置说明已在前文说明
