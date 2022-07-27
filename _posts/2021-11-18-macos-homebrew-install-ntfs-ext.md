---
title: macOS 使用 Homebrew 在线安装 NTFS / EXT4 支持
author: hypersport
date: 2021-11-18 11:18:11 +0800
toc: false
categories: [Macos]
tags: [Homebrew, ntfs, ext]
render_with_liquid: false
---

因为 *ntfs-3g*、*ext4fuse* 等软件依赖的 *macFUSE* 不是开源软件，在使用 *Homebrew* 安装这些软件的时候提示 `disable! date: "2021-04-08", because: "requires closed-source macFUSE"`，导致软件无法安装。下面解决方法以 *ext4fuse* 为例：

1. 执行
```bash
vi `brew formula ext4fuse`
```

2. 找到下面的语句用 `#` 注释掉并保存退出
```bash
on_macos do
    disable! date: "2021-04-08", because: "requires closed-source macFUSE"
end
```

3. 执行
```bash
brew install ext4fuse
```
进行安装即可

*ntfs-3g*、*ext2fuse*、*ifuse* 等软件遇到相同问题时的解决方法也一样，找到对应的文件后将这三条语句注释掉即可正常安装。