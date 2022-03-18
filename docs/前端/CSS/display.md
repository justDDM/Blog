# display

## flow-root

可以解决 BFC 问题。

## flex

### `flex-wrap`

1. `row`: 默认值，主轴方向自左向右
2. `row-reverse`: 主轴自右向左
3. `column`: 主轴自上往下
4. `column-reverse`: 主轴自下往上

### `flex-direction`

1. `nowrap`: 默认值，一行摆放
2. `wrap`: 换行放置
3. `wrap-reverse`: 倒着放多行，每一行是按顺序的
<img style="width: 50%; display: block; margin-top: 10px;" src="/前端/CSS/Flex-wrap-reverse.jpeg">

### `flex`

- `flex-grow`

  - 负值不生效，默认0。若主轴方向有剩余空间，按照`flex-grow`比例分配空间，拉伸宽度。

- `flex-shrink`

  - 负值无效，默认1。若容器元素不换行`flex-wrap: nowrap`，且元素宽度之和大于容器元素宽度则会压缩元素。

  - 每个元素缩小值 = ( (元素宽度 * flex-shrink) / 元素宽度和 ) * 超出宽度

- `flex-basic`

    - `width`未设置,只设置了`flex-basic` / `basic`大于`width`：此时设置的值为最小值

    - `width`、`flex-basic`都设置且 `width` > `flex-basic`：此时最小宽度为`flex-basic` 最大宽度为 `wdith`

    - 不换行的元素如英文(无空格隔开的长串)会将元素撑开至`width`设置的宽度，撑开的元素不会被压缩。

**<font color="#f40">取值</font>**

**单值语法**

  - 无单位数字：`flex: n` === `flex: n 1 0%` （作为`flex-grow`值）

  - 带有效单位数字：`flex: 10px` === `flex-shrink: 10px` (作为`flex-shrink`值)

  - `auto`: `flex: 1 1 auto` (能伸伸缩为auto)

  - `none`: `flex: 0 0 auto` (不伸不缩为none)

  - `initial`: `flex: 0 1 auto` (默认能缩不能伸)

**双值语法（第一个必须是无单位数字即`flex-grow`的取值，第二位分情况）**

  - 无单位数字：`flex: n m` === `flex: n m 0%`

  - 带有效单位数字：`flex: n 10px` === `flex: n 1 10px`

**三值语法**
  
  - 依次为 `flex-grow`、 `flex-shrink`、`flex-basic`

### 其他取值

- `justify-content`

- `justify-items`

- `align-content`: 默认为 stretch (若有多余空间，则平均给每一行元素下面加上间距)

- `align-items`

- `align-self`

- `order`

:::tip 优先级
`align-content` > `align-self` > `align-items`
:::


