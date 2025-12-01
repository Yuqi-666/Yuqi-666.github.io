---
private: false
featured: false
title: css布局
authors:
  - admin
tags:
  - css
  - 前端
categories:
  - tech
summary:
---
{{< toc mobile_only=true is_open=true >}}

# CSS 布局核心：position 属性详解
`position` 是 CSS 中控制元素定位的核心属性，决定了元素在页面中的布局方式（如何脱离正常文档流、如何相对于参考点定位）。它有 5 个核心取值：`static`、`relative`、`fixed`、`sticky`、`absolute`，下面逐一拆解其特性、使用场景和注意事项。


## 一、static（默认定位）
### 核心特性
- 元素的**默认定位方式**，未设置 `position` 时默认生效。
- 元素遵循**正常文档流**（自上而下、自左向右排列），不受 `top`、`right`、`bottom`、`left` 和 `z-index` 属性影响。
- 不会脱离文档流，元素的位置由其在 HTML 中的结构顺序和父元素布局决定。

### 代码示例
```css
.box {
  position: static;
  top: 20px; /* 无效！static 不识别定位偏移属性 */
  left: 30px; /* 无效！ */
}
```

### 适用场景
- 大多数普通元素的默认布局（无需特殊定位时），比如文本段落、列表、普通 div 等。


## 二、relative（相对定位）
### 核心特性
- 元素**不脱离正常文档流**，其原始位置会被保留（页面布局不会因定位而塌陷）。
- 定位参考点：**元素自身的原始位置**（正常文档流中的位置）。
- 可通过 `top`、`right`、`bottom`、`left` 设置偏移量（比如 `top: 10px` 表示相对于原始位置向下移 10px）。
- 支持 `z-index` 属性（可调整层级，默认 `auto`，高于 `static` 元素）。

### 代码示例
```css
.parent {
  width: 300px;
  height: 300px;
  background: #f0f0f0;
}
.box {
  position: relative;
  top: 20px; /* 相对于自身原始位置向下移 20px */
  left: 30px; /* 相对于自身原始位置向右移 30px */
  background: #42b983;
  width: 100px;
  height: 100px;
}
```

### 关键注意
- 元素偏移后，原始位置会“占位”（比如上述示例中，其他元素不会占据 `.box` 的原始位置）。
- 常作为 `absolute` 定位元素的**参考父容器**（需配合 `absolute` 使用）。

### 适用场景
- 微调元素位置（比如图标相对于文字偏移）。
- 作为绝对定位元素的“定位容器”。


## 三、absolute（绝对定位）
### 核心特性
- 元素**完全脱离正常文档流**（原始位置不再占位，其他元素会忽略它的存在）。
- 定位参考点：**最近的已定位祖先元素**（`position` 为 `relative`/`absolute`/`fixed`/`sticky` 的父/祖先元素）；若没有，则参考**根元素（`<html>`）** 。
- 可通过 `top`、`right`、`bottom`、`left` 精确控制位置（比如 `top: 0; right: 0` 表示贴紧参考容器的右上角）。
- 支持 `z-index` 属性，层级默认高于 `static` 和 `relative` 元素。
- 元素宽度默认“收缩包裹”内容（而非占满父容器）。

### 代码示例（参考父容器为 relative）
```css
.parent {
  position: relative; /* 作为 absolute 元素的参考容器 */
  width: 300px;
  height: 300px;
  background: #f0f0f0;
}
.box {
  position: absolute;
  top: 50%; /* 相对于父容器顶部向下 50% */
  left: 50%; /* 相对于父容器左侧向右 50% */
  transform: translate(-50%, -50%); /* 居中（抵消自身宽高的 50%） */
  width: 100px;
  height: 100px;
  background: #e53e3e;
}
```

### 关键注意
- 必须确保有“已定位的祖先元素”，否则会相对于页面（`<html>`）定位，滚动时位置会跟随页面变化。
- 脱离文档流后，可能导致父元素“高度塌陷”（需手动处理父元素高度，或用 `overflow: auto` 等）。

