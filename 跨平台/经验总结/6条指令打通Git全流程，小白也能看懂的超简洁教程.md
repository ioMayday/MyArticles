
**目录**
[toc]

标题：6条指令打通Git全流程，小白也能看懂的超简洁教程

## 远程建仓，本地拉取代码
----

```bash
git clone https地址或ssh地址
git checkout branch_name ## 将远端分支branch_nanme同步到本地同名分支上
```

## 代码提交
----

```bash
git pull origin               // 与远端仓库同步对齐
git add .                     // 将修改提交到工作区
git commit -m "..."           // 提交到本地仓库存储区，可以包含换行; -m表示commit后有附带的message信息，在"..."里
git push private branch_name  // 推到远端仓库的branch分支
```

**至此**，仅用6条指令即可完成Git操作全流，已能满足新手日常操作。遇到其他需求和问题时，再进一步地研究此工具就行了。

**提示**，若指令git review无法提交：**替换git review** ，直接网页fork一个，上传到个人私仓，然后再网页请求通过私仓合并入主干，在网页建立MergeRequest，添加reviewer即可。

```bash
git remote add private https://......git
git remote -v
git remote add private ssh:/.......git
git status
git commit --amend   ## 强制修改之前填的commit说明
git push -f private branch_name 
```
上传前先用 `git status` 确定代码仓最新情况，如不是最新的要用 `git pull origin` 同步最新代码后再提交。

## 版本回退
----

- 网页上关闭**merge request**请求
- **soft**将回退到某个版本，但回退前的那个版本并没有删除。
 指令：  `git reset --soft HEAD^`
- 或，**hard**强制删除，撤销并删除之前本地工作区所有修改及提交内容。
 指令： `git reset --hard HEAD^`
- 最后，重新走一遍提交流程，指令如下：
   `git add .  `
   `git commit "说明"`
   `git push -f ...   #注意要添加-f，表示强制同步到远端`


## 其他操作
----

 - **一句搞定，修改远程仓库地址**

```bash
git remote -v  ## 查看origin的原远端仓库地址
git remote set-url origin https://192.168.100.235:9797/john/git_test.git  #设置远端仓库新地址
```

## 扩展参考
----
1. [GitHub建仓及远端同步超简洁步骤总结](https://blog.csdn.net/qq_17256689/article/details/112505349)
2. [Git教程 | 菜鸟教程](https://www.runoob.com/git/git-tutorial.html)
3. [进阶：Git远程仓库地址变更本地如何修改](https://blog.csdn.net/asdfsfsdgdfgh/article/details/54981823)
4. [进阶：git本地版本回退与远端版本回退(回滚)](https://blog.csdn.net/tsq292978891/article/details/78965693)
