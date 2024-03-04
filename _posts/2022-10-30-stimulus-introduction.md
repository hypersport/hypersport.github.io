---
title: Stimulus 简介
author: hypersport
date: 2022-10-30 10:30:30 +0800
toc: false
categories: [前端]
tags: [JavaScript, Stimulus]
render_with_liquid: false
---

### 关于 *Stimulus*

*Stimulus* 是一个有着谦虚目标的 *JavaScript* 框架，与其他前端框架不同，*Stimulus* 旨在通过使用简单的注释将 *JavaScript* 对象连接到页面上的元素来增强静态或服务端渲染的已有的 *HTML*。

这些 *JavaScript* 对象称为 *controller*，*Stimulus* 会持续监视页面，等待 *HTML* 中 **data-controller** 属性的出现。对于每个属性，*Stimulus* 都会查看该属性的值以找到相应的 *controller* 类，然后创建该类的新实例，并将其连接到元素。

你可以这样想：就像 *class* 属性是连接 *HTML* 和 *CSS* 的桥梁一样，*Stimulus* 的 **data-controller** 属性是连接 *HTML* 和 *JavaScript* 的桥梁。

除了 *controller* 以外，*Stimulus* 还有三个主要概念：

  - *action*：*Stimulus* 使用 **data-action** 属性将控制器方法连接到 *DOM* 事件

  - *target*：定位控制器中的元素

  - *value*：读取、写入和观察控制器元素上的数据属性

*Stimulus* 对数据属性的使用有助于将内容与行为分开，就像 *CSS* 将内容与表现形式分开一样。 此外，*Stimulus* 的约定也鼓励开发人员按名称对相关代码进行分组。

反过来，*Stimulus* 可以帮助开发人员构建小型、可重用的 *controller*，为开发人员提供足够的结构，以防止代码变成 "JavaScript soup"。

### 关于这份手册

本手册将通过演示如何编写多个功能齐全的 *controller* 来了解 *Stimulus* 的核心概念。每一章都建立在前一章的基础上，从开始到结束，将学习到以下内容：

  - 在文本字段中打印对姓名的问候语

  - 单击按钮时，将文本从文本字段中复制到系统剪贴板

  - 浏览包含多张幻灯片的幻灯片放映

  - 自动从服务器获取 *HTML* 到页面上的元素中

  - 在自己的应用程序中设置 *Stimulus*

完成此处的练习后，查阅 [参考文档](https://stimulus.hotwired.dev/reference/controllers) 更好的理解有关 *Stimulus API* 的技术细节。
