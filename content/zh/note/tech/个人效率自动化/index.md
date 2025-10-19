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

### 编辑器：Obsidian

Obsidian编辑器操控核心仓库（数据库），进行数据的增删改查，依托于Obsidian的插件功能，进行丰富的编辑操作和视图操作

### 核心仓库：Yuqi-666.github.io

存放各种数据文件

### 前端：网站（多端显示），ios日历

网站用于展示博客笔记，建站方法参考[**使用hugo及hugo blox搭建个人博客**](/note/tech/personal-blog)
### 同步系统：git+github

### 后端

#### 任务管理系统

-  AI规划
{{< bilibili id="BV1opXjY2ELp" >}}

#### 任务同步系统