### 适用场景
- 弹窗、下拉菜单、tooltip（提示框）。
- 元素精确定位（比如图标在按钮右上角）。
- 组合布局（比如图片上叠加文字）。


## 四、fixed（固定定位）
### 核心特性
- 元素**完全脱离正常文档流**（原始位置不占位）。
- 定位参考点：**浏览器视口（viewport）** （可视区域，不随页面滚动而变化）。
- 可通过 `top`、`right`、`bottom`、`left` 固定位置（比如 `bottom: 0; right: 0` 表示贴紧浏览器右下角）。
- 支持 `z-index` 属性，层级默认高于 `absolute` 和 `relative` 元素。
- 页面滚动时，元素位置保持不变（始终悬浮在视口固定位置）。

### 代码示例（固定底部导航）
```css
.footer {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0; /* 左右贴紧视口，宽度自适应 */
  height: 60px;
  background: #2d3748;
  color: white;
  text-align: center;
  line-height: 60px;
}
```

### 关键注意
- 不受父元素布局影响（即使父元素有定位，仍以视口为参考）。
- 可能被 `transform`、`perspective` 或 `filter` 属性影响（若父元素有这些属性，参考点会变成父元素的可视区域）。

### 适用场景
- 固定导航栏（顶部/底部）。
- 悬浮按钮（比如“回到顶部”“客服”）。
- 弹窗遮罩层（全屏固定）。


## 五、sticky（粘性定位）
### 核心特性
- 结合了 `relative` 和 `fixed` 的特性，堪称“滚动时的条件固定”。
- 元素**未滚动到阈值时**：表现为 `relative`（遵循正常文档流，原始位置占位）。
- 元素**滚动到阈值时**：表现为 `fixed`（固定在视口指定位置，不再跟随滚动）。
- 定位参考点：**最近的滚动祖先容器**（父元素需有滚动条，或页面滚动）。
- 必须设置 `top`/`right`/`bottom`/`left` 中的至少一个（否则无法触发粘性效果）。

### 代码示例（粘性导航栏）
```css
.nav {
  position: sticky;
  top: 0; /* 滚动到顶部距离为 0 时，触发固定 */
  background: #4299e1;
  color: white;
  padding: 10px;
  z-index: 100; /* 避免被其他元素覆盖 */
}
.content {
  height: 2000px; /* 模拟长内容，触发滚动 */
}
```

### 关键注意
- 父元素不能有 `overflow: hidden`/`auto`/`scroll`（会破坏粘性定位的滚动检测）。
- 粘性效果仅在“滚动祖先容器”的可视范围内生效（比如父元素高度有限，滚动超出父元素后，粘性元素会跟随父元素消失）。

### 适用场景
- 滚动时吸顶的导航栏、分类标题。
- 列表滚动时固定的表头。


## 六、5 种定位的核心对比表
| 定位方式 | 是否脱离文档流 | 参考点 | 偏移属性生效 | z-index 生效 | 关键特性 |
|----------|----------------|--------|--------------|--------------|----------|
| static   | 否（默认）     | 无（正常流） | 否 | 否 | 普通元素默认布局 |
| relative | 否 | 自身原始位置 | 是 | 是 | 微调位置、作为绝对定位容器 |
| absolute | 是 | 最近已定位祖先 / html | 是 | 是 | 精确定位、脱离流 |
| fixed    | 是 | 浏览器视口 | 是 | 是 | 固定在视口、不随滚动 |
| sticky   | 条件脱离（滚动到阈值） | 最近滚动祖先 | 是 | 是 | 滚动吸顶/吸底 |


