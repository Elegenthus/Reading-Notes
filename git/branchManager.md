* 创建新分支并且切换到新创建的分支
    * git checkout -b <newBranch>
* 创建新分支
    * git branch <newBranch>
* 切换到新分支
    * git checkout <newBranch>
* 查看当前分支
    * git branch
* 合并newBranch到当前分支
    * git merge <newBranch>
* 删除分支newBranch
    * git branch -b <newBranch>
* 合并分支分为两种情况
    * Fast forward模式
        * 默认
        * 分支删除后，丢掉分支信息
        * 看不出曾经做过合并
    * 加上`--no-ff`参数
        * 合并后的历史有分支
* 保存当前分支内容到工作现场（转而开发另一分支）
    * git stash
* 查看工作现场目录
    * git stash list
* 恢复工作现场
    * git stash apply <stash号>
        * stash 内容并不删除，使用git stash drop删除
    * git stash pop
        * 恢复现场同时吧stash内容也删了
* 丢弃删除一个还未被合并的分支
    * git branch -D <name>
* 当git pull失败，就指定本地dev分支与远程origin/dev分支的链接
    * git branch --set-upstream-to=origin/dev dev
* 查看远程库信息
    * git remote -v
* 在本地创建和远程分支对应的分支
    * git checkout -b <name> origin/<name>
* 让交叉的分支变成一条直线
    * git rebase
* 显示历史提交的commit_id
    * git log --pretty=oneline --abbrev-commit
* 打标签
    * git tag <v0.9> <commit_id>
* 查看标签信息
    * git show <tagname>
* 创建带有说明的标签，用-a指定标签名，-m指定说明文字
    * git tag -a <name> -m "explanation" <commit_id>
* 删除标签
    * git tag -d <tagname>
* 推送标签到远程仓库
    * git push origin <tagname>
* 推送全部未推送过的本地标签
    * git push origin --tags
* 删除一个远程标签
    * git push origin :refs/tags/<tagname>
* 添加至`.gitignore`中的文件
    * git add -f <filename>
* 检查`.gitignore`控制下的文件为何不可以上传
    * git check-ignore -v <filename>
* 控制别名
    * git config --global alias.<name> <order>
    * 例：git config --global alias.st status