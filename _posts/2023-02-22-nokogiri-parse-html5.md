---
title: Nokogiri - 解析 HTML5
author: hypersport
date: 2023-02-22 23:02:22 +0800
toc: true
categories: [Ruby]
tags: [Nokogiri]
render_with_liquid: false
---

*Nokogiri* 使 *Ruby* 可以调用 [Gumbo HTML5 解析器](https://github.com/sparklemotion/nokogiri/tree/main/gumbo-parser) 并返回 *Nokogiri::HTML::Document*。

> **⚠** 仅 **v1.12.0** 及更高版本支持 *HTML5*

> **⚠** 目前暂不支持 *JRuby*

### 用法

解析 *HTML5* 文档：

```ruby
doc = Nokogiri.HTML5(string)
```

解析 *HTML5* 片段：

```ruby
fragment = Nokogiri::HTML5.fragment(string)
```

### 解析选项

```ruby
Nokogiri.HTML5(html, url = nil, encoding = nil, options = {})

Nokogiri::HTML5.parse(html, url = nil, encoding = nil, options = {})

Nokogiri::HTML5::Document.parse(html, url = nil, encoding = nil, options = {})

Nokogiri::HTML5.fragment(html, encoding = nil, options = {})

Nokogiri::HTML5::DocumentFragment.parse(html, encoding = nil, options = {})
```

目前支持的三个选项是 *:max_errors*、*:max_tree_depth* 和 *:max_attributes*。

### 错误报告

*Nokogiri* 包含一个实验性的 *HTML5* 解析错误报告工具。默认情况下，不会有解析错误报告，但可以向 [HTML5.parse](https://nokogiri.org/rdoc/Nokogiri/HTML5.html#parse-class_method) 或 [HTML5.fragment](https://nokogiri.org/rdoc/Nokogiri/HTML5.html#fragment-class_method) 传递 *:max_errors* 选项进行配置。

例如下面的代码：

```ruby
doc = Nokogiri::HTML5.parse('<span/>Hi there!</span foo=bar />', max_errors: 10)
doc.errors.each do |err|
  puts(err)
end
```

错误输出：

```ruby
1:1: ERROR: Expected a doctype token
<span/>Hi there!</span foo=bar />
^
1:1: ERROR: Start tag of nonvoid HTML element ends with '/>', use '>'.
<span/>Hi there!</span foo=bar />
^
1:17: ERROR: End tag ends with '/>', use '>'.
<span/>Hi there!</span foo=bar />
                ^
1:17: ERROR: End tag contains attributes.
<span/>Hi there!</span foo=bar />
                ^
```

使用 *max_errors：-1* 可以设置返回的错误数量不受限制。

*HTML* 标准定义了一系列 [标准解析错误代码](https://html.spec.whatwg.org/multipage/parsing.html#parse-errors)。这些错误代码只涵盖 *HTML* 解析的 *标记化* 阶段。*树构建* 阶段的解析错误还没有标准化的错误代码。

为了方便 *Nokogiri* 用户，可以通过 **Nokogiri::XML::SyntaxError#str1** 方法获取定义的错误代码：

```ruby
doc = Nokogiri::HTML5.parse('<span/>Hi there!</span foo=bar />', max_errors: 10)
doc.errors.each do |err|
  puts("#{err.line}:#{err.column}: #{err.str1}")
end
# => 1:1: generic-parser
#    1:1: non-void-html-element-start-tag-with-trailing-solidus
#    1:17: end-tag-with-trailing-solidus
#    1:17: end-tag-with-attributes
```

注意，第一个错误是 *generic-parser*，这是 *树构建* 阶段的错误，没有标准化的错误代码。

就版本而言，错误信息、错误位置和错误代码并不是 *Nokogiri* 公共 *API* 的一部分。也就是说，在 *Nokogiri* 主版本号不变的情况下，这些内容可能会发生变化。将来这些内容可能会稳定下来。

### 最大树深度

各种解析方法可解析的 *DOM* 树的最大深度可以通过 *:max_tree_depth* 选项进行配置，如果树的深度超过此限制，则会抛出一个 *::ArgumentError* 错误。

可以通过设置 *max_tree_depth: -1* 取消此限制（默认为 *Nokogiri::Gumbo::DEFAULT_MAX_TREE_DEPTH = 400*）。

```ruby
html = '<!DOCTYPE html>' + '<div>' * 1000
doc = Nokogiri.HTML5(html)
# raises ArgumentError: Document tree depth limit exceeded
doc = Nokogiri.HTML5(html, max_tree_depth: -1)
```

### 每个元素的属性个数限制

每个 *DOM* 元素的最大属性个数可以通过 *:max_attributes* 选项进行配置，如果元素的个数超过此限制，则会抛出一个 *::ArgumentError* 错误。

可以通过设置 *max_attributes: -1* 取消此限制（默认为 *Nokogiri::Gumbo::DEFAULT_MAX_ATTRIBUTES = 400*）。

```ruby
html = '<!DOCTYPE html><div ' + (1..1000).map { |x| "attr-#{x}" }.join(' ') + '>'
# "<!DOCTYPE html><div attr-1 attr-2 attr-3 ... attr-1000>"
doc = Nokogiri.HTML5(html)
# raises ArgumentError: Attributes per element limit exceeded
doc = Nokogiri.HTML5(html, max_attributes: -1)
```

### *HTML* 序列化

解析完 *HTML* 后，可以使用任意 *Nokogiri::XML::Node* 序列化方法对其进行序列化。特别是 *XML::Node#serialize*、*#to_html* 和 *#to_s* 将会序列化给定的节点及其子节点（相当于 *JavaScript* 的 *Element.outerHTML*）。同样 *XML::Node#inner_html* 将会序列化给定节点的子节点（相当于 *JavaScript* 的 *Element.innerHTML*）。

```ruby
doc = Nokogiri::HTML5("<!DOCTYPE html><span>Hello world!</span>")
puts doc.serialize
# => <!DOCTYPE html><html><head></head><body><span>Hello world!</span></body></html>
```

### 注意

- *Nokogiri::HTML5.fragment* 函数接收一个字符串，并将其解析为 *HTML5* 文档。*\<html\>*、*\<head\>* 和 *\<body\>* 元素会从该文档中删除，这些元素的所有子元素都会作为 *Nokogiri::HTML5::DocumentFragment* 返回。

- *Nokogiri::HTML5.parse* 函数接受一个字符串，使用默认选项传递给 *gumbo_parse_with_options* 方法，然后遍历生成的 *Gumbo* 解析树。

- 生成小写的元素名，而不是大写。

- 对于未知标签，不会返回 *unknown* 作为元素名称，而是返回原始的标签名。