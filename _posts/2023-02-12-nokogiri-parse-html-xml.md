---
title: Nokogiri - 解析 HTML XML
author: hypersport
date: 2023-02-12 23:02:12 +0800
toc: true
categories: [Ruby]
tags: [Nokogiri]
render_with_liquid: false
---

### 解析字符串

```ruby
html_doc = Nokogiri::HTML("<html><body><h1>Mr. Belvedere Fan Club</h1></body></html>")
xml_doc  = Nokogiri::XML("<root><aliens><alien><name>Alf</name></alien></aliens></root>")
```

变量 **html_doc** 和 **xml_doc** 都是 *Nokogiri* 文档，它们有很多有趣的属性和方法，可以点击 [这里](https://nokogiri.org/rdoc/Nokogiri/XML/Document) 了解。

### 解析文件

不需要将文件读取到字符串，*Nokogiri* 会自动处理：

```ruby
doc = File.open("blossom.xml") { |f| Nokogiri::XML(f) }
```

### 解析网页

```ruby
require 'open-uri'
doc = Nokogiri::HTML(URI.open("http://www.threescompany.com/"))
```

### 解析选项

*Nokogiri* 提供了许多影响文档解析方式的选项，可以在 [XML::ParseOptions](https://nokogiri.org/rdoc/Nokogiri/XML/ParseOptions) 文档中了解这些选项。

注意，*Nokogiri* 会默认将输入视为不受信任的文档，从而避免 [XXE](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Processing) 或 **XML eXternal Entity** 处理漏洞。这也意味着 *Nokogiri* 不会尝试加载外部 *DTD* 或访问任何外部网络资源。

一些具有安全隐患的常用 [解析选项](https://nokogiri.org/rdoc/Nokogiri/XML/ParseOptions)：

- **NONET** - 解析过程中禁止任何网络连接（默认选项）
> **⚠** 解析不受信任的文档时取消设置此选项是 **不安全** 的

- **NOENT** - 替代实体
> **⚠** 解析不受信任的文档时设置此选项是 **不安全** 的

- **DTDLOAD DTDVALID** - 如果需要 *DTD* 验证
> **⚠** 解析不受信任的文档时设置 *DTDLOAD* 是 **不安全** 的

- **HUGE** - 跳过文档大小或 *DOM* 深度的硬编码限制
> **⚠** 解析不受信任的文档时设置 *HUGE* 是 **不安全** 的

其他常用的 [解析选项](https://nokogiri.org/rdoc/Nokogiri/XML/ParseOptions)：

- **RECOVER** - 尝试从错误中恢复（默认选项）
> 建议用于解析格式错误或无效的文档

- **NOBLANKS** - 删除空白节点

- **NOERROR** - 禁止报告错误

- **STRICT** - 严格解析：解析格式错误的文档时引发错误

可以手动指定这些选项（不推荐）：

```ruby
doc = Nokogiri::XML(File.open("blossom.xml")) do |config|
  config.options = Nokogiri::XML::ParseOptions::STRICT | Nokogiri::XML::ParseOptions::NOBLANKS
end
```

更常用的做法是在配置对象上使用可链接的快捷方式：

```ruby
doc = Nokogiri::XML(File.open("blossom.xml")) do |config|
  config.strict.noblanks
end
```

如果想关闭默认设置的选项，可以在配置快捷方式前添加 **no** 前缀：

```ruby
doc = Nokogiri::XML(File.open("blossom.xml")) do |config|
  config.norecover
end
```

可能令人感到奇怪但合乎逻辑的 **nononet** 会重新打开网络连接：

```ruby
doc = Nokogiri::XML(File.open("blossom.xml")) do |config|
  config.nononet
end
```

### 编码方式

字符串在内部始终存储为 *UTF-8*。返回文本值的方法将始终返回 *UTF-8* 编码的字符串。返回 *XML* 的方法（如 *to_xml*、*to_html* 和 *inner_html*）将返回与源文件一样编码的字符串。

  > 有些文档声明了一种特定的编码，但使用的却是另一种编码。那么，解析器应该选择哪种编码呢？
  >
  > 记住数据只是字节流，只是人们为字节流增添了意义。任何特定的字节集都可能是多种编码中的有效字符，因此不可能 *100%* 准确地检测编码。*libxml2* 尽力在做，但也不可能 *100%* 正确。
  >
  > 如果想让 *Nokogiri* 正确处理文档编码，最好的办法是显式设置编码。例如将编码显式设置为 *EUC-JP*：
  >
  > ```ruby
  > doc = Nokogiri.XML('<foo><bar /><foo>', nil, 'EUC-JP')
  > ```
