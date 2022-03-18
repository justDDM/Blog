# CSS

## 水平垂直居中

```html
<head>
  <style>
  /* 基础样式 */
  body > div:nth-of-type(1) {
    width: 500px;
    height: 500px;
    border: 1px solid black;
  }
  body > div:nth-of-type(1) > div {
    width: 100px;
    height: 100px;
    border: 1px solid red;
  }
</style>
</head>

<body>
  <div class="wrap" >
    <div class="box" ></div>
  </div>
</body>
```

### 未知宽高

**<font color="#f40">1. Flex ☆</font>**

```css
/* 外层容器 */
.wrap {
  /* flex */
  display: flex;
  align-items: center;
  justify-content: center;
}
/* 居中元素 */
.box {}
```

**<font color="#f40">2. grid</font>**

```css
/* 方式1 */
.wrap {
  display: grid;
}

.box {
  align-self: center;
  justify-self: center;
}

/* 方式2 */
.wrap {
  justify-content: center;
  align-items: center;
}
.box {}
```

**<font color="#f40">3. margin + translate</font>**

```css
.wrap {
  /* 父元素 相对定位 */
  position: relative;
}

.box {
  /* 子元素绝对定位 */
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

### 已知宽高

**<font color="#f40">4. 负值margin + absolute</font>**

```css
.wrap {
  position: relative;
}

.box {
  position: absolute;
  top: 50%;
  left: 50%;
  margin: -50px 0 0 -50px;
}
```

**<font color="#f40">5. margin (auto) + absolute</font>**

```css
.wrap {
  position: relative;
}

.box {
  position: absolute;
  margin: auto;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
}
```

**<font color="#f40">6. absolute + calc</font>**

```css
.wrap {
  position: relative;
}

.box {
  position: absolute;
  top: calc(50% - 50px);
  left: calc(50% - 50%);
}
```

**<font color="#f40">7. 子元素转行内块元素（已知父容器高度）</font>**

```css
.wrap {
  /* 行高设为父容器高度 */
  line-height: 500px;
  text-align: center;
}

.box {
  display: inline-block;
  /* 设定行内元素垂直对齐方式为居中对齐 */
  vertical-align: middle;
}
```

## 三种隐藏元素区别

### display: none

- 元素不会存在渲染树中，不占据空间，无法触发事件，子节点设置其他`display`属性也不可见

### visibility: hidden

- 存在渲染树中，占据空间，无法触发事件（如点击），子孙属性修改为 `visible` 会可见

### opacity: 0

- 存在渲染树中，占据空间，可以触发事件（如点击），子孙元素修改 `opactiy` 不显示。

## 定位 Position

- `position: absolute` 的内联元素会变为`block`块级元素

## link & @important

`link` 是页面加载时候就加载的因为 `link` 是 `html` 标签，且 `link` 方式内的样式权重大

`@important` 是等到页面加载完才加载的