## 七、常见使用技巧
1. **absolute 居中**：`top: 50%; left: 50%; transform: translate(-50%, -50%)`（无需知道元素宽高）。
2. **fixed 避免遮挡内容**：底部固定元素需给页面底部加 `padding-bottom`（等于固定元素高度），防止内容被遮挡。
3. **z-index 层级管理**：定位元素默认层级按“出现顺序”叠加，可通过 `z-index: 数值` 调整（数值越大越靠上，注意父元素层级会限制子元素）。
4. **避免滥用定位**：脱离文档流的定位（absolute/fixed）可能导致布局混乱，优先使用 Flex/Grid 布局，定位仅用于特殊场景（弹窗、悬浮元素等）。

通过理解 `position` 的 5 种取值，可灵活控制元素在页面中的位置，结合 Flex/Grid 等布局方式，能实现绝大多数网页布局需求。





# CSS Flex 布局详解
Flex（Flexible Box，弹性盒布局）是 CSS3 引入的**一维布局模型**，专门用于解决元素在容器中的对齐、分布和自适应问题，替代传统的浮动（float）、定位（position）等布局方案，具有简洁、灵活、响应式友好的特点。


## 一、核心概念
Flex 布局的核心是「容器」和「项目」，以及围绕二者的关键术语：

| 术语                   | 说明                                                       |
| -------------------- | -------------------------------------------------------- |
| 弹性容器（Flex Container） | 父元素通过 `display: flex` 或 `display: inline-flex` 声明，成为弹性容器 |
| 弹性项目（Flex Item）      | 弹性容器的**直接子元素**（不包含孙子辈），自动成为弹性项目                          |
| 主轴（Main Axis）        | 项目排列的主方向（默认水平向右，可通过 `flex-direction` 修改）                 |
| 交叉轴（Cross Axis）      | 与主轴垂直的方向（默认垂直向下，随主轴方向变化）                                 |
| 主轴起点/终点              | 项目在主轴上的排列起点（如默认左）和终点（如默认右）                               |
| 交叉轴起点/终点             | 项目在交叉轴上的排列起点（如默认上）和终点（如默认下）                              |


## 二、基础使用：创建弹性容器
只需给父元素添加 `display: flex` 或 `display: inline-flex`，即可激活 Flex 布局：
```css
.container {
  display: flex; /* 块级弹性容器（独占一行） */
  /* 或 display: inline-flex; 行内级弹性容器（随内容宽度） */
}
```
**注意**：
- 容器激活后，子元素（项目）默认横向排列，且会「弹性伸缩」（默认不收缩、不放大，按自身宽高排列）；
- 项目的 `float`、`clear`、`vertical-align` 属性会失效（不影响容器外元素）。


## 三、容器属性（控制项目整体布局）
所有属性作用于**弹性容器**，用于控制项目的排列方向、对齐方式、换行规则等。

### 1. flex-direction：控制主轴方向（项目排列方向）
默认值：`row`（水平向右）
```css
.container {
  flex-direction: row;    /* 主轴水平，项目从左到右（默认） */
  flex-direction: row-reverse; /* 主轴水平，项目从右到左 */
  flex-direction: column; /* 主轴垂直，项目从上到下 */
  flex-direction: column-reverse; /* 主轴垂直，项目从下到上 */
}
```

### 2. flex-wrap：控制项目是否换行
默认值：`nowrap`（不换行，项目会被压缩）
```css
.container {
  flex-wrap: nowrap;  /* 不换行，项目溢出时压缩（默认） */
  flex-wrap: wrap;    /* 换行，溢出项目在下一行排列（从上到下） */
  flex-wrap: wrap-reverse; /* 反向换行，溢出项目在上一行排列（从下到上） */
}
```
**常用组合**：`flex-flow: <flex-direction> <flex-wrap>`（简写属性）
```css
.container {
  flex-flow: row wrap; /* 水平排列 + 自动换行（最常用） */
}
```

