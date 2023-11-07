---
title: Ubuntu 远程桌面配置
author: hypersport
date: 2022-08-08 23:08:18 +0800
categories: [Linux]
tags: [远程桌面, ubuntu, xrdp]
render_with_liquid: false
---

*Ubuntu* 远程桌面默认使用 *Gnome* 布局，如果使用不习惯，可以替换成 *Ubuntu* 布局。

创建配置文件

```bash
vim ~/.xsessionrc
```

添加一下内容

```bash
export GNOME_SHELL_SESSION_MODE=ubuntu
export XDG_CURRENT_DESKTOP=ubuntu:GNOME
export XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
```

重启 *xrdp* 服务

```bash
sudo systemctl restart xrdp.service
```
