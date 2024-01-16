---
title: Stimulus - 弹性设计
author: hypersport
date: 2022-12-12 12:12:12 +0800
categories: [前端]
tags: [JavaScript, Stimulus]
toc: false
render_with_liquid: false
---

尽管剪贴板 *API* 在[当前浏览器中得到了很好的支持](https://caniuse.com/#feat=clipboard)，但仍然要想到可能有少数人在旧版本的浏览器中使用我们的应用程序。

还应该预想到人们在访问应用程序时时不时地会遇到问题。例如，间歇性的网络连接问题或 *CDN* 可用性问题都可能会阻止部分或全部 *JavaScript* 加载。

有些人认为不值得为老版本浏览器提供支持，或者认为间歇性的网络问题在刷新后就会自动解决。但通常情况下，可以以一种优雅的构建方式来更加弹性的适应这些问题。

这种弹性的方式通常被称为 **渐进式增强(progressive enhancement)**，是一种 *web* 界面的交付实践，基本功能在 *HTML* 和 *CSS* 中实现，在浏览器支持的情况下，逐渐升级到由 *CSS* 和 *JavaScript* 实现。

### 渐进式增强 *PIN* 字段

逐步增强 *PIN* 字段，使复制按钮在浏览器不支持的情况下不可见，这样可以避免向其他人展示一个不起作用的按钮。

首先在 *CSS* 中隐藏复制按钮，然后在 *Stimulus* 控制器中对剪贴板 *API* 进行功能支持测试。如果支持 *API*，则向控制器元素添加一个类名以显示按钮。

首先将 **data-clipboard-supported-class="clipboard--supported"** 添加到具有 **data-controller** 属性的 *div* 中：

```html
  <div data-controller="clipboard" data-clipboard-supported-class="clipboard--supported">
```

然后将 **class="clipboard-button"** 添加到按钮上：

```html
  <button data-action="clipboard#copy" class="clipboard-button">Copy to Clipboard</button>
```

然后向 `public/main.css` 中添加样式：

```css
.clipboard-button {
  display: none;
}

.clipboard--supported .clipboard-button {
  display: initial;
}
```

接下来，首先将 **data-clipboard-supported-class** 属性作为静态类添加到控制器内：

```js
static classes = [ "supported" ]
```

这样就可以控制 *HTML* 中的特定 *CSS* 类，使得控制器可以更轻松地适应不同的 *CSS* 方法。通过这种方式添加的特定的类可以通过 *this.supportedClass* 访问。

现在向控制器添加一个 *connect()* 方法，该方法将测试是否支持剪贴板 *API*，并向控制器元素添加一个类名：

```js
  connect() {
    if ("clipboard" in navigator) {
      this.element.classList.add(this.supportedClass);
    }
  }
```

这个方法可以放置在控制器类主体中的任何位置。

如果想进行测试的话，在浏览器中禁用 *JavaScript*，重新加载页面，就会发现复制按钮不再可见。

这样就逐步增强了 *PIN* 字段：其复制按钮的基线状态是隐藏的，只有当 *JavaScript* 检测到对剪贴板 *API* 的支持时才变得可见。
