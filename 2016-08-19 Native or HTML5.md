## Native or HTML5?

### 优劣比较
* Native APP
指的是原生程序，一般依托于操作系统，有很强的交互，是一个完整的 APP ，可拓展性强。需要用户下载安装使用。
  - 优点：打造完美的用户体验、性能稳定、操作速度快，上手流畅、访问本地资源（通讯录，相册）、设计出色的动效，转场、拥有系统级别的贴心通知或提醒、用户留存率高。
  - 缺点：开发成本高（不同平台有不同的开发语言和界面适配）、维护成本高（例如一款 APP 已更新至 V5 版本，但仍有用户在使用 V2， V3， V4 版本，需要更多的开发人员维护之前的版本）、更新缓慢，根据不同平台，提交–审核–上线 等等不同的流程，需要经过的流程较复杂。

* Web APP
指采用 Html5 语言写出的 APP，不需要下载安装。类似于现在所说的轻应用。生存在浏览器中的应用，基本上可以说是触屏版的网页应用。
  - 优点：开发成本低、更新快、更新无需通知用户，不需要手动升级、能够跨多个平台和终端。
  - 缺点：临时性的入口、无法获取系统级别的通知，提醒，动效等等、用户留存率低、设计受限制诸多、体验较差。

Html5 通常拿来做推广辅助，做个游戏，有个产品页面之类的宣传
主要还是Html5 渲染不过关，Network Access已经不是问题了

```
其实h5和native各有优劣，
首先是h5的优势：
1. 用h5做的页面迭代速度快，每次就更新服务器的文件用户那头就更新了，不用好像native那样各种提交app store审核，审核不过打回来然后还继续审，万一不小心有bug带出去了又要重新更新一个版本。这是h5的一个巨大的优势——迭代迅速
2. h5现在已经能做越来越多的事，从地理位置获取到传感器获取到陀螺仪，h5的能力已经越来越大，并且相信会变得更大，这让开发者的门槛大大降低，更多的前端可以直接用js就能做出强大的东西——潜力
3. 跨平台。现在要做一个原生的app，至少要一批人做ios一批人做android，说不定做大点，windows phone又要找一批人搞，成本对小团队来说可相当不小，而h5，基本搞定一个就全部通用了。跨平台同样是h5的一个大优势——成本低

优势说完了劣势来了：
1. 说实话h5的性能真的差得可以，处女座表示实在很难接受，之前算是h5负责了比较久，想方设法开硬件加速，减少节点减少请求乱七八糟，是相对效率高了，但是离原生还有很大的距离，这个也不多说了，性能是硬伤——性能性能还是性能
2. 很多h5的页面喜欢模仿原生的来做，往往原生一两句代码就能搞定的东西，用h5做要写一堆css而且还模仿得不像，人家“啪！”那个选择框就弹出来了但在h5里面卡了2下再出来，这就是差别。而且用h5的话控件难以根据系统更改风格，例如ios6,ios7的选择框是不同的，原生的话自动适配但是h5倒没那么好搞了——界面难以做到和原生一样和手机ui统一
3. h5的bug真的呵呵呵呵呵呵，好不容易从ie6怪圈逃出来了又遇到了各种各样的android，甚至ios也会抽风。你说ie6的bug嘛还算有迹可循，百度也有很多沉淀，移动终端的我遇到好多bug根本百度不了，测试那里一堆手机一个个测总有各种各样奇怪的问题，胜在有万能的google和stackoverflow——bugs
```

### 如何判断
1. 断网情况
显示404或则错误页面的是html页面。
2. 页面布局(Android, 开发者选项中显示布局边界)
3. 复制文章的提示
4. 加载方式
如果在打开新页面导航栏下面有一条加载的线的话，这个页面就是H5页面
![](http://img0.tuicool.com/bu6BBfZ.jpg!web)
5. 导航栏是否会有关闭的操作
如果APP顶部导航栏当中出现了关闭按钮或者有关闭的图标，那么当前的页面肯定的H5，原生的不会出现（除非设计开发者故意弄的）.
美团的、大众点评的APp、微信APP当加载h5过多的时候，左上角会出现关闭2字。
6. 判断页面 下拉刷新的时候（前提是要有下拉刷新的功能）
如果界面没有明显刷新现象的是原生的，如果有明显刷新现象（比如闪一下）的是H5页面（ios和android）。
7. 下拉页面的时候显示网址提供方的一定是H5
![](http://img2.tuicool.com/VrQRNvJ.jpg!web)

---
我自己常用的判断方法：
* 体验
一般加载很慢且滑动很不流畅，切换时预加载和模拟动画--css3动画非常的消耗性能
* 剪切板事件
长按文字部分，进入选中之后，把选中的光标上下拖动。html 写出来的页面可以把很多图片、文字等一起选进去，原生的一般仅限于文字区域。
* 抓包

```
大家大谈H5APP时都是快速开发、低成本、多平台等等，但我却觉得它和很多APP开发方式相比有一个不同之处——`图文混合的排版`。正是这些复杂多变的CSS样式消耗了性能，但是它带来了排版的多样性，能够细致到每一个字宽行高和风格的像素级处理，才是H5的优异之处。
很多人都说纯H5A一次编写就能编译Android/iOS两种不同的APP，大大降低了成本。实际上这个观点本身就是值得怀疑的，如果你写过这类APP就能明白，它们既不省事，又存在很多BUG，调试时尤其繁琐。举一个很简单的例子，Android和iOS在返回上一页的处理方式上就有明显的区别，iOS的顶部bar在全屏下怎样处理，Android机器出现smart bar怎样处理页面的布局，调用底层硬件时怎样区分不同的场景等等，你需要写一个又一个机型和系统的判断，然后分别在Android和iOS下调试，最后你却发现这并没有卵用，累的要死却什么没学到，只有一堆不知道什么时候会过时的经验。
```
---