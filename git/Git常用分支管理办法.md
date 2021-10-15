# Git常用分支管理办法

目前使用度最高的工作流：

- Git Flow
- GitHub Flow
- GitLab Flow

其中 Git Flow 出现的最早，**GitHub Flow 在 Git Flow 的基础上做了一些优化**，适用于持续版本的发布，**而 GitLab Flow 出现的时间比较晚**，所以综合前面两种工作流的优点，制定而成的一个工作流。

## Git Flow

Git Flow 的分支结构很特别，按功能来说，可以分为5种分支，从5 种分支的生命时间上，又可以分别归类为**长期分支和暂时分支**，或者更贴切描述为，**主要分支和协助分支**。

### 主要分支

在采用 Git Flow 工作流的项目中，代码的中央仓库会一直存在以下两个长期分支（主要分支）：

- Master：对外发布的最新版本代码
- Develop：最新的开发进度代码

当 develop 上的代码达到一个稳定的状态，可以发布版本的时候，develop上这些修改会以某种特别方式（Release Branch）被合并到 master 分支上，然后标记上对应的版本标签。

### 协助分支

除了主要分支，Git Flow 的开发模式还需要一系列的协助分支，来帮助并行开发，简化功能开发和问题修复

- Feature Branch：用来做分模块功能开发，建议命名为`feature-xxx`，模块完成之后，会合并到 develop 分支，然后删除。

- Release Branch：用来做版本发布的预发布分支，建议命名为 `release-xxx`。

  例如在软件 1.0.0 版本的功能全部开发完成，提交测试之后，从 develop 检出release-1.0.0 。如果测试中出现小问题，则在 release 分支进行修改提交，测试完毕准备发布的时候，将其**合并到 master 和 develop，在master 分支合并后打上对应版本标签 v1.0.0, 最后删除自己**。这样做的好处是**在测试的时候，不影响下一个版本功能并行开发**。

- Hotfix Branch：用来做线上的**紧急 bug 修复的分支**，一般命名为`hotfix-xxx`。当线上某个版本出现了问题，将检出对应版本的代码，创建 Hotfix 分支。问题修复后，合并回 master 和 develop 。最后删除自己

