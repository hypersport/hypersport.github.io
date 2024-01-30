---
title: Stimulus - 状态管理
author: hypersport
date: 2022-12-22 22:22:22 +0800
categories: [前端]
tags: [JavaScript, Stimulus]
toc: true
render_with_liquid: false
---

大多数现代框架都鼓励始终在 *JavaScript* 中保持状态的做法，它们将 *DOM* 视为只写目标，通过从服务端获得的 *JSON* 渲染客户端模板。

*Stimulus* 采用了不同的做法，将应用的状态以属性的方式存在于 *DOM* 中，控制器本身是无状态的。这种方法使得可以从任何地方（初始文档、*Ajax* 请求、*Turbo* 访问，甚至是另一个 *JavaScript* 库）使用 *HTML*，并且无需任何显式初始化步骤即可自动启动相关联的控制器。

### 构建一个幻灯片

前面的章节中展示了 *Stimulus* 控制器如何通过向元素中添加一个类名来维持文档中的简单状态，现在通过构建一个幻灯片控制器来展示如何存储值，这个控制器将当前选择的幻灯片索引保存在属性中。

和之前一样，首先从 *HTML* 开始：

```html
<div data-controller="slideshow">
  <button data-action="slideshow#previous"> ← </button>
  <button data-action="slideshow#next"> → </button>

  <div data-slideshow-target="slide">🐵</div>
  <div data-slideshow-target="slide">🙈</div>
  <div data-slideshow-target="slide">🙉</div>
  <div data-slideshow-target="slide">🙊</div>
</div>
```

每个 *slide* 目标代表幻灯片中的一张幻灯片，控制器将负责确保一次只有一张幻灯片可见。

接下来创建控制器，新建 `src/controllers/slideshow_controller.js` 文件：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = [ "slide" ]

  initialize() {
    this.index = 0
    this.showCurrentSlide()
  }

  next() {
    this.index++
    this.showCurrentSlide()
  }

  previous() {
    this.index--
    this.showCurrentSlide()
  }

  showCurrentSlide() {
    this.slideTargets.forEach((element, index) => {
      element.hidden = index !== this.index
    })
  }
}
```
{: file='src/controllers/slideshow_controller.js'}

上面的控制器中定义了一个方法 *showCurrentSlide()*，该方法循环遍历每个 *slide* 目标，如果索引匹配则切换 **hiden** 属性。同时通过显示第一张幻灯片来初始化控制器，*next()* 和 *previous()* 方法负责控制前进和后退幻灯片。

  > 生命周期回调方法：
  >
  > *Stimulus* 生命周期回调方法对于在控制器进入或离开文档时设置或取消关联状态非常有用。
  >
  > | 方法           | 调用时机 |
  > |:---------------|:---------|
  > | ainitialize()  | 当控制器第一次被实例化的时候调用一次     |
  > | connect()      | 每次当控制器连接到 *DOM* 的时候调用     |
  > | disconnect()   | 每次当控制器与 *DOM* 断开连接的时候调用 |

重新加载页面并确认 *Next* 按钮可以前进到下一张幻灯片。

### 从 *DOM* 读取初始状态

注意控制器如何在 *this.index* 属性中追踪其状态（当前选定的幻灯片）。

如果想从第二张而不是第一张幻灯片开始播放，应该怎么标记呢？

一种方法是使用 *HTML* 数据属性加载初始索引。例如，可以向控制器的元素添加 data-index 属性：

```html
<div data-controller="slideshow" data-index="1">
```

然后，在 *initialize()* 方法中读取该属性，并将其转换为整数传递给 *showCurrentSlide()* 方法：

```js
  initialize() {
    this.index = Number(this.element.dataset.index)
    this.showCurrentSlide()
  }
```

这可能会达到目的，但有点笨拙，需要开发人员决定如何命名该属性，并且对再次访问索引或增加索引值并将结果保留在 *DOM* 中这种需求没有任何帮助。

### 使用 *values*

*Stimulus* 控制器支持自动映射到数据属性的值属性。当把值定义添加到控制器类的顶部时：

```js
  static values = { index: Number }
```

*Stimulus* 将创建一个与 **data-slideshow-index-value** 属性关联的 **this.indexValue** 控制器属性，并自动处理数字转换。

将关联的数据属性添加到 *HTML* 中：

```html
<div data-controller="slideshow" data-slideshow-index-value="1">
```

然后将 *static values* 定义添加到控制器并更改 *initialize()* 方法以记录 *this.indexValue*：

```js
export default class extends Controller {
  static values = { index: Number }

  initialize() {
    console.log(this.indexValue)
    console.log(typeof this.indexValue)
  }

  // …
}
```

重新加载页面并验证控制台是否显示 *1* 和 *Number*。

  > 关于 **static values**：
  >
  > 与 *"targets"* 类似，通过在称为 *values* 的静态对象属性中描述它们来定义 *Stimulus* 控制器中的值。在本例中，定义的是一个称为 *index* 的数字类型的值。
  >
  > 更多关于 [值定义](https://stimulus.hotwired.dev/reference/targets) 的文档。

更新 *initialize()* 方法和控制器中的其他方法，使用 *this.indexValue* 而不是 *this.index*：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = [ "slide" ]
  static values = { index: Number }

  initialize() {
    this.showCurrentSlide()
  }

  next() {
    this.indexValue++
    this.showCurrentSlide()
  }

  previous() {
    this.indexValue--
    this.showCurrentSlide()
  }

  showCurrentSlide() {
    this.slideTargets.forEach((element, index) => {
      element.hidden = index !== this.indexValue
    })
  }
}
```

重新加载页面并使用网页检查器确认控制器元素的 *data-slideshow-index-value* 属性在从一张幻灯片移动到下一张幻灯片时发生变化。

### 更改回调

修改后的控制器在原始版本的基础上进行了改进，但在重复调用 *this.showCurrentSlide()*。当控制器初始化以及在每次更新 *this.indexValue* 后，都必须手动更新文档的状态。

可以通过定义一个 *Stimulus* 值更改回调来清理重复，并指定控制器在索引值更改时应如何响应。

首先，删除 *initialize()* 方法并定义一个新方法 *indexValueChanged()*，然后删除 *next()* 和 *previous()* 中对 *this.showCurrentSlide()* 的调用：

```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = [ "slide" ]
  static values = { index: Number }

  next() {
    this.indexValue++
  }

  previous() {
    this.indexValue--
  }

  indexValueChanged() {
    this.showCurrentSlide()
  }

  showCurrentSlide() {
    this.slideTargets.forEach((element, index) => {
      element.hidden = index !== this.indexValue
    })
  }
}
```

重新加载页面并确认幻灯片跟之前一样。

*Stimulus* 在初始化时调用 *indexValueChanged()* 方法并响应 *data-slideshow-index-value* 属性的任何更改。甚至也可以直接在网页检查器中修改该属性，控制器也会作出响应更改幻灯片。

### 设置默认值

也可以设置默认值作为静态定义的一部分，如下：

```js
static values = { index: { type: Number, default: 2 } }
```

如果控制器元素上未定义 *data-slideshow-index-value* 属性，那么索引将从 *2* 开始。如果有其他值，还可以混合并匹配需要默认值和不需要默认值的值：

```js
  static values = { index: Number, effect: { type: String, default: "kenburns" } }
```
