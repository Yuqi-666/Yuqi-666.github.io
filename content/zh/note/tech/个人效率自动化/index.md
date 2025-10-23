---
private: false
featured: false
title: "个人效率自动化"
authors:
  - admin
tags:
categories:
  - tech
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
![Drawing 2025-10-24 01.42.20.excalidraw](../../../../../static/_Excalidraw/Drawing%202025-10-24%2001.42.20.excalidraw.md)
### 2. 核心仓库：Yuqi-666.github.io

存放各种数据文件
![Drawing 2025-10-24 01.43.20.excalidraw](../../../../../static/_Excalidraw/Drawing%202025-10-24%2001.43.20.excalidraw.md)
### 3. 前端：
#### Obsidian 主页
#### 网站（桌面端、移动端）
网站用于展示博客笔记，建站方法参考[**使用hugo及hugo blox搭建个人博客**](/note/tech/personal-blog)
#### ios日历，提醒事项
#### 快捷指令

![Drawing 2025-10-24 01.46.44.excalidraw](../../../../../static/_Excalidraw/Drawing%202025-10-24%2001.46.44.excalidraw.md)




### 4. 同步系统：git+github

### 5. 后端

#### 任务管理系统

-  AI规划
{{< bilibili id="BV1opXjY2ELp" >}}

#### 任务同步系统

### 6. 复盘系统




## 全局图

![Drawing 2025-10-03 20.18.48.excalidraw](../../../../../static/_Excalidraw/Drawing%202025-10-03%2020.18.48.excalidraw.md)

