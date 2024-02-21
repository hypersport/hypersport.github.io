---
title: Stimulus - 使用外部资源 
author: hypersport
date: 2023-01-01 23:01:01 +0800
categories: [前端]
tags: [JavaScript, Stimulus]
toc: true
render_with_liquid: false
---

上一章节介绍了如何使用值加载和保存控制器的内部状态。但是有时候控制器需要跟踪外部资源的状态，这里的外部资源是指任何不在 *DOM* 或 *Stimulus* 中的内容。例如，发出 *HTTP* 请求并在请求状态发生变化时做出响应，或者启动一个计时器，并在控制器断开连接时停止它。

### 异步加载 *HTML*

*Basecamp* 初始页面通过加载和插入远程 *HTML* 片段异步填充的方式保证页面加载速度，并使视图不受用户特定内容的影响，以便更有效率地进行缓存。

首先构建一个通用内容加载器控制器，该控制器使用从服务器获取的 *HTML* 填充其元素。然后使用它加载未读消息列表，就像在电子邮件收件箱中看到的那样。

创建 `public/index.html` 文件：

```html
<div data-controller="content-loader"
    data-content-loader-url-value="/messages.html">
</div>
```
{: file='public/index.html'}

然后创建消息列表的 `public/messages.html` 文件：

```html
<ol>
  <li>New Message: Stimulus Launch Party</li>
  <li>Overdue: Finish Stimulus 1.0</li>
</ol>
```
{: file='public/messages.html'}

真实的应用程序中，在服务器上动态生成此 *HTML*，但出于演示目的，这里仅使用静态文件。

实现控制器：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static values = { url: String }

  connect() {
    this.load()
  }

  load() {
    fetch(this.urlValue)
      .then(response => response.text())
      .then(html => this.element.innerHTML = html)
  }
}
```
{: file='src/controllers/content_loader_controller.js'}

当控制器连接时，向元素的 **data-content-loader-url-value** 属性中指定的 *URL* 发起 [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) 请求。然后将返回的 *HTML* 分配给元素的 *innerHTML* 属性来加载页面。

在浏览器的开发者控制台中打开网络选项卡并重新加载页面，将看到一个代表初始页面加载的请求，然后是控制器对 `messages.html` 的请求。

### 使用定时器自动刷新页面

改进控制器，使其可以定期刷新收件箱来保持最新状态。

使用 **data-content-loader-refresh-interval-value** 属性指定刷新频率（以毫秒为单位）：

```html
<div data-controller="content-loader"
    data-content-loader-url-value="/messages.html"
    data-content-loader-refresh-interval-value="5000">
</div>
```

现在更新控制器来检查时间间隔，如果存在，则启动刷新计时器。

向控制器添加静态值定义，并定义新方法 *startRefreshing()*：

```js
export default class extends Controller {
  static values = { url: String, refreshInterval: Number }

  startRefreshing() {
    setInterval(() => {
      this.load()
    }, this.refreshIntervalValue)
  }

  // …
}
```

然后更新 *connect()* 方法，当存在间隔值时调用 *startRefreshing()*：

```js
  connect() {
    this.load()

    if (this.hasRefreshIntervalValue) {
      this.startRefreshing()
    }
  }
```

重新加载页面，在开发者控制台中会看到每五秒会有一次新请求。然后修改 `public/messages.html` 并等待它出现在收件箱中。

### 释放跟踪资源

当控制器连接时计时器会自动启动，但永远不会停止。这意味着如果控制器的元素消失，那么仍将继续在后台发出 *HTTP* 请求。

可以通过修改 *startRefreshing()* 方法，保留对计时器的引用来解决这个问题：

```js
  startRefreshing() {
    this.refreshTimer = setInterval(() => {
      this.load()
    }, this.refreshIntervalValue)
  }
```

然后添加对应的 *stopRefreshing()* 方法取消定时器：

```js
  stopRefreshing() {
    if (this.refreshTimer) {
      clearInterval(this.refreshTimer)
    }
  }
```

最后，为了让 *Stimulus* 在控制器断开连接时取消计时器，需要添加一个 *disconnect()* 方法：

```js
  disconnect() {
    this.stopRefreshing()
  }
```

现在可以确保内容加载器控制器仅在连接到 *DOM* 时才会发出请求。

最终的控制器类：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static values = { url: String, refreshInterval: Number }

  connect() {
    this.load()

    if (this.hasRefreshIntervalValue) {
      this.startRefreshing()
    }
  }

  disconnect() {
    this.stopRefreshing()
  }

  load() {
    fetch(this.urlValue)
      .then(response => response.text())
      .then(html => this.element.innerHTML = html)
  }

  startRefreshing() {
    this.refreshTimer = setInterval(() => {
      this.load()
    }, this.refreshIntervalValue)
  }

  stopRefreshing() {
    if (this.refreshTimer) {
      clearInterval(this.refreshTimer)
    }
  }
}
```
{: file='src/controllers/content_loader_controller.js'}

### 使用动作参数

如果想让加载器与多个不同的源一起工作，可以使用动作参数来实现：

```html
<div data-controller="content-loader">
  <a href="#" data-content-loader-url-param="/messages.html" data-action="content-loader#load">Messages</a>
  <a href="#" data-content-loader-url-param="/comments.html" data-action="content-loader#load">Comments</a>
</div>
```

然后通过 *load* 动作使用这些参数：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  load({ params }) {
    fetch(params.url)
      .then(response => response.text())
      .then(html => this.element.innerHTML = html)
  }
}
```

甚至还可以拆分参数来获取 *URL*：

```js
  load({ params: { url } }) {
    fetch(url)
      .then(response => response.text())
      .then(html => this.element.innerHTML = html)
  }
```
