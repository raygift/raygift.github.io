向Githu开源项目提交PR，总共分几步？

主要参考[如何从零开始参与大型开源项目](https://pingcap.com/blog-cn/how-to-contribute/#%E5%A6%82%E4%BD%95%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E5%8F%82%E4%B8%8E%E5%A4%A7%E5%9E%8B%E5%BC%80%E6%BA%90%E9%A1%B9%E7%9B%AE)

# 通用步骤

## 1. fork开源项目
   1. 打开Github，找到开源项目主页，点击右上角“Fork”
   2. 经过数秒的loading后即可完成，页面跳转到forked 之后自己的仓库里

## 2. 将fork 的代码获取到个人开发终端本地
   1. 进入计划存放代码的上级目录，执行如下git clone 命令，命令会自动新建一个项目对应的文件夹

```
git clone git@github.com:raygift/awesome-database-learning.git
```

​		clone 到本地的代码默认在master 分支，且自动将个人github上的仓库作为origin

![image-20210406163357659](https://rayalioss.oss-cn-shanghai.aliyuncs.com/img/image-20210406163357659.png)

## 3. 保持本地代码与原仓库代码一致

*参考[这里](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork)*

为了保持与原项目仓库的一致，将原仓库设为本地代码的upstream

活跃开源项目的源代码会一直在更新，fork或clone来的仓库一旦放置不管就会很快离最新源码越来越远，为了让仓库保持最新状态，需要：

​		1. 将原仓库作为远程仓库，并设置名称，一般名称设为“upstream”

```
git remote add upstream git@github.com:pingcap/awesome-database-learning.git
```

![image-20210406164817414](https://rayalioss.oss-cn-shanghai.aliyuncs.com/img/image-20210406164817414.png)

​		2. 获取最新代码，并与本地分支进行合并

```
git fetch upstream
git merge upstream/master
```

​		3. 将原仓库最新代码更新到自己的远程仓库

完成本地代码的更新后，可以将代码push到自己的远程仓库origin，使得fork 的代码保持最新状态

```
git push
```

## 4. 本地完成代码开发

根据目标完成源码修改，一般目标为一个特定的issue，代码的修改一般使用单独一个分支进行开发

1. 创建特定工作分支

```
git checkout -b workBranch
```

或者

```
git branch workBranch2
```

![image-20210406170208461](https://rayalioss.oss-cn-shanghai.aliyuncs.com/img/image-20210406170208461.png)

![image-20210406170143012](https://rayalioss.oss-cn-shanghai.aliyuncs.com/img/image-20210406170143012.png)

2. 完成代码开发，并提交到本地仓库

```
git add .
git commit -m "modify desc"
```

3. 创建对应的远程工作分支，并完成代码推送

```
git push origin workBranch
```

本步骤中还可使用如下命令，将本地工作分支与远程分支关联起来，后续对此分支再次提交时就无需指定仓库和分支名

```
 git push --set-upstream origin workBranch 
 ....
 git push
```

*--set-upstream 还可以缩写为 -u*，使用如下命令查看本地分支与远程分支对应关系

```
git branch -vv
  master     eb7ae54 [origin/master] Add a learned index Paper (#36)
* workBranch 93904a7 [origin/workBranch] modify 2 desc
```

## 5. 创建pull request

1. 打开Github 个人仓库页面，会有工作分支push记录，以及“ Compare & pull request ”的提醒

![image-20210406173356540](https://rayalioss.oss-cn-shanghai.aliyuncs.com/img/image-20210406173356540.png)

2. 点击“ Compare & pull request ”进入“Open a pull request”，填写PR描述后点击“Create pull request”完成创建。

![image-20210406173623949](https://rayalioss.oss-cn-shanghai.aliyuncs.com/img/image-20210406173623949.png)

3. pr被接受后，自己远程仓库的工作分支可删除，本地工作分支也可删除

# TiDB 专用步骤

在向TiDB 贡献代码时，还需要参考[这篇官方指引(how to contribute)](https://github.com/pingcap/community/blob/master/contributors/README.md)，可以重点关注以下内容：

### - 需要首先签署CLA

TiDB 要求社区代码贡献者在贡献代码前先签署CLA协议

> CLA 是 Contributor License Agreement 的缩写，CLA 可以看做是对开源软件本身采用的开源协议的补充。一般分为公司级和个人级别的 CLA，所谓公司级即某公司代表签署 CLA 后即可代表该公司所有员工都签署了该 CLA，而个人级别 CLA 只代表个人认可该 CLA。
>
> 因为 CLA 是每个公司或组织自定义的，在细节上可能稍有不同，不过都会重点包含这些内容：1. 授予著作权给拥有该软件知识产权的公司或组织；2. 专利许可的授予；3. 签署者保证依法有权授予上述许可；

TiDB 自定义的CLA 的具体内容见[这里][https://cla-assistant.io/pingcap/tidb?pullRequest=16303]，节选一些重点内容如下：

> This license is for your protection as a contributor as well as the protection of PingCAP and its other contributors and users; it does not change your rights to use your own Contributions for any other purpose.
>
> 这个许可是为了保护贡献者以及PingCAP，以及其他贡献者和用户；本许可并不改变你以其他目的使用自己所贡献代码的权利。
>
> Grant of Copyright License. Subject to the terms and conditions of this CLA, You hereby grant to PingCAP and to recipients of software distributed by PingCAP a perpetual, worldwide, non-exclusive, no-charge, royalty-free, irrevocable copyright license to reproduce, prepare derivative works of, publicly display, publicly perform, sublicense, and distribute Your Contributions and such derivative works.
>
> 版权许可的授权。遵守本CLA协议的条款，在此你授权PingCAP 及PingCAP 软件分发的接收者 永久的，全球范围的，非独家的，免费的，免除专利使用费，不可撤回的版权（复制）许可，准备公开展示，公开演播，再授权，分发你的“Contributions”的衍生工作，以及衍生作品。
>
> Grant of Patent License. Subject to the terms and conditions of this CLA, You hereby grant to PingCAP and to recipients of software distributed by PingCAP a perpetual, worldwide, non-exclusive, no-charge, royalty-free, irrevocable (except as stated in this section) patent license to make, have made, use, offer to sell, sell, import, and otherwise transfer the Work, where such license applies only to those patent claims licensable by You that are necessarily infringed by Your Contribution(s) alone or by combination of Your Contribution(s) with the Work to which such Contribution(s) were submitted. If any entity institutes patent litigation against You or any other entity (including a cross-claim or counterclaim in a lawsuit) alleging that Your Contribution, or the Work to which You have contributed, constitutes direct or contributory patent infringement, then any patent licenses granted to that entity under this CLA for that Contribution or Work shall terminate as of the date such litigation is filed.
>
> 专利许可的授权。遵守本CLA协议的条款，在此你授权PingCAP 及PingCAP 软件分发的接收者永久的，全球范围的，非独家的，免费的，免除专利使用费，不可撤回的（除了本章提到的情况外）专利（申请）许可，已经制造，使用，要约销售，销售，导入，以及其他Work的转移，此许可只应用于那些你宣称可获得许可的专利，这类专利是不可避免的被你的“Contribution(s)”独立所破坏，或者被你提交的"Contribution(s)"组合所破坏。如果任何实体实行针对你的专利诉讼，或者任何其他实体（包括交叉诉讼或反诉）宣称你的Contribution或者你贡献的Work，直接构成或促成了专利诉讼，那么已授权的本CLA协议下针对这些Contribution和Work 的任何专利许可将于诉讼提出时随即终止
>
> 

### - 代码、注释以及评论风格上需要遵守几点：

1. 创建issue的描述规范
2. 创建工作分支的命名规范（github的username/代码的module名）
3. 新建pr申请的描述规范（根据创建pr时的注释填写）

### - 自动化测试



