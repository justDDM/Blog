# React

## 设计理念

React 目的是用于**快速响应**的大型 Web 应用程序，是一个用于构建用户界面的JavaScript库

制约快速响应的因素有很多，大致是两种:

#### 页面上需要进行大量的计算操作或设备性能不足导致掉帧卡顿

  JS是单线程的**JS引擎**和**GUI引擎**是互斥的（即执行JS和浏览器布局绘制不能同步进行），显示器刷新一帧的时间是16.6ms（60Hz刷新率），当JS计算比较复杂时执行时间 **大于**16.6ms时，这一帧就只能进行JS计算而不能执行样式布局绘制，就会导致页面掉帧卡顿。

#### 网络请求时间不确定性

  网络请求受制于网速、服务端处理时间等等各种不确定因素，当一个请求时间较长时就会产生页面等待的状况。

## React架构

### stack reconciler 架构

React 15以及之前使用的是stack reconciler的架构 [官网]([https://zh-hans.reactjs.org/docs/implementation-notes.html])

架构分为两层：

#### **1. reconciler**：用于找出变化的组件

[reconciler实现说明](/前端/框架/React/React/Reconciler.md)

#### **2. renderer**：将变化的组件更新到视图

renderer 渲染器，不同平台支持不同的渲染器

- ReactDOM Renderer：用于将 React 组件渲染成 DOM

- ReactNatvie Renderer：用于将 React 组件渲染成 Native 视图（在RN内部使用）

- ...

reconciler 和 renderer 是交替工作的，reconciler 发现一个组件更新了就通知 renderer 更新该组件，该交替过程是同步的直到所有组件检查完毕。

#### **<font color="#f40">Stack reconciler 架构缺点</font>**

在 Stack reconciler 中，挂载和更新的情况下都会递归更新子组件（同步更新）。递归过程一旦开始则无法结束，若递归深度很深，递归更新所需要的JS执行时间超过了浏览器一帧的时间，则就会引起页面掉帧卡顿。

::: tip 解决方法
React16 之后使用了新的 **Fiber reconciler** 架构。在新的架构中使用**可中断的异步更新**来代替了**同步更新**
:::

### fiber reconciler 架构

新的架构分为三层：

#### <font color="#f40" >**1. Scheduler: 调度任务优先级**（高优先级优先进入reconciler)</font>

#### **2. reconciler： 负责找出变化的组件**

#### **3. renderer**：将变化的组件更新到视图

:::tip 异步可中断更新
在浏览器每一帧里，预留一部分时间给JS线程、React利用这部分时间更新组件。当时间不够时候，控制权交还给浏览器渲染页面，React等待下一帧开始被中断的工作（时间切片）
:::

递归更新的工作变为了可以中断的循环过程。每次循环都会先调用`shouldYield`判断是否有剩余时间。

React 为了避免中断更新会带来渲染一部分的情况，在新的架构中 reconciler 和 renderer 不是交替工作的。当Scheduler将任务交给 reconciler 时 reconciler 会为变化的 DOM 打上标识标明是增/删/更新的操作。当所有组件都完成了 reconciler 工作后统一交给 renderer 更新视图。