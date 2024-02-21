---
title: Hello, Stimulus
author: hypersport
date: 2022-11-11 11:11:11 +0800
categories: [前端]
tags: [JavaScript, Stimulus]
render_with_liquid: false
---

了解 *Stimulus* 如何工作的最佳方法是构建一个简单的控制器，本章节将展示如何操作。

### 准备工作

为了练习，需要运行 [stimulus-starter](https://github.com/hotwired/stimulus-starter) 项目的副本，这是一个为探索 *Stimulus* 进行了预配置的空白项目。

推荐使用 [Glitch](https://glitch.com/edit/#!/import/git?url=https://github.com/hotwired/stimulus-starter.git)，这样可以完全在浏览器中运行而不需要安装任何东西。

如果更喜欢舒服的使用自己的编辑器，需要克隆并配置 *stimulus-starter*：

```bash
git clone https://github.com/hotwired/stimulus-starter.git
cd stimulus-starter
yarn install
yarn start
```

然后在浏览器访问 [http://localhost:9000/](http://localhost:9000/)。

  > 注意：*stimulus-starter* 使用 [Yarn](https://yarnpkg.com/) 包管理器进行依赖管理，因此要确保首先安装它。

### 一切从 *HTML* 开始

从使用文本字段和按钮的简单练习开始。当单击该按钮时，将文本字段中的值显示在控制台。

每个 *Stimulus* 项目都从 HTML 开始。 打开 `public/index.html` 在 *body* 标签中添加一下内容：

```html
<div>
  <input type="text">
  <button>Greet</button>
</div>
```

在浏览器中重新加载页面，应该可以看到文本字段和按钮。

### 控制器让 *HTML* 变的生动

*Stimulus* 的核心目的是自动将 *DOM* 元素连接到 *JavaScript* 对象。这些对象称为控制器。

通过扩展框架的内置 *Controller* 类来创建第一个控制器。在 `src/controllers/` 目录中创建一个名为 `hello_controller.js` 的新文件。并添加以下代码：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
}
```
{: file='src/controllers/hello_controller.js'}

### 用标识符将控制器与 *DOM* 链接

接下来，需要告诉 *Stimulus* 该控制器如何连接到我们的 *HTML*。这个通过在 *div* 标签的 **data-controller** 属性中放置一个标识符来实现：

```html
<div data-controller="hello">
  <input type="text">
  <button>Greet</button>
</div>
```

标识符充当l了元素和控制器之间的链接，在本例中，*hello* 标识符告诉 *Stimulus* 在 `hello_controller.js` 中创建控制器类的实例。可以在 [安装指南](https://pythonista.cn/posts/installing-stimulus-in-application) 中了解自动控制器加载的工作原理。

### 测试控制器是否工作

在浏览器中重新加载页面，会发现没有任何变化。那么如何知道控制器是否工作呢？

一种方法是在 *connect()* 方法中放置一条日志语句，每次控制器连接到 *DOM* 时 *Stimulus* 都会调用该语句。

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  connect() {
    console.log("Hello, Stimulus!", this.element)
  }
}
```
{: file='src/controllers/hello_controller.js'}

再次重新加载页面并打开开发者控制台，会看到 *Hello, Stimulus!*，然后是 *div* 标签。

### *Actions* 响应 *DOM* 事件

现在更改代码，在单击 *"Greet"* 按钮时显示日志消息.

首先将 *connect()* 重命名为 *greet()*：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  greet() {
    console.log("Hello, Stimulus!", this.element)
  }
}
```
{: file='src/controllers/hello_controller.js'}

我们希望在按钮的 *click* 事件被触发时调用 *greet()* 方法。在 *Stimulus* 中，处理事件的控制器方法被称为 *action* 方法。

要将 *action* 方法连接到按钮的 *click* 事件，在 `public/index.html` 中向按钮添加 **data-action** 属性：

```html
<div data-controller="hello">
  <input type="text">
  <button data-action="click->hello#greet">Greet</button>
</div>
```

  > **data-action** 的值 *click->hello#greet* 叫做动作描述符。其中：
  > - *click* 是事件名
  > - *hello* 是控制器标识符
  > - *greet* 是要执行的方法

在浏览器中加载该页面并打开开发者控制台。单击 *"Greet"* 按钮时，应该会看到日志消息。

### *targets* 将重要元素映射到控制器属性

最后通过更改 *action* 向在文本字段中输入的任何名称打招呼来完成练习。

为了做到这一点，首先需要一个对控制器内的输入元素的引用，然后读取 *value* 属性来获取其内容。

*Stimulus* 将重要元素标记为 *targets*，这样可以很容易的通过相应的属性在控制器中引用它们。打开 `public/index.html` 并将 **data-hello-target** 属性添加到 *input* 中：

```html
<div data-controller="hello">
  <input data-hello-target="name" type="text">
  <button data-action="click->hello#greet">Greet</button>
</div>
```

接下来，通过将 **name** 添加到控制器的目标定义列表中来为目标创建一个属性。*Stimulus* 将自动创建一个 **this.nameTarget** 属性，该属性返回第一个匹配的目标元素。可以使用此属性来读取元素的值并构建问候字符串。

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = [ "name" ]

  greet() {
    const element = this.nameTarget
    const name = element.value
    console.log(`Hello, ${name}!`)
  }
}
```
{: file='src/controllers/hello_controller.js'}

在浏览器中重新加载该页面并打开开发者控制台。在输入字段中输入你的姓名，然后单击 "Greet" 按钮。

### 控制器重构

*Stimulus* 的控制器是 *JavaScript* 类的实例，其方法可以充当事件处理程序。这意味着可以使用拥有标准重构技术进行重构。例如，可以将 *name* 的 *getter* 方法提取出来，使 *greet()* 方法更简洁：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = [ "name" ]

  greet() {
    console.log(`Hello, ${this.name}!`)
  }

  get name() {
    return this.nameTarget.value
  }
}
```
{: file='src/controllers/hello_controller.js'}
