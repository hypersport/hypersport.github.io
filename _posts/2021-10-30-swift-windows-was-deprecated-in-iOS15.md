---
title: "Swift warning: 'windows' was deprecated in iOS 15.0"
author: hypersport
date: 2021-10-30 11:11:11 +0800
toc: false
categories: [Swift]
tags: [iOS]
render_with_liquid: false
---

```swift
import SwiftUI

extension UIApplication {
    var keyWindow: UIWindow? {
        // Get connected scenes
        return UIApplication.shared.connectedScenes
        // Keep only active scenes, onscreen and visible to the user
            .filter { $0.activationState == .foregroundActive }
        // Keep only the first `UIWindowScene`
            .first(where: { $0 is UIWindowScene })
        // Get its associated windows
            .flatMap({ $0 as? UIWindowScene })?.windows
        // Finally, keep only the key window
            .first(where: \.isKeyWindow)
    }
}

/*
    From:
        https://stackoverflow.com/a/68989580
    Usage e.g.:
        let windowScene = UIApplication.shared.keyWindow?.windowScene
 */
```
