---
title: Stimulus - Building Something Real
author: hypersport
date: 2022-11-22 11:22:33 +0800
categories: [前端]
tags: [JavaScript, Stimulus]
render_with_liquid: false
img_path: /assets/img/posts/2022-11-22-stimulus-build-something-real/
---

在前面的章节中实现了第一个控制器，并了解了 *Stimulus* 如何将 *HTML* 连接到 *JavaScript*，现在通过重新实现 *Basecamp* 网站上一个的控制器来看一下如何在真实应用中使用 *Stimulus*。

### 封装 *DOM* 剪贴板的 *API*

*Basecamp* 网站 *UI* 中的按钮：

![Clipboard UI](clipboard-ui.png "Clipboard UI")

当点击按钮的时候，*Basecamp* 会将文本（例如 *URL* 或电子邮件地址）复制到剪贴板。

*Web* 平台有一个用于访问系统剪贴板的 [API](https://www.w3.org/TR/clipboard-apis/)，但没有 *HTML* 元素可以满足我们的需要。因此，为了实现一个 "复制到剪贴板" 功能的按钮，就必须使用 *JavaScript*。

### 实现一个复制按钮

假设一个应用程序，允许通过生成 *PIN* 码来授予人们访问权限。那么，如果在生成的 *PIN* 码旁边有一个可以将它复制到系统剪贴板的按钮，就可以很方便的进行分享。

打开 `public/index.html` 并将 *body* 中的内容替换：

```html
<div>
  PIN: <input type="text" value="1234" readonly>
  <button>Copy to Clipboard</button>
</div>
```

### 设置控制器

创建 `src/controllers/clipboard_controller.js` 并添加一个空的 *copy* 方法：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  copy() {
  }
}
```
{: file='src/controllers/clipboard_controller.js'}

然后在外层 *div* 中添加 **data-controller="clipboard"**，每当这个有属性的元素出现时，*Stimulus* 就会连接到控制器的一个实例：

```html
<div data-controller="clipboard">
```

### 定义目标

添加一个对文本字段的引用，便于在调用剪贴板 *API* 之前获取其内容。将 **data-clipboard-target="source"** 添加到 *input* 标签中：

```html
  PIN: <input data-clipboard-target="source" type="text" value="1234" readonly>
```

然后将一个目标定义添加到控制器，以便将文本字段元素作为 *this.sourceTarget* 访问：

```js
export default class extends Controller {
  static targets = [ "source" ]

  // ...
}
```

  > **static targets** 的作用：
  >
  > 当 *Stimulus* 加载控制器类时，会在名为 *"targets"* 的静态数组中查找目标名称字符串。对于数组中的每个目标名称，*Stimulus* 都会向控制器添加三个新属性：
  > - *this.sourceTarget* 是控制器作用域中的第一个源目标。如果没有源目标，访问该属性将引发错误。
  > - *this.sourceTargets* 是控制器作用域内所有源目标的数组。
  > - *this.hasSourceTarget* 如果存在源目标则为 *true*，否则为 *false*。
  >
  > 更多关于 [target](https://stimulus.hotwired.dev/reference/targets) 的文档。

### 连接 *Action*

通过添加 **data-action="clipboard#copy"**，使得单击按钮时可以调用控制器中的 *copy()* 方法：

```html
  <button data-action="clipboard#copy">Copy to Clipboard</button>
```

  > 注意上面的动作描述符省略了 *click->*，这是因为 *Stimulus* 将 *button* 的默认事件动作定义为 *click*。一些其他的元素也都有默认事件定义，下面是完整的列表：
  >
  > | 元素               | 默认事件 |
  > |:------------------|:---------|
  > | a                 | click    |
  > | button            | click    |
  > | details           | toggle   |
  > | form              | submit   |
  > | input             | input    |
  > | input type=submit | click    |
  > | select            | change   |
  > | textarea          | input    |

最后，在 *copy()* 方法中，选择输入框中的内容并调用剪贴板 *API*:

```js
  copy() {
    navigator.clipboard.writeText(this.sourceTarget.value)
  }
```

在浏览器中加载页面，并点击 *Copy* 按钮。然后切换到文本编辑器并粘贴，这时应该能看到 *PIN* 码 - *1234*。

### 控制器可重复使用

上面的例子中，页面上只有一个控制器实例，但一个页面上有多个控制器实例是很常见的，比如一个 *PIN* 码列表，每个 *PIN* 码都有自己的复制按钮。

控制器可重复使用：任何时候想要将文本复制到剪贴板，只需要在页面上标上正确的标记。

在原来的页面上复制并粘贴 *div* 标签，这样就有两个相同的 *PIN* 字段，然后更改第二个的value属性:

```html
<div data-controller="clipboard">
  PIN: <input data-clipboard-target="source" type="text" value="3737" readonly>
  <button data-action="clipboard#copy">Copy to Clipboard</button>
</div>
```

重新加载页面并确认两个按钮都正常工作。

### *actions* 和 *targets* 适用于任何元素

再添加一个 *PIN* 码，但这次使用复制链接而不是按钮：

```html
<div data-controller="clipboard">
  PIN: <input data-clipboard-target="source" type="text" value="3737" readonly>
  <a href="#" data-action="clipboard#copy">Copy to Clipboard</a>
</div>
```

只要有正确的 **data-action** 属性，*Stimulus* 可以适用于任何元素。

注意在这种情况下，浏览器还是会跳转链接的 *href*，要取消这种默认行为，可以在 *action* 中调用 **event.preventDefault()**:

```js
  copy(event) {
    event.preventDefault()
    navigator.clipboard.writeText(this.sourceTarget.value)
  }
```

类似的，源目标也不一定是 *\<input type="text"\>*，控制器只需要它有一个 **value** 属性和一个 **select()** 方法。因此也可以使用 *textarea* 来代替:

```html
  PIN: <textarea data-clipboard-target="source" readonly>3737</textarea>
```
