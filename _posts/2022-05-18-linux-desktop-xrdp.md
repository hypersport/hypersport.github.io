---
title: Linux 安装桌面环境配置远程连接
author: hypersport
date: 2022-05-18 23:05:18 +0800
categories: [Linux]
tags: [远程桌面]
render_with_liquid: false
---

### 安装桌面环境

以 `Ubuntu` 为例，桌面环境为 `xfce4`

```bash
sudo apt update
sudo DEBIAN_FRONTEND=noninteractive apt install xfce4
sudo apt install xfce4-session
```

### 安装配置远程工具

```bash
sudo apt install xrdp
sudo systemctl enable xrdp
echo xfce4-session > ~/.xsession
```

如果是 `Ubuntu 20`，需要允许 `xrdp` 访问证书

```bash
sudo adduser xrdp ssl-cert
```

重启 `xrdp` 服务

```bash
sudo systemctl restart xrdp
```

如果用户没有设置密码，需要先设置密码

```bash
sudo passwd ubuntu
```
