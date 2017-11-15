---
title: Feature Branch在Gitlab中的应用
date: 2017-11-14 10:58:42
tags: Git攻略
---

## Feature Branch

Workflow中提倡Feature Branch，最佳实践是为每一个feature单独启用一个branch，在上面进行开发、测试、验收，通过，完全通过即表示该feature开发完成，符合预期，然后何如到Dev分支中。

<!-- more -->

### 工作流程

1. 从dev中分出待开发feature分支：

```
git branch -b dev_feature_name origin/dev
```

> 同理，如果需要从master/release中分出bugfix分支，可以使用相同的命令如下，下文中主要针对new feature进行介绍，所以不再重复说明

```
git branch -b bugfix_bug_descrpition origin/master
```

2. 进行功能开发并Commit

```
git add .
git commit -m "Commit Message..."
```

3. 持续开发，需要进行修改再Commit，使用amend命令直接进行修改commit

```
git add .
git commit --amend
```

> 输入回车后进入Vi编辑器编辑之前的Commit Message，根据需要修改内容并":wq"回车就可以推出并完成提交

4. 重复第三步至开发完成，推送到Gitlab并创建Merge Request

```
git push -u origin dev_feature_name
```

登陆Gitlab创建Merge Request即可

* IMPORTENT * : 为了避免Gitlab上Feature分支过多，创建Merge Request时，请勾选"Remove source branch when merge request is accepted."

创建Merge Request后，根据实际流程进行Code review，功能测试、验收等。如果没问题，即该Feature开发完成。如有问题需要进行修改，则继续往下走。

5. 使用步骤3完成修改并提交到本地，然后再次推送到Gitlab

```
git push -f
```

> 没错，是force commit，由于Gitlab不支持patch set的概念，所以使用force commit达到相同目的。


至此，已经满足我们大多数功能开发。

但是，若需求点功能较大，开发时间较长，第3步中还是不建议简单实用--amend进行修改提交，而是尽量保持开发中细颗粒commit。具体有两种方案，下面进一步继续介绍。

### 大颗粒需求工作流程

其余步骤需求还是与一般需求工作流程一致，这里直接介绍第三步的进一步细化流程

* 方案一：

3.1 持续开发细颗粒任务，以细颗粒为单位进行一般commit，与步骤2一致

3.2 整体功能开发完成，使用交互式rebase命令进行commit合并

```
git rebase -i
```

根据提示，选择合并Commit成一个Commit进行提交。

继续步骤4以及后续流程。

* 方案二：（仅介绍，如非必要保留细颗粒日志不推荐）

分离新分支进行本地开发，再回到本分支使用merge --squash 新分支进行分支合并，并再次提交以及进行后续流程。

### 处理合并冲突

由于开发过程耗时可能比较长，可能存在合并冲突，所以推送至Gitlab建议先rebase一下

```
git pull
git rebase origin/dev
```

> 若rebase有冲突需要先解决然后使用rebase --continue，详细请参考rebase的文档提示

> 为什么使用rebase而不是merge? merge会保留本分支的commit然后合并目标分支，可以简单理解为“并行合并”，而rebase则以目标分支为基点，向后合并本分支的commit，可以理解为"串行合并"，而我们需要保持我们的commit处于最前端以便继续修改commit，所以选用rebase。


## 总结一下

1. 确保Commit的目的原子性
1. 一个Feature一个Branch
1. 确保Commit Message的可读性















