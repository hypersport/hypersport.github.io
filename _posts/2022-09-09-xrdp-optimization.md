---
title: xrdp 优化连接速度
author: hypersport
date: 2022-09-09 09:09:09 +0800
toc: false
categories: [Linux]
tags: [xrdp]
render_with_liquid: false
---

在高分辨率的情况下，*xrdp* 连接会比较慢，这是因为默认的 *TCP* 缓冲区大小为 *32K*，可以通过修改 *xrdp* 的配置文件修改默认大小。


打开配置文件

```bash
sudo vim /etc/xrdp/xrdp.ini
```

将下面的部分注释删掉并修改缓冲区大小

```bash
tcp_send_buffer_bytes=4194304
tcp_recv_buffer_bytes=4194304
```

这里将大小设置为 *4M*，仅供参考，如果超过内核缓冲区大小，将以内核缓冲区大小为准。

重启 *xrdp* 服务

```bash
sudo systemctl restart xrdp.service
```