![git-flow](https://gitee.com/ma-yunfei/picture/raw/master/img/git-flow.jpg)

master 分支每合并一个分支，无论是 hotfix 还是 release ,都会打一个版本标签（小版本标签 0.1 0.2 ...）。

## GitHub Flow



![github-flow](https://gitee.com/ma-yunfei/picture/raw/master/img/github-flow.png)

GitHub Flow特点如下：

- **只有一个长期分支 master ,而且 master 分支上的代码永远是可发布状态**，一般 master 会设置 protected 分支保护，只有有权限的人才能推送代码到 master 分支。
- 如果有新功能开发，可以从 master 分支上检出新分支new-branch
- 当开发完毕，想合并分支时，可以发起一个 pull request。
- 当 review 或者讨论通过后，代码会合并到目标分支（master）。
- **一旦合并到 master 分支，应该立即发布**

### Pull Request

当你想更正别人仓库里的错误时，要走一个流程：

- 先 fork 别人的仓库，相当于拷贝一份（不能直接修改原仓库）

- clone 到本地分支，做一些 bug fix

- 发起 pull request 给原仓库，让他看到你修改的 bug

- 原仓库 review 这个 bug，如果是正确的话，就会 merge 到他自己的项目中

## GitLab Flow

GitLab Flow有个非常大的优势：集百家之长，补百家之短。

GitLab 既支持 Git Flow 的分支策略，也有 GitHub Flow 的 Pull Request（ Merge Request ） 和 issue tracking。

Gitlab flow 分成两种情况应对不同的开发流程：

- 持续发布
- 版本发布

### GitLab flow：带环境分支（持续发布）

针对GitHub里面只有一个Master分支的情况，从需要发布的环境的角度出发，**添加了 pre-production 和 prodution 分支**都对应不同的环境。这个分支策略比较适用服务端，**测试环境，预发环境，正式环境，每个环境建一个分支**。

只有当master稳定到一定程度才部署到pre-production，在pre-production上充分测试，测试完成后再移到production

![gitlab-flow](https://gitee.com/ma-yunfei/picture/raw/master/img/gitlab-flow.png)

### 上游优先

代码合并的顺序：按环境依次推送，确保代码被充分测试过,才会从上游分支合并到下游分支。除非是很紧急的情况，才允许跳过上游分支，直接合并到下游分支。这种规则称之为 **“upstream first”**，也就是 “上游优先”。

### GitLab flow：带发布分支（版本发布）

**只有需要向外界发布软件时，才需要使用发布分支**。每一个稳定版本，都要从master分支拉出一个分支，比如2-3-stable、2-4-stable等等。发现问题，就从对应版本分支创建修复分支，**完成之后，先合并到 master，才能cherry-pick到版本分支**，并且还需要更新版本号，**遵循 “上游优先” 原则**。

![rebase_branches](https://gitee.com/ma-yunfei/picture/raw/master/img/rebase_branches.png)

## 主线开发

主线开发模型是同一个产品的所有的开发人员共享一个 trunk，开发人员可以有自己的私有分支，但所有修改最后都会回到主干，只有在 Release 时才会创建 Release Branch，由 Release Engineer 进行维护，**发布分支是主干某个时点的快照，必要时通过 cherry-pick 从主干挑拣代码到发布分支**。基于主干的开发的优点是**保证了所有用户看到的都是同一份代码的最新版本**，**避免了合并分支时的麻烦**。![主线开发](https://gitee.com/ma-yunfei/picture/raw/master/img/%E4%B8%BB%E7%BA%BF%E5%BC%80%E5%8F%91.webp)

## 优劣比较

**Git flow**

优点：清晰可控

缺点：相对复杂，且需要频繁切换分支。如果在版本快速迭代的专案中，是**几乎用不到Hotfix 和Release 分支的**，因为合并到master后如果有bug就直接修复且发布下个版本了，**如果还使用这两个分支，需要合并回develop和master分支，但实际上开发者很常忘记合并回这两个主分支**。

**GitHub flow**

优点：适合项目是”持续发布”类型（单分支master），

缺点：由于master为最新代码，但是**准被发布版本不一定是用最新代码，或是有固定时间才发布更新的项目**，就会有影响(例如:发布ios，时会审核master时，但审核期间如果有新版本推上会造成审核延迟)。**如果只有一个master主分支就会不够用，因此还得另外再建一个新分支来维护**。

**Gitlab flow**

Gitlab flow 是 Git flow 与 Github flow 的综合。结合了两者的优点，有开发环境上的弹性又有单一主分支的方便。master分支的分支不够，于是增加了一个 prodution 分支，专门用来发布版本。



# Git二次开发

## nvie/gitflow

https://github.com/nvie/gitflow

- 基于bash的gitflow脚步库
- 其中包含gitflow的思想，被以下的库所运用



## gitflow 0.5.1

https://pypi.org/project/gitflow/

- 最近发布：2013
- 支持语言：python2.7 
- GitFlow二次开发的起始库，以下二次开发的库都是在该库的基础上Fork

## nu-gitflow 1.0.2

- 最新发布：2016年（相对其他库较新）
- 支持语言：python2 python3
- 相对其他库较新，但有些辅助库版本较低，例如用于单元测试的unitest2，很多单元测试跑不通，但核心代码应该没问题（具体还需要测试）

![image-20210111113734691](C:\Users\mhp\AppData\Roaming\Typora\typora-user-images\image-20210111113734691.png)

## auto-git-flow 1.0.2

- 在nu-gitflow基础上做了进一步的封装
- release feature名称都是自动产生，利用命令行的模式时，可用缩写表示输出，例如：`gfrs # instance for "git flow release start X.X+1.X"`
- 文字说明方面过度自动化，不利于对所做需求的理解