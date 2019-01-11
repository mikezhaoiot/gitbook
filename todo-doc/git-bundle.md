##### git bundle

做项目过程中，经常遇到需要更新原厂的代码，有的时候发的patch,有的是发的bundle,stackoverflow 中有一个回答描述了两者的区别[^1]

其实bundle将常用，其中Pro Git 一书中打包详细描述具体的方法[^2]这里记录下常用的命令


- 用`git bundle create`命令来打包


```C
$ git bundle create repo.bundle HEAD master
Counting objects: 6, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (6/6), 441 bytes, done.
Total 6 (delta 0), reused 0 (delta 0)
```

然后你就会有一个名为 repo.bundle 的文件，该文件包含了所有重建该仓库 master 分支所需的数据。 在使用 bundle 命令时，你需要列出所有你希望打包的引用或者提交的区间。 如果你希望这个仓库可以在别处被克隆，你应该像例子中那样增加一个 HEAD 引用。

- 用`git bundle create`命令来打包,指定打包区间

```C
$ git bundle create commits.bundle master ^9a466c5
Counting objects: 11, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (9/9), 775 bytes, done.
Total 9 (delta 0), reused 0 (delta 0)
```

- 用`git bundle verify`校验是否合法

```C
$ git bundle verify ../commits.bundle
The bundle contains 1 ref
71b84daaf49abed142a373b6e5c59a22dc6560dc refs/heads/master
The bundle requires these 1 ref
9a466c572fe88b195efd356c3f2bbeccdb504102 second commit
../commits.bundle is okay
```

- 用` git bundle list-heads`列出顶端提交

```C
$ git bundle list-heads ../commits.bundle
71b84daaf49abed142a373b6e5c59a22dc6560dc refs/heads/master
```
- 用` git fetch`导入提交

```C
//从包中取出 master 分支到我们仓库中的 'other-master' 分支：
$ git fetch ../commits.bundle master:other-master
From ../commits.bundle
 * [new branch]      master     -> other-master
```


当你在没有合适的网络或者可共享仓库的情况下，git bundle 很适合用于共享或者网络类型的操作。




git clone mike@192.168.1.201:/home/mike/project/uboot-2015-dev



[^1]:[Difference between git bundle and .patch](https://stackoverflow.com/questions/16407490/difference-between-git-bundle-and-patch)


[^2]:[Pro Git-打包](https://bingohuang.gitbooks.io/progit2/content/07-git-tools/sections/bundling.html)

