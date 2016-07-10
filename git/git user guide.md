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

## Git 命令概览：
安装完成后输入git命令即可查看git命令概览，如下所示：

 * 开始一个工作区（参见：git help tutorial）
    *   clone      克隆一个仓库到一个新目录
    *   init       创建一个空的 Git 仓库或重新初始化一个已存在的仓库

 * 在当前变更上工作（参见：git help everyday）
    *   add        添加文件内容至索引
    *   mv         移动或重命名一个文件、目录或符号链接
    *   reset      重置当前 HEAD 到指定状态
    *   rm         从工作区和索引中删除文件

 * 检查历史和状态（参见：git help revisions）
    *   bisect     通过二分查找定位引入 bug 的提交
    *   grep       输出和模式匹配的行
    *   log        显示提交日志
    *   show       显示各种类型的对象
    *   status     显示工作区状态

 * 扩展、标记和调校您的历史记录
    *   branch     列出、创建或删除分支
    *   checkout   切换分支或恢复工作区文件
    *   commit     记录变更到仓库
    *   diff       显示提交之间、提交和工作区之间等的差异
    *   merge      合并两个或更多开发历史
    *   rebase     本地提交转移至更新后的上游分支中
    *   tag        创建、列出、删除或校验一个 GPG 签名的标签对象

 * 协同（参见：git help workflows）
    *   fetch      从另外一个仓库下载对象和引用
    *   pull       获取并整合另外的仓库或一个本地分支
    *   push       更新远程引用和相关的对象

 * 命令 'git help -a' 和 'git help -g' 显示可用的子命令和一些概念帮助。
 查看 'git help <命令>' 或 'git help <概念>' 以获取给定子命令或概念的帮助。

## Git 常用命令说明
 * `git init`: 创建版本库，本地创建版本库可通过`git remote add origin git@github.com:youname/repository.git`与远程仓库关联，或者可直接使用`git clone git@github.com:youname/repository.git`将你在github上创建的仓库clone到本地并自动关联
 * `git status`: 查看当前仓库的状态，显示文件修改、工作区状态等信息
 * `git add`: 添加修改或新增文件到暂存区，支持通配符，如可以使用`git add *.java`提交目录下的所以java文件
 * `git mv`: 移动或重命名一个文件、目录或符号链接
 * `git rm`:  使用 `git rm --cached <文件>...` 以取消暂存
 * `git commit`: 将已经添加到暂存区的修改提交到仓库，要求添加提交说明，可通过-m指定:`git commit -m 'you comments'`
 * `git log`: 显示提交日志，即git commit日志
 * `git reset`: 版本回退，已经commit的内容可通过`git reset --hard <commit版本号>`，版本号可通过`git log`查看，使用时只需要提供版本号的前几位，`git log`只能显示当前提交之前的日志，如果之前已经产生过回退，可通过`git reflog`命令查看命令历史，以便确定要回到未来的哪个版本; `git reset HEAD file`撤销已经提交到暂存区的文件
 * `git branch`: `git branch -a`查看本地和远程分支; `git brach -d`删除本地分支，强制删除使用-D; `git branch -r`查看远程分支
 * `git checkout`: `git checkout <branch>`切换分支； `git checkout -b <branch>`创建并切换到分支； `git checkout -- <filename>`从版本库中还原工作区文件（没有执行`git add`，已经执行过的通过`git reset HEAD <file>`撤销）
 * `git tag`: 显示tag信息，同`git tag -l`； `git tag <tagname>`新建tag； `git tag -d <tagname>`删除tag
 * `git diff`: 显示提交之间、提交和工作区之间等的差异
 * `git merge`: 合并分支，将当前分支与指定分支合并
 * `git pull`: 从远程库中拉取文件
 * `git push`: 将本地信息推送到远程分支，使用`git push
origin :<branch>`可删除远程分支（慎重使用）； `git push origin <localbranch>:<remotebranch>`将本地分支推送到指定的远程分支； `git push --set-upstream origin <branchname>`关联远程分支; `git push origin <remotebranch>`完成关联后可使用此命令推送
* 其他： `git config --global core.quotepath false`解决中文名称文件显示问题
