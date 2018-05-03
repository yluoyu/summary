
```
echo "# test-001" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/yluoyu/test-001.git
// fatal:refusing to merge unrelated histories
// 解决远程仓库与本地仓库pull 失败的问题
git pull origin master --allow-unrelated-histories

// branch绑定 先指定远程分支 然后再推送
// git branch --set-upstream-to=origin/master master
// git push

// -u 直接指定
git push -u origin master
```

```
// 编辑配置文件
git config --global -e

// 保存密码
git config –global credential.helper store
// 默认保存路径 ~/.git-credentials
```

### 1. 概念

**HEAD**

是当前分支版本顶端的别名，也就是在当前分支你最近的一个提交

HEAD 就是你当前的工作目录所处的位置，可以用 checkout 命令改变 HEAD 指向的位置。注意 HEAD 不一定指向一个分支，也可以指向一个 commit

**INDEX** 缓存区

index也被称为staging area，是指一整套即将被下一个提交的文件集合。他也是将成为HEAD的父亲的那个commit

**Working Copy** 工作区

working copy代表你正在工作的那个文件集

**文件状态**

1）Untracked files → 文件未被跟踪；

2）Changes to be committed → 文件已缓存，这是下次提交的内容；

3）Changes bu not updated → 文件被修改，但并没有添加到缓存区。
```
    git init
    git add . // 把当前目录下所有文件添加到暂存区
    除了添加文件他会监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件。

    git add -u 他仅监控已经被add的文件（即tracked file），他会将被修改的文件提交到暂存区。add -u 不会提交新文件（untracked file）。（git add --update的缩写）

    git add -A 是上面两个功能的合集（git add --all的缩写）

    git remote add origin <你的项目地址> //注:项目地址形式为:https://gitee.com/xxx/xxx.git


    git commit -m [message]
    使用一次新的commit，替代上一次提交
    如果代码没有任何新变化，则用来改写上一次commit的提交信息
    git commit --amend -m [message]

    git commit -m //只会提交添加到缓存区的文件（只提交添加的）

    git commit -a //是把unstaged的文件变成staged（这里不包括新建(untracked)的文件），然后commit
    相当于
    git add
    git commit


    重做上一次commit，并包括指定文件的新变化
    git commit --amend [file1] [file2]
  ```

git checkout
```
git checkout
最简单的用法，显示工作区，暂存区和HEAD的差异：
如果让HEAD文件指向一个commit id，那就变成了detached HEAD。git checkout 可以达到这个效果

git checkout -- filename
用暂存区中filename文件来覆盖工作区中的filename文件。相当于取消自上次执行git add filename以来（如果执行过）的本地修改

git checkout branch -- filename
维持HEAD的指向不变。用branch所指向的提交中filename替换暂存区和工作区中相   应的文件。注意会将暂存区和工作区中的filename文件直接覆盖

git checkout -- . 或写作 git checkout .
注意git checkout 命令后的参数为一个点（“.”）。这条命令最危险！会取消所有本地的  修改（相对于暂存区）。相当于用暂存区的所有文件直接覆盖本地文件
```
Git错误non-fast-forward后的冲突解决

出现原因在于：git仓库中已经有一部分代码，所以它不允许你直接把你的代码覆盖上去，两种解决方法

- git push -f 即强推
- git pull

```
git config branch.master.remote origin
git config branch.master.merge refs/heads/master
git pull

//上面这两条命令和下面这条相等 建议用下面
git branch --set-upstream-to=origin/master master

fatal:refusing to merge unrelated histories

git pull origin master --allow-unrelated-histories

git revert 放弃某次提交
git reset 是回滚到某次提交
git reset --soft 此次提交之后的修改会被退回到暂存区
git reset --hard 此次提交之后的修改不做任何保留，git status干净的工作区

git rebase 当两个分支不在一条直线上，需要执行merge操作时，使用该命令操作
```

缓存当前改动 暂不提交

    git stash //缓存
    do some work // 做其他事情
    git stash pop // 把缓存的再释放出来

reset soft,hard,mixed之区别

git reset命令是用来将当前branch重置到另外一个commit的，而这个动作可能会将index以及work tree同样影响

如果你仔细研究reset命令本身就知道，它本身做的事情就是重置HEAD(当前分支的版本顶端）到另外一个commit

reseet 命令 主要有两个作用

1. 文件从暂存区回退到工作区 相当于git add的逆操作
    git reset filename
2. 版本回退
```
    git reset HEAD^ ：回退版本，一个^表示一个版本，可以多个，另外也可以使用 git reset HEAD～n这种形式
    如果HEAD指针指向的是master分支，那么HEAD还可以换成master，如果知道特定的commit-id，那么还可以直接使用 git reset commit-id 如果不加参数，实际上使用的是默认的参数mixed

    git reset --soft HEAD～1 意为将版本库软回退1个版本，所谓软回退表示将本地版本库的头指针全部重置到指定版本，且将这次提交之后的所有变更都移动到暂存区

    默认的mixed参数：git reset HEAD～1 意为将版本库回退1个版本，将本地版本库的头指针全部重置到指定版本，且会重置暂存区，即这次提交之后的所有变更都移动到未暂存阶段

    hard参数：git reset --hard HEAD～1 意为将版本库回退1个版本，但是不仅仅是将本地版本库的头指针全部重置到指定版本，也会重置暂存区，并且会将工作区代码也回退到这个版本

    --soft、--mixed和--hard对文件层面的git reset毫无作用，因为缓存区中的文件一定会变化，而工作目录中的文件一定不变。

  git log -3来查看最近三次的提交

```
清空未保存的文件

    git clean -fd
    经常和reset配合使用 因为默认reset的mixed参数 并不会清空工作区

git rebase 和merger

    git merge b
    # 将b分支合并到当前分支

同样 git rebase b，也是把 b分支合并到当前分支

在这里，你可以用"pull"命令把"origin"分支上的修改拉下来并且和你的修改合并； 结果看起来就像一个新的"合并的提交"(merge commit):

但是，如果你想让"mywork"分支历史看起来像没有经过任何合并一样，你也许可以用 `git rebase`:
`$ git checkout mywork`
`$ git rebase origin`

这些命令会把你的"mywork"分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把"mywork"分支更新 为最新的"origin"分支，最后把保存的这些补丁应用到"mywork"分支上。

在rebase的过程中，也许会出现冲突(conflict). 在这种情况，Git会停止rebase并会让你去解决 冲突；在解决完冲突后，用"git-add"命令去更新这些内容的索引(index), 然后，你无需执行 git-commit,只要执行:
`$ git rebase --continue`
这样git会继续应用(apply)余下的补丁。
在任何时候，你可以用--abort参数来终止rebase的行动，并且"mywork" 分支会回到rebase开始前的状态。
`$ git rebase --abort`

**reset与checkout异同点**

1. reset强调，撤销

2. checkout强调，替换
```
git checkout [- -] filename
用暂存区的内容替换工作区的文件
git checkout head filename
用head指向的目录（版本库）替换暂存区和工作区的文件
```
**Revert**

Revert撤销一个提交的同时会创建一个新的提交。这是一个安全的方法，因为它不会重写提交历史

相比git reset，它不会改变现在的提交历史。因此，git revert可以用在公共分支上，git reset应该用在私有分支上。

git revert当作撤销已经提交的更改，而git reset HEAD用来撤销没有提交的更改
