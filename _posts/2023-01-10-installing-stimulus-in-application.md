---
title: 安装 Stimulus 
author: hypersport
date: 2023-01-10 23:01:10 +0800
categories: [前端]
tags: [JavaScript, Stimulus]
toc: true
render_with_liquid: false
---

在应用程序中安装 *Stimulus*，需要添加 [@hotwired/stimulus npm 包](https://www.npmjs.com/package/@hotwired/stimulus)。或者在 **<script type=\"module\">** 标签中导入 [stimul.js](https://unpkg.com/@hotwired/stimulus/dist/stimulus.js)。

### 使用 [Stimulus for Rails](https://github.com/hotwired/stimulus-rails)

如果将 [Stimulus for Rails](https://github.com/hotwired/stimulus-rails) 和 [import map](https://github.com/rails/importmap-rails) 一起使用，那么会自动从 `app/javascript/controllers` 加载集成进去。

将控制器文件命名为 `[identifier]_controller.js`，其中 **identifier** 对应 *HTML* 中每个控制器的 **data-controller** 标识符。

[Stimulus for Rails](https://github.com/hotwired/stimulus-rails) 通常使用下划线分隔文件名中的多个单词。控制器文件名中的每个下划线都会转换为其标识符中的短横线。

还可以使用子文件夹为控制器命名，控制器文件路径中的每个正斜杠在其标识符中都会变成两个短横线。

也可以在控制器文件名中的任何位置使用短横线而不使用下划线，*Stimulus* 都会进行相同的处理。

| 控制器文件名                   | 对应的标识符      |
|:------------------------------|:-----------------|
| clipboard_controller.js       | clipboard        |
| date_picker_controller.js     | date-picker      |
| users/list_item_controller.js | users\-\-list-item |
| local-time-controller.js      | local-time       |

### 使用 *Webpack Helpers*

如果使用 *Webpack*，则可以使用 [@hotwired/stimulus-webpack-helpers](https://www.npmjs.com/package/@hotwired/stimulus-webpack-helpers) 获得与 [Stimulus for Rails](https://github.com/hotwired/stimulus-rails) 相同的自动加载功能。

添加包后像这样使用：

```js
import { Application } from "@hotwired/stimulus"
import { definitionsFromContext } from "@hotwired/stimulus-webpack-helpers"

window.Stimulus = Application.start()
const context = require.context("./controllers", true, /\.js$/)
Stimulus.load(definitionsFromContext(context))
```

### 使用其他构建系统

*Stimulus* 也适用于其他构建系统，但不支持控制器自动加载，因此必须在应用程序实例中显式加载注册控制器文件：

```js
import { Application } from "@hotwired/stimulus"

import HelloController from "./controllers/hello_controller"
import ClipboardController from "./controllers/clipboard_controller"

window.Stimulus = Application.start()
Stimulus.register("hello", HelloController)
Stimulus.register("clipboard", ClipboardController)
```
{: file='src/application.js'}

如果将 *stimulus-rails* 与 *esbuild* 等构建器一起使用，则可以使用 `stimulus:manifest:update Rake` 任务和 `./bin/rails generate stimulus [controller]` 生成器保证控制器索引文件 `app/javascript/controllers/index.js` 自动更新。

### 不使用构建系统

如果不使用构建系统，可以在 **<script type=\"module\">** 标签中导入 [stimul.js](https://unpkg.com/@hotwired/stimulus/dist/stimulus.js)：

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <script type="module">
    import { Application, Controller } from "https://unpkg.com/@hotwired/stimulus/dist/stimulus.js"
    window.Stimulus = Application.start()

    Stimulus.register("hello", class extends Controller {
      static targets = [ "name" ]

      connect() {
      }
    })
  </script>
</head>
<body>
  <div data-controller="hello">
    <input data-hello-target="name" type="text">
    …
  </div>
</body>
</html>
```

### 覆盖属性默认值

如果 *Stimulus* 的 **data-\*** 属性与项目中的其他库冲突，可以在创建 *Stimulus* 应用程序时覆盖它们。

- data-controller

- data-action

- data-target

这些 *Stimulus* 核心属性可以被覆盖（参见：[schema.ts](https://github.com/hotwired/stimulus/blob/main/src/core/schema.ts)）：

```js
import { Application, defaultSchema } from "@hotwired/stimulus"

const customSchema = {
  ...defaultSchema,
  actionAttribute: 'data-stimulus-action'
}

window.Stimulus = Application.start(document.documentElement, customSchema);
```
{: file='src/application.js'}

### 错误处理

*Stimulus* 对应用程序代码的所有调用都包含在 *try ... catch* 块中。

如果应用程序代码抛出错误，*Stimulus* 将捕获该错误并将其记录到浏览器控制台，并且会包括额外的详细信息，例如控制器名称和事件或正在调用的生命周期函数。如果使用定义了 *window.onerror* 的错误跟踪系统，*Stimulus* 也会将错误传递给它。

可以通过定义 *Application#handleError* 覆盖 *Stimulus* 处理错误的方式：

```js
import { Application } from "@hotwired/stimulus"
window.Stimulus = Application.start()

Stimulus.handleError = (error, message, detail) => {
  console.warn(message, detail)
  ErrorTrackingSystem.captureException(error)
}
```
{: file='src/application.js'}

### 调试 *Stimulus*

如果已经将 *Stimulus* 应用程序指定为 *window.Stimulus*，那么可以使用 *Stimulus.debug = true* 从控制台打开调试模式。还可以在源代码中配置应用程序实例时设置此标志。

### 浏览器支持

*Stimulus* 支持所有主流的、还在维护更新的桌面和移动浏览器。*Stimulus 3+* 不支持 *Internet Explorer 11*（可以使用 *Stimulus 2* 与 *@stimulus/polyfills*）。
