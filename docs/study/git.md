# git常用命令

## 1.  初始化数据仓库
   git init  //初始化本地要存放数据的仓库根目录

   本地初始化仓库后，在github上新建一个仓库：
   配置远程仓库地址：
  git remote add origin https://github.com/namedL/git-cli.git

     git remote -v  ##查看远程仓库，fetch为拉取地址，push为推送地址
    推送本地文件到远程仓库：
     git push -u origin master
   -------推送报错，则可执行如下命令，在重新推送-------  
   【报错：Updates were rejected because the remote contains work that you do 
     或者：fatal: refusing to merge unrelated histories   #拒绝合并不相关历史】
  git pull origin master --allow-unrelated-histories   #合并两个仓库的历史

## 2. 拉取远程数据
  git clone [url]   //在远程克隆数据到本地

## 3. 添加（或删除）修改的代码到暂存区
  git add/rm 1.html 2.html *.c      //添加（或删除）修改的代码到暂存区
   git add .    // #添加当前目录下的所有文件到暂存区
   git commit -m 'message'    //添加暂存区数据到本地仓库
   git commit -am 'message' #不加入缓存，直接提交到仓库
   git commit --amend //此命令会让你重新输出提交日志

## 4. git中管理状态
	git status -s   #当前文档在git中管理状态

## 5. 取消加入缓存的文件 
	$ git reset HEAD -- 1.html  #取消加入缓存的文件

## 6.文件 重命名
	git mv README  README.md     //重命名

## 7. 查看提交历史
	$ git log 	//查看提交历史
	$ git log --oneline   #简洁版
	$ git reflog #记录你的每一次命令

## 8. 添加git标签
	$ git tag -a v1.0     //添加git标签
	$ git tag -a v0.9 85fc7e7  #追回已经提交的代码，打上标签

## 9.将更新同步到本地
	$ git merge origin/master   #将更新同步到本地

## 10. 查看指定文件内容
	cat README.md     //查看 README.md 文件内容

## 11. 推送数据到远程
	git push -u origin master #推送数据到远程
	git pull   //获取远程数据  | git pull会进行合并操作，相当于git fetch 和 git merge

## 12. 版本回退
	$ git reset --hard HEAD^   #数据回退到上一个版本， head^^(退回上两个版本)	# ^几个就是几个版本
	$ git reset --hard 3628164  #回到指定版本
	git reset HEAD readme.txt  #git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本


## 13. 撤销修改
	$ git checkout -- readme.txt   
		#工作区的修改全部撤销，用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
		#一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
		#一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
		#总之，就是让这个文件回到最近一次git commit或git add时的状态

## 14. 删除仓库分支
     命令：$ git push origin 【空格】【冒号】【你的分支名字】
      比如我github上有master和feature分支，我现在想着删除feature分支，命令如下：
      $ git push origin :feature

## 15. 仓库分支操作
    1，查看本地分支：
    git branch
    2,查看远程分支
    git branch -r
    3,查看所有分支
    git branch -a
    4，本地创建分支
    git branch [branch name]
    5,切换到新分支
    git checkout [branch name]
    6,创建+切换分支
    git checkout -b [branch name]
    7,将新分支推送到github
    git push origin [branch name]
    8,删除本地分支：
    git branch -d [branch name]
    9，删除github远程分支
    git push origin :[branch name]
    10,clone分支
    	git clone -b <branch> <remote_repo> <目录别名>
    	例子 :git clone -b 1.x http://git.strongsoft.net:6280/devx/TaskManager.git TaskManager_1.x
    11,合并指定分支到当前分支：【此时应该先切换回master分支, 使用参数-no-ff可以存在合并记录，-m添加合并说明】
      git merge --no-ff -m "merge with no-ff" [branch name]    

## 16. 查看 git status详情
      git diff
      执行 git diff 来查看执行 git status 的结果的详细信息。
      git diff 命令显示已写入缓存与已修改但尚未写入缓存的改动的区别。git diff 有两个主要的应用场景。
      尚未缓存的改动：git diff
      查看已缓存的改动： git diff --cached
      查看已缓存的与未缓存的所有改动：git diff HEAD
      显示摘要而非整个 diff：git diff --stat

## 18. 创建文件
	touch 1.html  #touch指创建一个文件

## 19. 创建自己的用户信息
    $ git config --global user.name 'xxx'
    $ git config --global user.email xxx@163.com

## 20. 查看(/配置)自己的用户信息
    $ git config user.name ‘xxx’ 
    $ git config user.email 'xxx@163.com' 

## 21. 解决Git在添加ignore文件之前就提交了项目无法再过滤问题
      $ git rm -r --cached .
      #设置过滤文件
      $ git add .
      $ git commit -m "add ignore"
      $ git push

## 22. vim 退出
【最快捷的方法：按了ESC后，直接按shift+zz，或者切换到大写模式按ZZ，就可以保存退出了，即是按2下大写的Z】
     Esc
     wq #w：write，写入 / q：quit，退出
     q! #不保存退出
     !  #强制退出