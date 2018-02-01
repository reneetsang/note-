# git使用

cat readme.txt查看内容

第一步：使用命令 git add readme.txt添加到暂存区里面去
第二步：用命令 git commit告诉Git，把文件提交到仓库实际上，就是把暂存区的所有内容提交到当前分支上
git commit -m "注释"

git status 来查看下结果
git diff readme.txt 想看readme.txt文件到底改了什么内容
git log 查看下历史记录，显示从最近到最远的显示日志
git log –pretty=oneline 显示简短的历史记录
git reflog 获取到版本号，然后git reset --hard 6fcfc89（版本号）来恢复

git reset --hard HEAD^ 把当前的版本回退到上一个版本，上上一个版本就多加一个^
git reset --hard HEAD~100 回退到前100个版本
git checkout -- readme.txt 把readme.txt文件在工作区做的修改全部撤销
	1.readme.txt自动修改后，还没有放到暂存区，使用 撤销修改就回到和版本库一模一样的状态。
	2.另外一种是readme.txt已经放入暂存区了，接着又作了修改，撤销修改就回到添加暂存区后的状态。

git remote add origin https://github.com/tugenhua0707/testgit.git 可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。
git push命令，实际上是把当前分支master推送到远程。
	由于远程库是空的，我们第一次推送master分支时，加上了 –u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
	从现在起，只要本地作了提交，就可以通过如下命令：
	git push origin master

git clone http://.... 克隆一个本地库

git checkout -b dev 创建并切换分支，git checkout 命令加上 –b参数表示创建并切换，相当于如下2条命令
	git branch dev 创建分支
	git checkout dev 切换分支
	git branch查看分支，会列出所有的分支，当前分支前面会添加一个星号。
git merge dev 把dev分支上的内容合并到当前分支上
git branch -d dev 删除dev分支
总结创建与合并分支命令如下：
查看分支：git branch
创建分支：git branch name
切换分支：git checkout name
创建+切换分支：git checkout –b name
合并某分支到当前分支：git merge name
删除分支：git branch –d name

