### MVC

![MVC](http://blog.leichunfeng.com/images/M-V-C.png)

![M-VC](http://blog.leichunfeng.com/images/M-VC.png)
经常被调侃为 Massive View Controller

### MVVM

![MVVM](http://blog.leichunfeng.com/images/M-V-VM.png)

* view ：由 MVC 中的 view 和 controller 组成，负责 UI 的展示，绑定 viewModel 中的属性，触发 viewModel 中的命令；
* viewModel ：从 MVC 的 controller 中抽取出来的展示逻辑，负责从 model 中获取 view 所需的数据，转换成 view 可以展示的数据，并暴露公开的属性和命令供 view 进行绑定；
* model ：与 MVC 中的 model 一致，包括数据模型、访问数据库的操作和网络请求等；
* binder ：在 MVVM 中，声明式的数据和命令绑定是一个隐含的约定，它可以让开发者非常方便地实现 view 和 viewModel 的同步，避免编写大量繁杂的样板化代码。

### ReactiveCocoa

尽管，在 iOS 开发中，系统并没有提供类似的框架可以让我们方便地实现 binder 功能，不过，值得庆幸的是，GitHub 开源的 RAC ，给了我们一个非常不错的选择。

RAC 是一个 iOS 中的函数式响应式编程框架，它受 Functional Reactive Programming 的启发，是在开发 GitHub for Mac 过程中的一个副产品，它提供了一系列用来组合和转换值流的 API 。

我们可以使用 RAC 来在 view 和 viewModel 之间充当 binder 的角色，优雅地实现两者之间的同步。此外，我们还可以把 RAC 用在 model 层，使用 Signal 来代表异步的数据获取操作，比如读取文件、访问数据库和网络请求等。

---

我们只要将 MVC 中的 controller 中的展示逻辑抽取出来，放置到 viewModel 中，然后通过一定的技术手段，比如 RAC 来同步 view 和 viewModel ，就完成了 MVC 到 MVVM 的转变。

优点:
* 由于展示逻辑被抽取到了 viewModel 中，所以 view 中的代码将会变得非常轻量级；
* 由于 viewModel 中的代码是与 UI 无关的，所以它具有良好的可测试性；
* 对于一个封装了大量业务逻辑的 model 来说，改变它可能会比较困难，并且存在一定的风险。在这种场景下，viewModel 可以作为 model 的适配器使用，从而避免对 model 进行较大的改动。

对于 MVVM 来说，我们可以把 view 看作是 viewModel 的可视化形式，viewModel 提供了 view 所需的数据和命令。因此，viewModel 的可测试性可以帮助我们极大地提高应用的质量。

---

extracted from  <br />
[MVVM With ReactiveCocoa, by 雷纯锋](http://blog.leichunfeng.com/blog/2016/02/27/mvvm-with-reactivecocoa/)
