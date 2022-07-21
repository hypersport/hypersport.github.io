---
title: Swift 全角半角转换
author: hypersport
date: 2021-10-10 10:10:10 +0800
toc: false
categories: [Swift]
tags: [全角, 半角]
render_with_liquid: false
---

```swift
import Foundation

extension String {
    // 全角字符串转为半角字符串
    public var halfwidthString: String {
        let text:CFMutableString = NSMutableString(string: self) as CFMutableString
        CFStringTransform(text, nil, kCFStringTransformFullwidthHalfwidth, false)
        return text as String
    }
    
    // 半角字符串转为全角字符串
    public var fullwidthString: String {
        let text:CFMutableString = NSMutableString(string: self) as CFMutableString
        CFStringTransform(text, nil, kCFStringTransformFullwidthHalfwidth, true)
        return text as String
    }
}
```

其他类型的字符串转换类似，具体可查阅 [官方文档](https://developer.apple.com/documentation/corefoundation/cfmutablestring/transform_identifiers_for_cfstringtransform) 。
