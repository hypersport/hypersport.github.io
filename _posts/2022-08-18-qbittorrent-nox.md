---
title: qBittorrent 配置种子下载服务器
author: hypersport
date: 2022-08-18 20:20:20 +0800
toc: false
categories: [Linux]
tags: [qBittorrent]
render_with_liquid: false
---

**qBittorrent** 是一个跨平台的自由 *BitTorrent* 客户端，有多种图形化工具，包含 web 和桌面环境。 **qBittorrent-nox** 是一个仅包含 web 界面而不包含桌面环境的版本，适合在轻量级服务上使用。

### 安装软件：

```bash
sudo apt update
sudo apt install qbittorrent-nox 
```

### 启动服务

```bash
sudo systemctl start qbittorrent-nox@ubuntu
sudo systemctl enable qbittorrent-nox@ubuntu
```

之后在浏览器中访问 *host-ip:8080* 进去管理界面

> 账号：admin

> 密码：adminadmin
