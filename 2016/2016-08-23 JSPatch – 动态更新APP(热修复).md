## JSPatch – 动态更新iOS APP

> http://www.cocoachina.com/ios/20150709/12468.html

JSPatch只需在项目中引入极小的引擎，就可以使用JavaScript调用任何Objective-C的原生接口，获得脚本语言的能力：动态更新APP，替换项目原生代码修复bug。

#### 用途

是否有过这样的经历：新版本上线后发现有个严重的bug，可能会导致crash率激增，可能会使网络请求无法发出，这时能做的只是赶紧修复bug然后提交等待漫长的AppStore审核，再盼望用户快点升级，付出巨大的人力和时间成本，才能完成此次bug的修复。

使用JSPatch可以解决这样的问题，只需在项目中引入JSPatch，就可以在发现bug时下发JS脚本补丁，替换原生方法，无需更新APP即时修复bug。

#### 范例
暂略

#### 原理

JSPatch用iOS内置的JavaScriptCore.framework作为JS引擎，但没有用它JSExport的特性进行JS-OC函数互调，而是通过Objective-C Runtime，从JS传递要调用的类名函数名到Objective-C，再使用NSInvocation动态调用对应的OC方法。详细的实现原理以及实现过程中遇到的各种坑和hack方法会另有文章介绍。

#### 方案对比

目前已经有一些方案可以实现动态打补丁，例如WaxPatch，可以用Lua调用OC方法，相对于WaxPatch，JSPatch的优势是：

1.JS语言

JS比Lua在应用开发领域有更广泛的应用，目前前端开发和终端开发有融合的趋势，作为扩展的脚本语言，JS是不二之选。

2.符合Apple规则

JSPatch更符合Apple的规则。iOS Developer Program License Agreement里3.3.2提到不可动态下发可执行代码，但通过苹果JavaScriptCore.framework或WebKit执行的代码除外，JS正是通过JavaScriptCore.framework执行的。

3.小巧

使用系统内置的JavaScriptCore.framework，无需内嵌脚本引擎，体积小巧。

4.支持block

wax在几年前就停止了开发和维护，不支持Objective-C里block跟Lua程序的互传，虽然一些第三方已经实现block，但使用时参数上也有比较多的限制。

相对于WaxPatch，JSPatch劣势在于不支持iOS6，因为需要引入JavaScriptCore.framework。另外目前内存的使用上会高于wax，持续改进中。

#### 风险

JSPatch让脚本语言获得调用所有原生OC方法的能力，不像web前端把能力局限在浏览器，使用上会有一些安全风险：

1. 若在网络传输过程中下发明文JS，可能会被中间人篡改JS脚本，执行任意方法，盗取APP里的相关信息。可以对传输过程进行加密，或用直接使用https解决。

2. 若下载完后的JS保存在本地没有加密，在未越狱的机器上用户也可以手动替换或篡改脚本。这点危害没有第一点大，因为操作者是手机拥有者，不存在APP内相关信息被盗用的风险。若要避免用户修改代码影响APP运行，可以选择简单的加密存储。

#### 其他用途

JSPatch可以动态打补丁，自由修改APP里的代码，理论上还可以完全用JSPatch实现一个业务模块，甚至整个APP，跟wax一样，但不推荐这么做，因为：

1. JSPatch和wax一样都是通过Objective-C Runtime的接口通过字符串反射找到对应的类和方法进行调用，这中间的字符串处理会损耗一定的性能，另外两种语言间的类型转换也有性能损耗，若用来做一个完整的业务模块，大量的频繁来回互调，可能有性能问题。
2. 开发过程中需要用OC的思维写JS/Lua，丧失了脚本语言自己的特性。
3. JSPatch和wax都没有IDE支持，开发效率低。

若想动态为APP添加模块，目前React Native给出了很好的方案，解决了上述三个问题：

1. JS/OC不会频繁通信，会在事件触发时批量传递，提高效率。（详见React Native通信机制详解）
2. 开发过程无需考虑OC的感受，遵从React框架的思想进行纯JS开发就行，剩下的事情React Native帮你处理好了。
3. React Native连IDE都准备好了。

所以动态添加业务模块目前还是推荐尝试React Native，但React Native并不会提供原生OC接口的反射调用和方法替换，无法做到修改原生代码，JSPatch以小巧的引擎补足这个缺口，配合React Native用统一的JS语言让一个原生APP时刻处于可扩展可修改的状态。

目前JSPatch处于开发阶段，稳定性和功能还存在一些问题。
