### What
作为开源的Git扩展，Git大文件存储（Large File Storage，简称LFS）的目标是更好地把“大型二进制文件，比如音频文件、数据集、图像和视频”集成到Git的工作流中。

### Why
众所周知，Git在存储二进制文件时效率不高，因为：
Git默认会压缩并存储二进制文件的所有完整版本，如果二进制文件很多，这种做法显然不是最优。
> By default git will compress and store all subsequent full versions of the binary assets, which is obviously not optimal if you have many.


Git LFS处理大型二进制文件的方式是用“文本指针”替换它们。这些文本指针实际上是包含二进制文件信息的文本文件。文本指针存储在Git中，而大文件本身通过HTTPS托管在Git LFS服务器上。
> Git LFS approach to large binary files handling consists in replacing them with "text pointers," i.e, text files containing information to identify the binary file. Text pointers are stored inside Git, while the large files themselves are hosted on a Git LFS server over HTTPS.

** 使用 LFS : 有效的处理大文件 **  <br/>
当然，LFS 并不能像"变魔术一样"处理所有的大型数据：它需要记录并保存每一个变化。然而，这就把负担转移给了远程服务器 - 允许本地仓库保持相对的精简。<br/>
为了实现这个可能，LFS 耍了一个小把戏：它在本地仓库中并不保留所有的文件版本，而是仅根据需要提供检出版本中必需的文件。<br/>
但这引发了一个有意思的问题：如果这些庞大的文件本身没有出现在你的本地仓库中....改用什么来代替呢? LFS 保存轻量级指针中有真实的文件数据。当你用一个这样的指针去迁出一个修订版时，LFS 会很轻易地找到源文件（不在他上面可能就在服务器上，特殊缓存）然后你下载就行了。<br/>
因此，你最终只会得到你真正想要的文件 - 而不是一些你可能永远都不需要冗余数据。

** Git LFS 命令 **  </br>
Git LFS向Git中添加了一条新命令lfs，支持以下参数：
* config：显示Git LFS的配置。
* init：初始化Git LFS。
* logs：显示git-lfs中的错误。
* track：向Git仓库中添加一个大文件；允许指定文件扩展名。
* untrack：从Git LFS中移除一个文件。
* push：把当前监控的文件推送到Git LFS服务器。
* status：显示产生改动的Git LFS对象的路径。
```
config: display Git LFS configuration.
init: initialized Git LFS.
logs: show errors from git-lfs.
track: add a large file to Git repo; it allows to specify a file extension.
untrack: remove a file from Git LFS.
push: push tracked files to Git LFS endpoint.
status: Displays paths of modified Git LFS objects.
```

### How
* 使用LFS追踪文件
```
// 没有特别说明的情况下，LFS 不会处理大文件问题，因此，我们必须明确告诉 LFS 该处理哪些文件。
git lfs track "design-resources/design.psd"   // 让我们回到“大 Photoshop 文件”的示例， 我们可以使用“lfs track”命令来告诉 LFS 处理“design.psd”文件. 乍一看，这条命令好像没生效，不过，你会看到项目根目录下新建了一个新文件 ".gitattributes" （如果已存在，将会被修改），".gitattributes" 文件记录了我们用 LFS 追踪的所有的文件路径。
cat .gitattributes
design-resources/design.psd filter=lfs diff=lfs merge=lfs -text
git add .gitattributes
git add design-resources/design.psd   // 棒棒哒！在这之后 LFS 会处理这个文件。我们接下来只要像往常那样把这个文件提交到仓库。值得注意的是，".gitattributes" 文件也需要提交到仓库，操作和提交其他修改文件一样：
git commit -m "Add design file"
git push origin master
```

* 追踪文件路径
```
// 添加单一文件如上所示就可以。但是，比如说，如果你想追踪项目里所有后缀名为 indd 的文件呢？放心，你不用手动的添加每个文件。LFS 允许你定义文件路径，就像忽略文件时的用法那样。举个例子，下面的命令会告诉 LFS 追踪所有的 InDesign 文件 — 已经存在的和以后添加的。
git lfs track "*.indd"
// 你也可以告诉 LFS 追踪整个文件夹里的所有内容
git lfs track "design-assets/*"
```

* 追踪文件概述
有时候，你可能想知道到底有哪些文件在被 LFS 追踪。你可以简单的看看.gitattributes文件。然而，它们不是真实的文件，而是包含一些规则和理论的文件：某些文件可能会漏掉，例如由于拼写错误或者过分严格的规则。
```
// 想要查看你当前正在追踪的实际文件的列表，可以使用 git lfs ls-files 命令：
git lfs ls-files
194dcdb603 * design-resources/design.psd
```

### When
记住 LFS 并没有改变 git 本身的原理：被提交到仓库中的文件还会留在那儿。改变项目的提交历史很困难（也有风险）。<br/>
这意味着你应该在文件没有提交到仓库前就让 LFS 进行追踪。</br>
不然，它就成了项目历史的一部分 - 令项目增大数 MB 或数 GB 的大小...</br>
初始化仓库时要选择配置要追踪的文件规则的完美时机（就跟配置忽略文件一样）。

---
https://git-lfs.github.com/
[Git LFS Tutorial]https://github.com/git-lfs/git-lfs/wiki/Tutorial

[Git Large File Storage Promises to Extend Git to Large Binary Files]https://www.infoq.com/news/2015/04/github-large-file-storage
[Git大文件存储将帮助Git处理大型二进制文件]http://www.infoq.com/cn/news/2015/04/github-large-file-storage?utm_source=tuicool

[Getting Started with Git LFS]https://about.gitlab.com/2017/01/30/getting-started-with-git-lfs-tutorial/
[Git LFS 入门指南]https://www.oschina.net/translate/getting-started-with-git-lfs-tutorial?cmp
