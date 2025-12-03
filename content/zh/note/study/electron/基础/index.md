---
private: false
title: electron基础
authors:
  - admin
tags:
  - electron
  - 全栈开发
categories:
  - study
summary: electron基础
---
{{< toc mobile_only=true is_open=true >}}

![](assets/Pasted%20image%2020251203160034.png)


## electron生命周期

1. ready（app）: app 初始化完成
2. dom-ready（window）: 一个窗口中的文档加载完成
3. did-finsh-load（window）: 导航完成时触发
4. ready-to-show（window）:窗口加载完毕可以显示
5. close（window）: 在窗口要关闭的时候触发。
6. closed（window）: 当窗口关闭时触发，此时应删除窗口引用
7. window-all-closed（app）: 所有窗口都被关闭时触发
8. before-quit（app）: 在程序关闭窗口前发信号
9. will-quit（app）: 当所有窗口被关闭后触发，同时应用程序将退出
10. quit（app）: 在应用程序退出时发出



## 窗口
### 加载后显示
```js
show: false, // 默认情况下创建一个窗口对象之后就会显示,设置为false 就不会显示了
```


### 尺寸
```js
let mainWin = new BrowserWindow({
x: 100,
y: 100, // 设置窗口显示的位置,相对于当前屏幕的左上角

width: 800,
height: 400,
maxHeight: 600,
maxWidth: 1000,
minHeight: 200,
minWidth: 300, // 可以通过 min max 来设置当前应用窗口的最大和最小尺寸
resizable: false // 是否允许缩放应用的窗口大小




})
```

### 无边框
```js
frame: false,//默认为true，false设置为无边框
```
![](assets/Pasted%20image%2020251203185224.png)

### 自定义标题栏
```js
titleBarStyle: 'hidden'//去除默认标题栏
titleBarOverlay: true//添加回窗口控件
```
通过网页自定义标题栏
```html
<div class="titlebar">Cool titlebar</div>
```

```css
body {
  margin: 0;
}
.titlebar {
  height: 30px;
  background: blue;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  app-region: drag;
}
```
样式`app-region: drag;`可以定义哪些区域可以用来拖拽窗口