### 3. justify-content：主轴对齐（项目在主轴上的分布）
默认值：`flex-start`（靠主轴起点对齐）
```css
.container {
  justify-content: flex-start;    /* 靠主轴起点（默认左/上） */
  justify-content: flex-end;      /* 靠主轴终点（右/下） */
  justify-content: center;        /* 主轴居中（水平/垂直居中） */
  justify-content: space-between; /* 两端对齐，项目间距相等（无首尾间距） */
  justify-content: space-around;  /* 项目两侧间距相等（首尾间距是中间的1/2） */
  justify-content: space-evenly;  /* 所有间距（包括首尾）相等 */
}
```
**示例**：水平居中 + 均匀分布
```css
.container {
  display: flex;
  justify-content: space-evenly; /* 主轴均匀分布 */
}
```

### 4. align-items：交叉轴对齐（项目在交叉轴上的对齐）
默认值：`stretch`（项目未设置高度时，拉伸填满容器交叉轴）
```css
.container {
  align-items: stretch;    /* 拉伸填满交叉轴（默认） */
  align-items: flex-start; /* 靠交叉轴起点（上/左） */
  align-items: flex-end;   /* 靠交叉轴终点（下/右） */
  align-items: center;     /* 交叉轴居中（垂直/水平居中） */
  align-items: baseline;   /* 按项目内文字基线对齐（常用于文字对齐） */
}
```
**经典场景**：水平 + 垂直居中（Flex 最常用用法）
```css
.container {
  display: flex;
  justify-content: center; /* 主轴（水平）居中 */
  align-items: center;     /* 交叉轴（垂直）居中 */
  height: 300px; /* 必须给容器设置高度，否则交叉轴无拉伸空间 */
}
```

### 5. align-content：多行车对齐（仅当项目换行时生效）
控制**多行项目**在交叉轴上的整体分布（单行项目时无效），默认值：`stretch`
```css
.container {
  align-content: stretch;    /* 拉伸填满交叉轴（默认） */
  align-content: flex-start; /* 多行靠交叉轴起点 */
  align-content: flex-end;   /* 多行靠交叉轴终点 */
  align-content: center;     /* 多行交叉轴居中 */
  align-content: space-between; /* 多行两端对齐，间距相等 */
  align-content: space-around;  /* 多行两侧间距相等 */
}
```
**注意**：需配合 `flex-wrap: wrap` 使用，且容器需有交叉轴方向的高度（如垂直方向容器需设置 `height`）。


## 四、项目属性（控制单个项目布局）
所有属性作用于**弹性项目**，用于调整单个项目的大小、对齐方式等，优先级高于容器属性。

### 1. flex：项目伸缩比例（核心属性，简写）
`flex` 是 `flex-grow`、`flex-shrink`、`flex-basis` 的简写，默认值：`0 1 auto`

| 子属性         | 说明                                |
| ----------- | --------------------------------- |
| flex-grow   | 项目的「放大比例」，默认 0（不放大，即使容器有剩余空间）     |
| flex-shrink | 项目的「缩小比例」，默认 1（容器空间不足时，项目会缩小）     |
| flex-basis  | 项目在主轴上的「初始宽度/高度」，默认 auto（按项目自身尺寸） |


**常用取值**：
```css
.item {
  flex: 1; /* 等价于 flex: 1 1 0% → 项目平均分配剩余空间（最常用） */
  flex: 2; /* 放大比例为 2，占比是 flex:1 项目的 2 倍 */
  flex: 0 0 200px; /* 不放大、不缩小，固定宽度 200px（固定尺寸） */
  flex: auto; /* 等价于 flex: 1 1 auto → 先按自身尺寸，再分配剩余空间 */
  flex: none; /* 等价于 flex: 0 0 auto → 不放大、不缩小，固定自身尺寸 */
}
```
**示例**：三列布局（左侧固定 200px，中间自适应，右侧固定 150px）
```css
.container {
  display: flex;
}
.left { flex: 0 0 200px; } /* 固定宽度，不伸缩 */
.middle { flex: 1; } /* 自适应，占满剩余空间 */
.right { flex: 0 0 150px; } /* 固定宽度 */
```

