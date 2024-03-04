---
title: Nokogiri 简介
author: hypersport
date: 2023-02-02 23:02:02 +0800
toc: true
categories: [Ruby]
tags: [Nokogiri]
render_with_liquid: false
---

### 关于 *Nokogiri*

*Nokogiri*（鋸）使 *Ruby* 可以简单轻松地处理 *XML* 和 *HTML*。它提供了合理、易懂的 *API*，用于读写、修改和查询文档。它依赖于 *libxml2*、*libgumbo* 和 *xerces* 等原生解析器，处理速度快且符合标准。

### 指导原则

*Nokogiri* 努力遵循的指导原则：

- 默认情况下，将所有文档视为 **不受信任** 来确保安全

- 在底层解析器的基础上做到 **尽可能正确合理**，不会试图修复解析器之间的行为差异

### 功能概述

- 适用于 *XML*、*HTML4* 和 *HTML5* 的 *DOM* 解析器

- 适用于 *XML* 和 *HTML4* 的 *SAX* 解析器

- 适用于 *XML* 和 *HTML4* 的 *Push* 解析器

- 通过 *XPath 1.0* 进行文档搜索

- 通过 *CSS3* 选择器和一些类似 *jquery* 的扩展功能进行文档搜索

- *XML Schema* 验证

- *XSLT* 转换

- 用于 *XML* 和 *HTML* 文档的 "生成器" *DSL*

### 安装 *Nokogiri*

```bash
gem install nokogiri
```
