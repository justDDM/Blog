# Render

Reconciler 的工作阶段即为 Render 阶段。

render 阶段开始于 `performSyncWorkOnRott` 或 `performConcurrentWorkOnRoot` 方法。唯一区别是前者会调用 `shouldYield` 方法判断是否有剩余时间。

## 工作流程

### beginWork

从 `rootFilber` 开始向下深度优先遍历，遍历到的节点调用 `beginWork`，传入当前 `Fiber` 节点，创建出一个子 `Fiber` 节点，并且将两个 `Fiber` 节点连接起来。一直到遍历到叶子节点后结束。

### completeWork

当某个 `Fiber` 节点遍历到叶子节点后会调用 `completeWork` 方法处理 `Fiber` 节点，当执行完 `completeWork`后

- 如果存在 `sibling Fiber` 节点（兄弟节点）则会进入兄弟节点的 `beginWork` 流程。

- 如果不存在 `sibling Fiber` 节点，则会进入父 `Fiber` 节点的 `completeWork`。

整个 `beginWokr` 和 `completeWork` 交替直到最后执行完 `rootFiber` 的 `completeWork`，结束 `render` 阶段工作。

```js
function App() {
  return (
    <div>
      <span>name</span>
      <span>ddm</span>
    </div>
  )
}
```

<div style="display:flex">
  <img src="/前端/框架/React/Render-dom.jpeg" style="width: 50%; border: 1px solid black" />
  <img src="/前端/框架/React/Render-render.jpeg" style="width: 50%; border: 1px solid black" />
</div>

## beginWork

> [源码](https://github.com/facebook/react/blob/970fa122d8188bafa600e9b5214833487fbf1092/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L3058) 

`beginWork` 需要传入当前 `Fiber` 节点，创建子 `Fiber` 节点

`mount` 的时候由于没有组件所以 `Fiber` 节点为空，传入的即为 `null`，因此可以通过 `current` 是否为 `null` 判断是挂载还是更新。

- 更新：满足一定条件的话，可以复用 `current` 节点的内容，可以直接使用 `current.child` 作为新的 `workInProgress.child`，不需要新建 `workInProgress.child`。

  - `props` 和 `type` 不变时候可以复用，不用新建 `Fiber`

  - 当前 `Fiber` 节点优先级不够

- 挂载：除了应用的根节点 `fiberRoot`，会根据 `fiber.tag` 不同，创建不同类型的子 `fiber` 节点。


### reconcileChildren

beginWork 处理到常用的组件类型（函数组件、类组件、宿主组件等）最后会进入一个 `reconcileChildren`函数

该函数完成了 `reconciler` 核心的工作部分，生成新的子 `Fiber` 节点赋值给 `workInProgress.child`

- mount: 创建新的子 `Fiber` 节点

- update: 与上次更新的 `Fiber` 节点进行 **diff**，将比较结果生成新的 `Fiber` 节点

### effectTag

render 阶段在内存中执行，当执行完毕后会通知 renderer 渲染器执行 DOM 操作。

renconciler 阶段会将需要进行的操作标记在 `effectTag` 属性上，由 renderer 统一处理。

:::tip 首屏加载处理
由于首屏加载时候每个 `Fiber` 节点都**没有** DOM 节点在页面上，所以按照道理会把每个 `fiber` 节点的 `effectTag` 属性标记为需要插入，在`commit`阶段每个 `Fiber` 节点**都进行**一次插入DOM操作，这样大量操作会比较效率低，因此特殊处理在 mount 的时候**只有 `rootFaber` 根节点**有插入的标记，只会执行一次挂载根节点操作。
:::

---

<img src="/前端/框架/React/Reconciler-beginWork.jpeg" style="border: 1px solid black;">

## completeWork

### HostComponent

> 宿主元素处理

根据 `current` 是否为`null` 判断是 mount 还是 update，然后根据 `worInProgress.stateNode` 属性是否为 `null`，判断是否存在对应 DOM 节点。

- 更新

  - update 时已经存在 DOM 不需要生成 DOM 节点，需要处理Props

    1. 事件注册
    2. style props
    3. innerHTML props(DANGEROUSLY_SET_INNER_HTML)
    4. children prop
    5. ...
  
  - 更新主要调用了 `updateHostComponent` 方法，处理完的 props 会赋值给 `workInProgress.updateQueue`，然后在 commit 阶段渲染在页面上。

- 挂载

  - 挂载主要逻辑

    1. 为 `Fiber` 节点生成 DOM 节点
    2. 将子孙 DOM 插入刚生成的 DOM 节点
    3. 处理 props (类似update)

当 `completeWork` 执行到 `rootFiber` 时候就已经构件好了一个 DOM 树，但是并未挂载到页面上。等到 commit 阶段渲染到页面上。

### effectList

reconciler 在 render 阶段已经处理完 mount 或者 update，对应的 `fiber` 也已经标记了 `effectTag` 属性，commit 阶段不需要再次遍历 `fiber` 树去找到打了标记的 `fiber` 节点进行操作。

`completeWork`的上层函数中，每个执行完的 `fiber` 节点若存在 `effectTag`，则会被保存在一个 `effectList` 单向链表中。因此 commit 阶段只需要遍历 `effectList` 即可处理所有的 `effct` 了。

<img src="/前端/框架/React/Reconciler-completeWork.jpeg" style="border: 1px solid black;" >

render 阶段完成，然后会将应用的根节点 `fiberRoot` 传递给 `commitRoot`方法，进行 commit 阶段流程。

