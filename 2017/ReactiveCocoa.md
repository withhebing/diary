### ReactiveCocoa

ReactiveCocoa 的版本演进  <br />
<= v2.5 ：Objective-C  <br />
   v3.x ：Swift 1.2  <br />
   v4.x ：Swift 2.x  <br />

#### 层次结构

![层次结构](http://blog.leichunfeng.com/images/ReactiveCocoa%20v2.5.png)

四大核心组件:
* 信号源：RACStream 及其子类；
* 订阅者：RACSubscriber 的实现类及其子类；
* 调度器：RACScheduler 及其子类；
* 清洁工：RACDisposable 及其子类。

其中，信号源又是最核心的部分，其他组件都是围绕它运作的。

对于一个应用来说，绝大部分的时间都是在等待某些事件的发生或响应某些状态的变化，比如用户的触摸事件、应用进入后台、网络请求成功刷新界面等等，而维护这些状态的变化，常常会使代码变得非常复杂，难以扩展。而 ReactiveCocoa 给出了一种非常好的解决方案，它使用信号来代表这些异步事件，提供了一种统一的方式来处理所有异步的行为，包括代理方法、block 回调、target-action 机制、通知、KVO 等

````objc
// 代理方法
[[self
    rac_signalForSelector:@selector(webViewDidStartLoad:)
    fromProtocol:@protocol(UIWebViewDelegate)]
    subscribeNext:^(id x) {
        // 实现 webViewDidStartLoad: 代理方法
    }];

// target-action
[[self.avatarButton
    rac_signalForControlEvents:UIControlEventTouchUpInside]
    subscribeNext:^(UIButton *avatarButton) {
        // avatarButton 被点击了
    }];

// 通知
[[[NSNotificationCenter defaultCenter]
    rac_addObserverForName:kReachabilityChangedNotification object:nil]
    subscribeNext:^(NSNotification *notification) {
        // 收到 kReachabilityChangedNotification 通知
    }];

// KVO
[RACObserve(self, username) subscribeNext:^(NSString *username) {
    // 用户名发生了变化
}];
````

真正强大的地方在于我们可以对这些不同的信号进行任意地组合和链式操作，从最原始的输入 input 开始直至得到最终的输出 output 为止：

````objc
[[[RACSignal
    combineLatest:@[ RACObserve(self, username), RACObserve(self, password) ]
    reduce:^(NSString *username, NSString *password) {
      return @(username.length > 0 && password.length > 0);
    }]
    distinctUntilChanged]
    subscribeNext:^(NSNumber *valid) {
        if (valid.boolValue) {
            // 用户名和密码合法，登录按钮可用
        } else {
            // 用户名或密码不合法，登录按钮不可用
        }
    }];
````

#### 信号源

在 ReactiveCocoa 中，信号源代表的是随着时间而改变的值流，这是对 ReactiveCocoa 最精准的概括，订阅者可以通过订阅信号源来获取这些值：
> Streams of values over time.

###### RACStream

RACStream 是 ReactiveCocoa 中最核心的类，代表的是任意的值流，它是整个 ReactiveCocoa 得以建立的基石

![RACStream继承结构图](http://blog.leichunfeng.com/images/RACStream.png)

事实上，RACStream 是一个**抽象类**，通常情况下，我们并不会去实例化它，而是直接使用它的两个子类 **RACSignal** 和 **RACSequence** 。

###### RACSignal

RACSignal 代表的是未来将会被传送的值，它是一种 push-driven 的流。

RACSignal 可以向订阅者发送三种不同类型的事件：
* next ：RACSignal 通过 next 事件向订阅者传送新的值，并且这个值可以为 nil ；
* error ：RACSignal 通过 error 事件向订阅者表明信号在正常结束前发生了错误；
* completed ：RACSignal 通过 completed 事件向订阅者表明信号已经正常结束，不会再有后续的值传送给订阅者。

注意，ReactiveCocoa 中的值流只包含正常的值，即通过 next 事件传送的值，并不包括 error 和 completed 事件，它们需要被特殊处理。通常情况下，一个信号的生命周期是由任意个 next 事件和一个 error 事件或一个 completed 事件组成的。

RACSignal 并非只有一个类，事实上，它的一系列功能是通过**类簇**来实现的。  <br />
除去我们将在下节介绍的 RACSubject 及其子类外，RACSignal 还有五个用来实现不同功能的私有子类：
* RACEmptySignal ：空信号，用来实现 RACSignal 的 `+empty` 方法；
* RACReturnSignal ：一元信号，用来实现 RACSignal 的 `+return:` 方法；
* RACDynamicSignal ：动态信号，使用一个 block 来实现订阅行为，我们在使用 RACSignal 的 `+createSignal:` 方法时创建的就是该类的实例；
* RACErrorSignal ：错误信号，用来实现 RACSignal 的 ``+error:`` 方法；
* RACChannelTerminal ：通道终端，代表 RACChannel 的一个终端，用来实现双向绑定。

对于 RACSignal 类簇来说，最核心的方法莫过于 ``-subscribe:`` 了，这个方法封装了订阅者对信号源的一次订阅过程，它是订阅者与信号源产生联系的唯一入口。因此，对于 RACSignal 的所有子类来说，这个方法的实现逻辑就代表了该子类的具体订阅行为，是区分不同子类的关键所在。同时，这也是为什么 RACSignal 中的 -subscribe: 方法是一个抽象方法，并且必须要让子类实现的原因：

````objc
- (RACDisposable *)subscribe:(id<RACSubscriber>)subscriber {
  NSCAssert(NO, @"This method must be overridden by subclasses");
  return nil;
}
````

###### RACSubject

RACSubject 代表的是可以手动控制的信号，我们可以把它看作是 RACSignal 的可变版本，就好比 NSMutableArray 是 NSArray 的可变版本一样。RACSubject 继承自 RACSignal ，所以它可以作为信号源被订阅者订阅，同时，它又实现了 RACSubscriber 协议，所以它也可以作为订阅者订阅其他信号源，这个就是 RACSubject 为什么可以手动控制的原因：

![RACSignal and RACSubject](http://blog.leichunfeng.com/images/RACSubject.png)

RACSubject 也有三个用来实现不同功能的子类：  <br />
* RACGroupedSignal ：分组信号，用来实现 RACSignal 的分组功能；
* RACBehaviorSubject ：重演最后值的信号，当被订阅时，会向订阅者发送它最后接收到的值；
* RACReplaySubject ：重演信号，保存发送过的值，当被订阅时，会向订阅者重新发送这些值。

RACSubject 的功能非常强大，但是太过灵活，也正是因为如此，我们只有在迫不得已的情况下才会使用它。一旦过度使用，就会使代码变得非常复杂，难以理解。

###### RACSequence

RACSequence 代表的是*一个不可变的值的序列*，与 RACSignal 不同，它是 pull-driven 类型的流。从严格意义上讲，RACSequence 并不能算作是信号源，因为它并不能像 RACSignal 那样，可以被订阅者订阅，但是它与 RACSignal 之间可以非常方便地进行转换。

从理论上说，一个 RACSequence 由两部分组成：
* head ：指的是序列中的第一个对象，如果序列为空，则为 nil ；
* tail ：指的是序列中除第一个对象外的其它所有对象，同样的，如果序列为空，则为 nil 。

事实上，一个序列的 tail 仍然是一个序列，如果我们将序列看作是一条毛毛虫，那么 head 和 tail 可表示如下：
![RACSequence's head and tail](http://blog.leichunfeng.com/images/listmonster.png)

同样的，一个序列的 tail 也可以看作是由 head 和 tail 组成，而这个新的 tail 又可以继续看作是由 head 和 tail 组成，这个过程可以一直进行下去。而这个就是 RACSequence 得以建立的理论基础，所以一个 RACSequence 子类的最小实现就是 head 和 tail

总的来说，RACSequence 存在的最大意义就是为了简化 Objective-C 中的集合操作：
> Simplifying Collection Transformations: Higher-order functions like map, filter, fold/reduce are sorely missing from Foundation.

````objc
// 通常实现
NSMutableArray *results = [NSMutableArray array];
for (NSString *str in strings) {
    if (str.length < 2) {
        continue;
    }

    NSString *newString = [str stringByAppendingString:@"foobar"];
    [results addObject:newString];
}
````

````objc
// 使用RACSequence实现
RACSequence *results = [[strings.rac_sequence
    filter:^ BOOL (NSString *str) {
        return str.length >= 2;
    }]
    map:^(NSString *str) {
        return [str stringByAppendingString:@"foobar"];
    }];
````

因此，我们可以非常方便地使用 RACSequence 来实现集合的**链式操作**，直到得到你想要的最终结果为止。除此之外，使用 RACSequence 的另外一个主要好处是，RACSequence 中包含的值在默认情况下是**懒计算**的，即只有在真正用到的时候才会被计算，并且只会计算一次。也就是说，如果我们只用到了一个 RACSequence 中的部分值的时候，它就在不知不觉中提高了我们应用的性能。

同样的，RACSequence 的一系列功能也是通过**类簇**来实现的，它共有九个用来实现不同功能的私有子类：

* RACUnarySequence ：一元序列，用来实现 RACSequence 的 ``+return:`` 方法；
* RACIndexSetSequence ：用来遍历索引集；
* RACEmptySequence ：空序列，用来实现 RACSequence 的 ``+empty`` 方法；
* RACDynamicSequence ：动态序列，使用 blocks 来动态地实现一个序列；
* RACSignalSequence ：用来遍历信号中的值；
* RACArraySequence ：用来遍历数组中的元素；
* RACEagerSequence ：非懒计算的序列，在初始化时立即计算所有的值；
* RACStringSequence ：用来遍历字符串中的字符；
* RACTupleSequence ：用来遍历元组中的元素。

RACSequence 为类簇提供了统一的对外接口，对于使用它的客户端代码来说，完全不需要知道私有子类的存在，很好地隐藏了实现细节。另外，值得一提的是，RACSequence 实现了快速枚举的协议 NSFastEnumeration

#### 订阅者

为了获取信号源中的值，我们需要对信号源进行订阅。在 ReactiveCocoa 中，订阅者是一个抽象的概念，所有实现了 RACSubscriber 协议的类都可以作为信号源的订阅者。

###### RACSubscriber

四个方法必须实现的方法中, ``-sendNext:`` 、``-sendError:`` 和 ``-sendCompleted` 分别用来从 RACSignal 接收 next 、error 和 completed 事件，而 ``-didSubscribeWithDisposable:`` 则用来接收代表某次订阅的 disposable 对象。

订阅者对信号源的一次订阅过程可以抽象为：通过 RACSignal 的 -subscribe: 方法传入一个订阅者，并最终返回一个 RACDisposable 对象的过程：
![订阅者对信号源的一次订阅过程](http://blog.leichunfeng.com/images/subscribe.png)

注意：在 ReactiveCocoa 中并没有专门的类 RACSubscription 来代表一次订阅，而间接地使用 RACDisposable 来充当这一角色。因此，一个 RACDisposable 对象就代表着一次订阅，并且我们可以用它来取消这次订阅.

除了 RACSignal 的子类外，还有两个实现了 RACSubscriber 协议的类，RACSubscriver 和 RACPassthroughSubscriber.  

其中，RACSubscriber 类的名字与 RACSubscriber 协议的名字相同，这跟 Objective-C 中的 NSObject 类的名字与 NSObject 协议的名字相同是一样一样的，除了名字相同外，然并卵。通常来说，RACSubscriber 类充当的角色就是信号源的真正订阅者，它老老实实地实现了 RACSubscriber 协议。

既然 RACSubscriber 类就是真正的订阅者，那么 RACPassthroughSubscriber 类又是干嘛用的呢？原来，在 ReactiveCocoa 中，一个订阅者是可以订阅多个信号源的，也就是说它会拥有多个 RACDisposable 对象，并且它可以随时取消其中任何一个订阅。为了实现这个功能，ReactiveCocoa 就引入了 RACPassthroughSubscriber 类，它是 RACSubscriber 类的一个装饰器，封装了一个真正的订阅者 RACSubscriber 对象，它负责转发所有事件给这个真正的订阅者，而当此次订阅被取消时，它就会停止转发：
````objc
- (void)sendNext:(id)value {
  if (self.disposable.disposed) return;
  [self.innerSubscriber sendNext:value];
}

- (void)sendError:(NSError *)error {
  if (self.disposable.disposed) return;
  [self.innerSubscriber sendError:error];
}

- (void)sendCompleted {
  if (self.disposable.disposed) return;
  [self.innerSubscriber sendCompleted];
}
````

事实上，在 ReactiveCocoa 中，我们倾向于隐藏订阅者，因为外界根本不需要知道订阅者的存在，这是内部的实现细节。这样做的主要目的是进一步简化信号源的订阅逻辑，客户端代码只需要关心它所需要的值就可以了，根本不需要关心内部的订阅过程。

###### RACMulticastConnection

通常来说，我们在订阅一个信号源的过程中可能会产生副作用或者消耗比较大的资源，比如修改全局变量、发送网络请求等。这个时候，我们往往需要让多个订阅者之间共享一次订阅

RACMulticastConnection 通过一个标志 `_hasConnected` 来保证只对 sourceSignal 订阅一次，然后对外暴露一个 RACSubject 类型的 signal 供外部订阅者订阅。这样一来，不管外部订阅者对 signal 订阅多少次，我们对 sourceSignal 的订阅至多只会有一次：
![RACMulticastConnection](http://blog.leichunfeng.com/images/RACMulticastConnection.png)

#### 调度器

有了信号源和订阅者，我们还需要由调度器来统一调度订阅者订阅信号源的过程中所涉及到的任务，这样才能保证所有的任务都能够合理有序地执行。

###### RACScheduler

RACScheduler 在 ReactiveCocoa 中就是扮演着调度器的角色，本质上，它就是用** GCD 的串行队列**来实现的，并且支持取消操作。是的，在 ReactiveCocoa 中，并没有使用到 NSOperationQueue 和 NSRunloop 等技术，RACScheduler 也只是对 GCD 的简单封装而已。

同样的，RACScheduler 的一系列功能也是通过**类簇**来实现的，除了用来测试的子类外，总共还有四个私有子类：
* RACImmediateScheduler ：立即执行调度的任务，这是唯一一个支持**同步**执行的调度器；
* RACQueueScheduler ：一个抽象的队列调度器，在一个 GCD 串行列队中异步调度所有任务；
* RACTargetQueueScheduler ：继承自 RACQueueScheduler ，在一个以一个任意的 GCD 队列为 target 的串行队列中异步调度所有任务；
* RACSubscriptionScheduler ：一个只用来调度订阅的调度器。

值得一提的是，在 RACScheduler 中有一个非常特殊的方法：``- (RACDisposable *)scheduleRecursiveBlock:(RACSchedulerRecursiveBlock)recursiveBlock;``
这个方法的作用非常有意思，它可以将递归调用转换成迭代调用，这样做的目的是为了解决深层次的递归调用可能会带来的堆栈溢出问题。

#### 清洁工

在订阅者订阅信号源的过程中，可能会产生副作用或者消耗一定的资源，所以当我们在取消订阅或者完成订阅时，我们就需要做一些资源回收和垃圾清理的工作。

###### RACDisposable

在订阅者订阅信号源的过程中，可能会产生副作用或者消耗一定的资源，所以当我们在取消订阅或者完成订阅时，我们就需要做一些资源回收和垃圾清理的工作。

RACDisposable

RACDisposable 在 ReactiveCocoa 中就充当着清洁工的角色，它封装了取消和清理一次订阅所必需的工作。它有一个核心的方法 ``-dispose`` ，调用这个方法就会执行相应的清理工作，这有点类似于 NSObject 的 -dealloc 方法。RACDisposable 总共有四个子类：

* RACSerialDisposable ：作为 disposable 的容器使用，可以包含一个 disposable 对象，并且允许将这个 disposable 对象通过原子操作交换出来；
* RACKVOTrampoline ：代表一次 KVO 观察，并且可以用来停止观察；
* RACCompoundDisposable ：跟 RACSerialDisposable 一样，RACCompoundDisposable 也是作为 disposable 的容器使用。不同的是，它可以包含多个 disposable 对象，并且支持手动添加和移除 disposable 对象，有点类似于可变数组 NSMutableArray 。而当一个 RACCompoundDisposable 对象被 disposed 时，它会调用其所包含的所有 disposable 对象的 -dispose 方法，有点类似于 autoreleasepool 的作用;
* RACScopedDisposable ：当它被 dealloc 的时候调用本身的 -dispose 方法。


#### Summary

通过介绍 ReactiveCocoa 四大核心组件，对它的架构有了宏观上的认识。它建立于 Monad 的概念之上，然后围绕其搭建了一系列完整的配套组件，它们共同支撑了 ReactiveCocoa 的强大功能。尽管，ReactiveCocoa 是一个重型的函数式响应式框架，但是它并不会对我们现有的代码构成侵略性，我们完全可以在一个单独的类中使用它，哪怕只是简单的一行代码，也是没有问题的。


---

extracted from  <br />
[ReactiveCocoa v2.5 源码解析之架构总览, by 雷纯锋](http://blog.leichunfeng.com/blog/2015/12/25/reactivecocoa-v2-dot-5-yuan-ma-jie-xi-zhi-jia-gou-zong-lan/)

something else  <br />
[ReactiveCocoa Design Guidelines](https://github.com/ReactiveCocoa/ReactiveCocoa/blob/v2.5/Documentation/DesignGuidelines.md#avoid-using-subjects-when-possible)
