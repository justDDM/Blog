# Reconciler

:::tip 协调器
React 通过 reconciler 来找出变化的组件和变化的部分
:::


## 工作阶段

- Reconciler 协调器工作阶段称为 <font color="#f40" >render 阶段</font>

- Renderer 渲染器工作阶段称为 <font color="#f40" >commit 阶段</font>

## Stack reconciler

Stack reconciler 在挂载和更新的情况下都会递归更新子组件（同步更新），递归过程一旦开始则无法结束。

> [React官网实现说明](https://zh-hans.reactjs.org/docs/implementation-notes.html)

### 流程

1. ReactDOM把`<App/>`传递给 reconciler

2. reconciler 检查`<App/>`是类/函数

    - 类：调用 `new App(props)` 来实例化 `App`，并调用 `componentWillMount`，之后调用 `render` 方法获取渲染的内容

    - 函数：调用 `App(props)` 执行函数获取渲染的内容

3. 遇到宿主元素会挂载宿主元素（例如 ReactDOM 会创建一个 DOM 节点）

4. 如果宿主元素有子元素则会递归渲染元素（App内可能会包含其他元素，其他元素可能也会包含元素，子组件的 DOM 会附加在父 DOM 节点上）

5. 完成整个DOM结构组装


### 工厂函数

React 的组件分为两种、对于两种元素 React 通过不同的方法进行处理。通过`type`属性进行区分。

- 组合元素(Composite element): 即正常编写的 React 组件，大写开头，例如 `<App>`

- 宿主元素(Host element): 内置的组件，例如 `<div></div>`、`<p></p>`

<img src="/前端/框架/React/React工厂函数.png" style="border: 1px solid black;" ></img>

```js
function instantiateComponent(element) {
  var type = element.type;
  if (typeof type === 'function') {
    // 用户定义组件
    return new CompositeComponent(element);
  }else if (typeof type === 'string') {
    // 平台特定组件
    return new DOMComponent(element);
  }
}
```

React通过不同的类对元素进行处理，相较于函数优点在于在**实例**上可以**存储**一些需要使用的信息。

---

### 挂载

#### Composite Component

:::tip 组合元素
组合元素中类组件和函数组件处理方法不同
:::

`CompositeComponent` 提供 `mount` 函数用于挂载组合元素

`mount` 函数通过 `type` 判断元素是类组件/函数组件，获取渲染的内容
，然后**递归挂载**子元素

- `constructor` 阶段将元素存储在 `currentElement`

- `mount` 过程将元素实例存储在 `publicInstance`

- 递归子组件返回的实例保存在 `renderedComponent`

#### Host Component (DOMComponent)

::: tip 宿主元素
  对于宿主元素需要特殊处理
:::

1. 获取子元素数组

2. 按照当前元素创建 DOM 元素并保存

3. 设置属性

4. 创建并保存子项

5. 挂载子项，并保存子项 `mount` 挂载后返回的结点

6. 返回 DOM 结点、

7. 将完整的 DOM 树挂载到容器节点中

<img src="/前端/框架/React/React处理组合元素.png" style="border: 1px solid black" />

::: warning 挂载的结果
挂载的结果取决于渲染器 renderer。 ReactDOM 是挂载 DOM 节点，而 ReactNative 就是一个代表原生视图的数字
:::

---

### 卸载

对于组合组件可以调用生命周期（`componentWillUnMount`）方法，然后递归卸载

### 更新

> reconciler 目标是尽可能复用现有实例来保留 DOM 和状态
>
> virtual DOM diffing

#### 更新组合组件

使用新的 prop 重新渲染组件，获取下一次渲染的元素，查看下次渲染元素的 `type` 属性

- `type`未改变：就地更新

- `type`改变：卸载组件实例，挂载并渲染新的实例（因为一旦元素类型都变了，肯定要重新渲染）

- `key`更改单独处理


#### 更新宿主属性

1. 首先更新DOM属性

2. 更新子组件（宿主元素可能包含多个子组件，类似内部实例数组 按照type是否改变进行更新或者替换）


## Fiber reconciler

Fiber reconciler 支持异步可中断的更新，即更新可以被中断（高优先级任务可以插队，时间片用完可以被打断先去执行绘制），恢复之后可以继续按照被打断时候的状态继续执行。

> 原生的 generate 支持类似的实现，但是因为[某些原因](https://github.com/facebook/react/issues/7942#issuecomment-254987818)被放弃了

### 含义

- 静态数据结构：每个 `Fiber` 节点对应一个 `React` 元素，保存了该组件的类型、DOM信息等属性

- 动态工作单元：每个 `Fiber` 节点保存了本次更新中该组件改变的状态、要执行的工作等（删除/插入/更新...）

### 结构

Fiber 通过三个属性将多个 `Fiber` 节点连成树

- `return`（指向父`Fiber`）

- `child`（指向子`Fiber`)

- `sibling`（指向右边第一个兄弟`Fiber`）

通过 `tag`、`key`、`elementType` 等属性保存组件相关信息

通过 `memoizedProps`、`updateQueue` 等属性保存更新相关信息

通过 `lanes`、`childLanes` 保存调度优先级相关信息

### 工作原理

:::tip 双缓存
在内存中构建并直接替换
:::

#### 双缓存 Fiber 树

- 屏幕渲染的树 `current Fiber`树

- 内存中构建的树 `workInProgress Fiber`树

两个树通过 `alternate` 属性相连，`React` 通过 `current` 指针在这两颗 `Fiber` 树切换完成 `current Fiber` 指向，即指向哪棵树哪棵树就是渲染的树，另一棵树就变为内存中构建的树，每次更新状态都会更新内存中的 `workInProgress Fiber` 树，更新后 `current` 指针指向内存中的树将其变为渲染的树，重新渲染更新页面。

### 挂载

1. 首次执行 `ReactDOM.render` 会创建 `fiberRoot`、`rootFiber`

  - `fiberRoot`：整个应用的根节点，只有一个。（通过current指针指向页面已经渲染内容的Fiber树，即`current Fiber`树，首次挂载时候没有节点所以为空）

  - `rootFiber`：组件树（多次调用`ReactDOM.render`可以生成多个）

2. 进行 <font color="#f40" >render 阶段</font>，根据组件返回的 `JSX` 在内存中创建 `Fiber` 节点并连接构建 Fiber树（`workInProgress Fiber`树）

  - 构建 `workInProgress Fiber` 树时候会尝试**复用** `current Fiber` 树内的 `Fiber` 节点

3. 构建完的 `workInProgress Fiber` 树会在 <font color="#f40" >commit 阶段</font>渲染到页面。

<img style="border: 1px solid black;" src="/前端/框架/React/Reconciler-Fiber-mount.jpeg">

4. 渲染到页面后 `fiberRoot` 的 `current` 指针会指向下图右边的 `workInProgress Fiber` 树，该树就**变为** `current Fiber`树了

<img style="border: 1px solid black; width: 50%" src="/前端/框架/React/Reconciler-Fiber-mounted.jpeg">

### 更新

1. 当触发更新时候，会重新开启一次新的 <font color="#f40" >render 阶段</font> 构建一棵新的 `workInProgress Fiber`树。构建的时候可以复用 `current Fiber`树的节点数据。

::: tip Diff
是否复用 `current Fiber` 树的过程就是 Diff 算法
:::

<img style="border: 1px solid black;" src="/前端/框架/React/Reconciler-Fiber-update.jpeg">

2. 在 <font color="#f40" >render 阶段</font>完成了构建后进入 <font color="#f40" >commit 阶段</font> 渲染到页面上，并完成 `current` 指针的切换
