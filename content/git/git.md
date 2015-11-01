---
title: Git CheatSheet
date: 2015-08-21 15:20:11
tag: tools
---
### init
``` bash
git init
```

### config
``` bash
git config --global --user.name=name
git config --global --user.email=name@xxx.xxx
git config --list
```

### operation
``` bash
git status
git diff

git add <file>
git commit -m <message>
git commit -a -m <message>
git commit --amend
git revert <commit_id>

git log --pretty=oneline
git log --graph --pretty=oneline --abbrev-commit
git reflog

git reset --hard <commit_id>

git diff HEAD -- <file> #see the difference between file in workspace and resp.

git checkout -- <file>  #discard changes in working directory
git reset HEAD <file>   #unstage

git rm <file>
git rm --cached <file>  #remove from tracked file list, do not delete it
git rm -r
git mv

git remote -v
git remote add <remotename> <url>
git pull <remotename> <branch>
git push -u <remotename> <brach> #-u is optional
git push <remotename> <localbranch>:<remotebranch>
git clone <repo>
git checkout -b <branchname> <remotename/branchname>
git branch --set-upstream <branchname> <remotename/branchname>
git remote rename <old_name> <new_name>
git remote rm <remotename>


git push <remotename> :<branch> #delete remote branch, v1
git push origin --delete branch #delete remote branch, v2

git branch
git branch <branchname> #create a branch
git checkout <branchname> #switch to another branch
git checkout -b <branchname> #create and switch
git branch -d <branchname> #delete a branch
git branch -D <branchname> #force delete

git merge <branchname> #merge another branch to current branch
git merge --no-ff -m <message> <branchname> #merge, disable fast forward, a new commit


git stash #save working directory and index state
git stash list
git stash apply
git stash drop
git stash pop

git tag <tagname>
git tag <tagname> <commit_id>
git show <tagname>
git push origin <tagname>
git push origin --tags
git tag -d <tagname>
git push origin :refs/tags/<tagname>

git apply, patch, e.g. git diff A B | git apply
```


### notes
Git跟踪并管理的是修改，而不是文件。

```commit id```, 版本号，16进制表示，可通过 ```git log``` 查看。

```HEAD```, 指向的是当前版本，上一个版本即HEAD^, HEAD~

stage（或index）即暂存区，```git add```命令就是将工作区中要提交的修改放到暂存区，然后执行 ```git commit```命令就可以将暂存区中的所有修改提交至当前分支。








