---
private: false
featured: false
title: 个人效率自动化
authors:
  - admin
tags:
categories:
  - tech
summary:
---
{{< toc mobile_only=true is_open=true >}}

## 前言

我希望构建一个自动化流程，接管我的生活安排规划。

让我像玩游戏那样专注于设立目标，以及完成给定的任务，而无需考虑对目标达成的具体任务规划和安排，在不知不觉中完成成长，达成目标等。

同时需要有高可达性的可视化前端，让我方便的查看任务，设立目标，查看完成了哪些任务，距离目标有多远，以及周期性复盘。

让我摆脱没有规划，没有清晰自我认知的状态。

实现层面：

- 技术方面：需要有数据库用来存放个人管理相关数据，后端用来执行各种程序脚本，处理请求，需要嵌入AI，高可达性前端，使我仅通过一步，就能从我身边的终端访问，操作，视图。
- 理论方面：使用SOP，OKR，GTD等效率理论设计工作流
{{< bilibili id="BV1enZCYhERs" >}}
## 自动化流程架构

### 1. 编辑器：Obsidian

Obsidian编辑器操控核心仓库（数据库），进行数据的增删改查，依托于Obsidian的插件功能，进行丰富的编辑操作和视图操作
![Drawing 2025-10-24 01.42.20.excalidraw](assets/Drawing%202025-10-24%2001.42.20.excalidraw.md)
### 2. 核心仓库：Yuqi-666.github.io

存放各种数据文件
![Drawing 2025-10-24 01.43.20.excalidraw](assets/Drawing%202025-10-24%2001.43.20.excalidraw.md)
![Drawing 2025-10-25 16.55.20.excalidraw](assets/Drawing%202025-10-25%2016.55.20.excalidraw.md)
### 3. 前端：
#### Obsidian 主页

主页使用白板，白板上编排各个组件，组件的内容是_HOME文件夹下的一个个md文件，这些文件各自承担一个组件内容，在白板中以卡片预览笔记的形式展示

使用插件：
- echarts 绘制图表
- dataviewjs  读取json数据，处理数据


#### 网站（桌面端、移动端）
网站用于展示博客笔记，建站方法参考[**使用hugo及hugo blox搭建个人博客**](/note/tech/使用hugo及hugo-blox搭建个人博客网站)
#### ios日历，提醒事项
#### 快捷指令

![Drawing 2025-10-24 01.46.44.excalidraw](assets/Drawing%202025-10-24%2001.46.44.excalidraw.md)




### 4. 同步系统：git+github

### 5. 后端

#### AI助理

| 输入              | 输出  |
| --------------- | --- |
| 我要备考四级          |     |
| 我下午四点在图书馆研讨室有个会 |     |
| 提醒我把今天的稍后观看视频看了 |     |
| [一张截图]          |     |
| 帮我看看这周末有没有空闲时间  |     |
|                 |     |

### 6. 复盘系统




## 全局图
![](assets/Drawing%202025-10-03%2020.18.48.excalidraw.svg)


