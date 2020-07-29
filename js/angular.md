## Angular 应用程序的生命周期hooks有哪些？

1. ngOnChanges：当Angular设置其接收当前和上一个对象值的数据绑定属性时响应。
2. ngOnInit：在第一个ngOnChange触发器之后，初始化组件/指令。这是最常用的方法，用于从后端服务检索模板的数据。
3. ngDoCheck：检测并在Angular上下文发生变化时执行。每次更改检测运行时，会被调用。
4. ngOnDestroy：在Angular销毁指令/组件之前清除。取消订阅可观察的对象并脱离事件处理程序，以避免内存泄漏。
5. ngAfterContentInit：组件内容已初始化完成
6. ngAfterContentChecked：在Angular检查投影到其视图中的绑定的外部内容之后。
7. ngAfterViewInit：Angular创建组件的视图后。
8. ngAfterViewChecked：在Angular检查组件视图的绑定之后。

## 使用Angular 2及以上版本，和使用Angular 1相比，有什么优势？

```text
1.Angular 2是一个平台，不仅是一种语言
2.更好的速度和性能
3.更简单的依赖注入
4.模块化，跨平台
5.具备ES6和Typescript的好处。
6.灵活的路由，具备延迟加载功能
7.更容易学习
```
## Angular 中的路由工作原理是什么？

1. 路由是能够让用户在视图/组件之间导航的机制。Angular 2简化了路由，并提供了在模块级（延迟加载）下配置和定义的灵活性。 

2. Angular应用程序具有路由器服务的单个实例，并且每当URL改变时，相应的路由就与路由配置数组进行匹配。在成功匹配时，它会应用重定向，此时路由器会构建ActivatedRoute对象的树，同时包含路由器的当前状态。在重定向之前，路由器将通过运行保护（CanActivate）来检查是否允许新的状态。Route Guard只是路由器运行来检查路由授权的接口方法。保护运行后，它将解析路由数据并通过将所需的组件实例化到<router-outlet></router-outlet>中来激活路由器状态

## Observables和Promises的核心区别是什么？

1. Observable类似于（在许多语言中的）Stream，当每个事件调用回调函数时，允许传递零个或多个事件。
通常Observable比Promise更受欢迎，因为它不但提供了Promise特性，还提供了其它特性。
使用Observable可以处理0,1或多个事件。
你可以在每种情况下使用相同的API。
Observable是可取消的，这相比于Promise也具有优势。
如果服务器的HTTP请求结果或其它一些异步操作不再需要，则Observable的订阅者可以取消订阅，
而Promise将最终调用成功或失败的回调，即使你不需要通知或其提供的结果。
Observable提供像map，forEach，reduce之类的类似于数组的运算符，还有强大的运算符，如retry（）或replay（）等，使用起来是相当方便的

2. Promises：
```text
2.1. 返回单个值
2.2. 不可取消
```

3. Observables：
```text
3.1. 可以使用多个值
3.2. 可取消
3.3. 支持map，filter，reduce和类似的操作符
3.4. ES 2016提议的功能
3.5. 使用反应式扩展（RxJS）
3.6. 根据时间的变化，数组成员可以异步获取
```

## Angular 组件之间通讯方式有哪些？

1. 父组件向子组件传递信息
1.1.在父组件上设置子组件的属性
```text
父组件绑定信息
<app-child childTitle="可设置子组件标题"></app-child>
子组件接收消息
import { Component, OnInit, Input } from '@angular/core';
@Input childTitle: string;
```
1.2.父组件调用子组件的方法

2.子组件向父组件传递信息

2.1.使用 EventEmitter

2.2.使用 ViewChild, 有父组件主动获取

3.不限于父子组件的通过依赖注入Service进行通讯
