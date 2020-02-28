* git diff file1
    * 查看file1的在提交的两个版本之间不同
* git log 
    * 查看提交历史记录
    * --pretty=oneline
        * 只剩下提交名称和提交id
* git reflog 
    * 查看所有的操作命令的历史记录
* git reset --hard commit_id
    * 回退到commit_id版本
* git checkout -- file
    * 撤销工作区的修改
    * 没有git add
* git reset HEAD <file>
    * 已经git add
    * 撤销暂存区的修改
* git rm <file>
    * 从版本库中删除
    * 之后git commit
* git remote add origin git@server-name:path/repo-name.git
    * 例：git remote add origin git@github.com:username/repo-name.git
    * 用于关联一个远程库