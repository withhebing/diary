### 响应式编程 (Reactive Programming)

相关框架: Rx, Bacon.js, RAC

响应式思维

响应式编程, 为异步数据流编程, 是一种面向数据流和变化传播的编程范式, 数据更新是相关联的

创建(create)流, 将流进行组合(combine) 和 过滤(filter); input, merge, filter, map

!["点击按钮"事件流](http://i.imgur.com/cL4MOsS.png)

一个流就是一个**将要发生的以时间为序的事件**序列. 它能发射出三种不同的信号: 一个数据值(data value, 某种类型的), 一个错误(error) 或 一个完成(completed)的信号

监听流的行为叫做**订阅**, 观察者设计模式

e.g. 假设实现一个双击事件流(两次或两次以上)

![Multi clicks stream](http://i.imgur.com/HMGWNO5.png)

响应式编程提高了代码的抽象层次, 这样就可以专注于业务逻辑的事件定义, 而不是尝尝捣鼓那些大量的实现细节. RP的代码可能会更简洁清晰.

![Response metastream](http://i.imgur.com/HHnmlac.png)



---

[通俗解释什么是响应式编程？](http://www.jdon.com/48275)


----

### Reactive Cocoa

iOS开发中事件: Target, Delegate, KVO, 通知, 时钟, 网络异步回调

ReactiveCocoa用信号接管了iOS中的所有事件.

![ReactiveCocoa特征](http://upload-images.jianshu.io/upload_images/1156719-245a898374fcc8c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/658)

![next completed error](http://upload-images.jianshu.io/upload_images/1156719-a270eb8904562dc0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/581)

RAC特点: 通过block函数式+链式的编程, 可以让所有相关的代码继承在一起.
使用时需要注意循环引用, @weakify(self) / @strongify(self) 组合解除循环引用


---

### FRP

![Reactive -- Flicker](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/1.png)

![构建 Reactive System](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/2.png)

![FRP Code](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/3.png)

![Callback Style Code](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/4.png)

![Event Stream](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/5.png)

![Behavior](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/6.png)

![](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/7.png)

![](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/8.png)

![filter](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/9.png)

![map](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/10.png)

![merge](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/11.png)

![until](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/12.png)

![then](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/13.png)

![lift](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/14.png)

![FRP编程 -- 乐高积木 -- 组合能力Composability](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/15.png)

![Callback风格 -- event-driven框架](https://res.infoq.com/articles/functional-reactive-programming/zh/resources/16.png)


reference to [函数式反应型编程(FRP) —— 实时互动应用开发的新思路, by 邓际锋](http://www.infoq.com/cn/articles/functional-reactive-programming)


---

被观察者的keyPath值变化(注意：单纯改变其值不会调用此方法，只有通过getters和setters来改变值才会触发KVO)，就会在观察者里调用方法observeValueForKeyPath:ofObject:change:context:

但是KVO只能检测类中的属性，并且属性名都是通过NSString来查找，编译器不会帮你检错和补全，纯手敲所以比较容易出错。
