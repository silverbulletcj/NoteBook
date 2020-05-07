<center> Git学习 </center>

1. 远程仓库的创建与使用
   * 在github上创建一个新的repository，执行初始化过程，即*git init*
   * 本地电脑通过git clone address来将远程仓库中克隆到本地
   * 为了能够将本地仓库上的修改push到github中，需要将本地电脑生成的SSH Key添加到远程仓库的SSH Keys设置中。生成SSH Key的命令为**ssh-keygen -t rsa -C "youremail@example.com"**，这样能够在本地生成*id_rsa*和*id_rsa.pub*两个文件，分别对应私钥和公钥。
   * 如果是关联远程仓库，则是使用**git remote add**命令，在之后需要推送修改，则可以使用git push orgin master来实现
2. 分支管理
   * 分支的创建使用的指令为**git switch -c dev**，即创建并转到新的分支dev中，单独创建分支则可用**git branch name**命令
   * 分支的合并命令为**git merge dev**，即在当前的分支合并dev中的提交，如果当前分支和合并的分支对同一个文件进行不同的修改，则会导致合并失败，这时需要将其中一个分支的内容编辑为所希望的内容。同时可以用**git log --graph**来查看分支合并图
   * git在分支合并时，会使用Fast forward模式，这样会在删除分支后，丢掉分支信息；要想强行禁止掉Fast forward模式，可以使用**--no-ff**参数进行合并
     * 在bug分支进行修复bug的任务时，常常需要保存当时的环境，此时可以通过**git stash**命令来实现，这会将工作区的内容保存下来。在完成bug修复之后，可以通过**git stash list**来查看保存的内容，之后通过**git stash apply**或者是**git stash pop**来完成恢复工作，前者需要通过**git stash drop**来将stash里的内容删除。
3. 常用命令
   * git log    #显示提交日志
   * git reset --hard HEAD^.   #版本退回到上个版本（一个^代表的是上一个版本）
   * git checkout --file.    #撤销在工作区中的文件的修改，即回到最近一个git add或者是git commit的状态
   * git reset HEAD <file>      #将暂存区中的修改撤销