### 2. align-self：单个项目交叉轴对齐（覆盖容器 align-items）
用于单独调整某个项目的交叉轴对齐方式，默认值：`auto`（继承容器 `align-items`）
```css
.item {
  align-self: auto;      /* 继承容器 align-items（默认） */
  align-self: flex-start; /* 单个项目靠交叉轴起点 */
  align-self: flex-end;   /* 单个项目靠交叉轴终点 */
  align-self: center;     /* 单个项目交叉轴居中 */
  align-self: stretch;    /* 单个项目拉伸填满交叉轴 */
  align-self: baseline;   /* 单个项目按文字基线对齐 */
}
```
**示例**：大部分项目居中，单个项目靠底
```css
.container {
  display: flex;
  align-items: center; /* 所有项目默认垂直居中 */
  height: 300px;
}
.special-item {
  align-self: flex-end; /* 单个项目靠底 */
}
```

### 3. order：项目排列顺序
默认值：`0`，通过整数控制项目的排列优先级（数值越小，越靠前）
```css
.item1 { order: 2; } /* 排第 3 位 */
.item2 { order: 1; } /* 排第 2 位 */
.item3 { order: 0; } /* 排第 1 位（默认） */
```
**注意**：仅改变视觉顺序，不改变 DOM 结构（无障碍访问需注意）。

### 4. flex-basis：项目初始尺寸
定义项目在主轴上的初始宽度/高度，优先级高于项目自身的 `width`/`height`（主轴为水平时控制宽度，垂直时控制高度）
```css
.item {
  flex-basis: 100px; /* 初始宽度 100px（主轴水平时） */
  flex-basis: auto; /* 按项目自身尺寸（默认） */
  flex-basis: 50%; /* 初始宽度为容器的 50% */
}
```


## 五、常见应用场景
### 1. 水平居中 + 垂直居中
```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px; /* 必须设置父容器高度 */
}
.child {
  width: 100px;
  height: 100px;
}
```

### 2. 两列布局（左侧固定，右侧自适应）
```css
.container {
  display: flex;
}
.left {
  flex: 0 0 250px; /* 固定宽度 250px，不伸缩 */
  background: #f5f5f5;
}
.right {
  flex: 1; /* 自适应剩余空间 */
  background: #eee;
}
```

### 3. 均匀分布的导航栏
```css
.nav {
  display: flex;
  justify-content: space-around; /* 均匀分布 */
  list-style: none;
  padding: 0;
}
.nav li {
  padding: 10px 0;
}
```

### 4. 响应式换行布局（适配移动端）
```css
.card-container {
  display: flex;
  flex-wrap: wrap; /* 自动换行 */
  gap: 16px; /* 项目间距（替代 margin，更简洁） */
}
.card {
  flex: 1 1 250px; /* 最小宽度 250px，剩余空间均匀分配 */
  height: 200px;
  background: #f0f0f0;
}
```
- `gap`：控制项目之间的间距（主轴和交叉轴同时生效），无需手动计算 `margin`，是 Flex 布局的便捷属性。


## 六、Flex 布局特点与兼容性
### 特点
- 一维布局：仅关注「行」或「列」一个方向的布局（二维布局需用 Grid）；
- 自适应强：项目自动伸缩，适配不同屏幕尺寸；
- 简洁高效：无需浮动、定位，少量代码实现复杂布局；
- 对齐方便：原生支持水平、垂直居中，间距分配等常见需求。

### 兼容性
- 支持所有现代浏览器（Chrome、Firefox、Safari、Edge）；
- 不支持 IE9 及以下（如需兼容，需降级为浮动布局）。


## 七、总结
Flex 布局的核心是「容器 + 项目」的组合，通过容器属性控制整体布局，项目属性调整单个元素，关键记住：
1. 激活容器：`display: flex`；
2. 主轴对齐：`justify-content`；
3. 交叉轴对齐：`align-items`（容器）/ `align-self`（项目）；
4. 项目伸缩：`flex`（最常用 `flex:1` 实现自适应）。

Flex 是前端布局的首选方案，尤其适合导航栏、卡片布局、居中对齐、响应式布局等场景，掌握后可大幅提升布局效率。