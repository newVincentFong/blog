# Gitt团队协作流程
## 前言
为了使本文简洁，需要读者已经有Git基础，省略Git命令太细的解释
## 一、当拿到一个公司项目
```git clone http://xxx.xxx.xx/xxx.git```

在你想要存放项目的目录下，命令行clone一下

等价于执行了下面一连串命令
```
git init                              //创建一个空的.git
git remote add http://...             //给.git添加远程仓库信息
git fetch                             //将远程仓库拉到.git的origin
git checkout -b master origin/master  //创建一个master分支，内容为origin的master，HEAD放到master上
```
clone的时候一般会弹窗要求登陆远程仓库，用公司帐号登就行。SSH同理，反正就是一种认证方式。
## 二、完善你在团队协作中的资料
```
git config --local user.name "Eric Dale"              //你在团队中的昵称
git config --local user.email "eric2019@gmail.com"    //你的联系方式，出bug了能找你
```
有了以上信息，每次提交，别人都能在commit log中看到是你

一般用 `--local` 就好，联系方式局限于每个独立的git项目，毕竟你除了公司的东西还有自己的一点Github小项目，甚至前东家的项目

用 `--global` 一个邮箱走遍天下不太行得通，公司有各自的企业邮。
## 三、先熟悉一下远程仓库里的分支构成、提交规范
### 常见的分支管理方案有：
* `master`  核心的分支，没什么好说的，合并需要有权限的leader审核
* `release` 对外发布的分支，最稳定，测过没问题的代码才放上去，通常落后于master，带有版本号或者日期，同样合并需要审核
* `dev`    研发部门的分支，变化最频繁，最新的feature写好了就合并上去，等待测试人员的测试
* `test`   测试部门的分支，测-改-测-改...
* `bug`    改bug而拉出来的分支，寿命最短，通常改好了就合上去，关闭分支

以上分支仅表示种类，一般团队都需要这几类分支，但具体不一定按上面的命名，要自己发现清楚
### 提交规范：
没这个的，通常公司都不咋地。Leader懒得去约束大家，手下的人提交的时候就地随便写一句'update'、'改bug'...
出了问题翻log不好定位。

这个提供一篇 [Angular规范](https://segmentfault.com/a/1190000009048911) 的文章，常用开头feat、bug、style、chore...感觉够用了
## 四、接到需求开始写代码
* 假设公司直接以 `master` 分支作为 `dev` 分支去使用
* 先在远程仓库上，从`master`上拉出一个需求分支
* 在本地的git上，更新一下 `master`

```git pull master origin/master```

等价于

```
git fetch                        //将远程仓库更新到本地的origin
git merge master origin/master   //将origin的master分支，合并到本地master
```
* 然后在本地的 `master` 分支拉出一条命名相同的需求分支，人工去保证本地与远程有一对一的映射
* 此后，短时间内的代码写好提交了，就可以对本地的需求分支与远程的需求分支随意 `git pull` 、 `git push` 了

## 五、危险的Merge，频发的冲突，怎么处理
* 合并类操作是git最危险的时刻之一，`Merge` 、 `Rebase` 都算是合并类
* 假设两名前端人员在开发一个大模块，两个人同时修改了本地的一个地方，但两个人修改的值不一样
* 最先推送上远程分支的那位同事没什么问题，但后推送的同事要遭殃
* 第二名同事 `push` 自己代码前，要先 `pull` 已被更新的远程分支，而 `pull` 涉及 `merge` 操作
* git扫描到那个同时修改的地方，就会不知道怎么 `merge` ，合并操作就会暂停，分支处于`未合并完`的状态
