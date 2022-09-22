---
title: 使用树莓派作为种子下载服务器
author: hypersport
date: 2021-10-20 20:20:20 +0800
toc: false
categories: [Linux]
tags: [树莓派, 下载器]
render_with_liquid: false
---

**transmission-daemon** 比其他客户端使用更少的资源，非常适合服务器，还可以通过 Web 界面和终端程序进行远程控制，支持本地对等发现、完全加密、DHT、µTP、PEX 和 Magnet Link 等。

首先，通过 **apt** 安装软件包，这里注意安装的是 **transmission-daemon**：

```bash
sudo apt-get update
sudo apt-get install transmission-daemon
```

然后修改 `/etc/transmission-daemon/` 目录下的配置文件 `settings.json`

```json
{
    "alt-speed-down": 50,
    "alt-speed-enabled": false,
    "alt-speed-time-begin": 540,
    "alt-speed-time-day": 127,
    "alt-speed-time-enabled": false,
    "alt-speed-time-end": 1020,
    "alt-speed-up": 50,
    "bind-address-ipv4": "0.0.0.0",
    "bind-address-ipv6": "::",
    "blocklist-enabled": false,
    "blocklist-url": "http://www.example.com/blocklist",
    "cache-size-mb": 4,
    "dht-enabled": true,
    "download-dir": "/home/ubuntu/transmission", # 下载目录 
    "download-limit": 100,
    "download-limit-enabled": 0,
    "download-queue-enabled": true,
    "download-queue-size": 5,
    "encryption": 1,
    "idle-seeding-limit": 30,
    "idle-seeding-limit-enabled": false,
    "incomplete-dir": "/home/ubuntu/transmission", # 下载未完成目录
    "incomplete-dir-enabled": true,
    "lpd-enabled": false,
    "max-peers-global": 200,
    "message-level": 1,
    "peer-congestion-algorithm": "",
    "peer-id-ttl-hours": 6,
    "peer-limit-global": 200,
    "peer-limit-per-torrent": 50,
    "peer-port": 51413,
    "peer-port-random-high": 65535,
    "peer-port-random-low": 49152,
    "peer-port-random-on-start": false,
    "peer-socket-tos": "default",
    "pex-enabled": true,
    "port-forwarding-enabled": false,
    "preallocation": 1,
    "prefetch-enabled": true,
    "queue-stalled-enabled": true,
    "queue-stalled-minutes": 30,
    "ratio-limit": 2,
    "ratio-limit-enabled": false,
    "rename-partial-files": true,
    "rpc-authentication-required": true,
    "rpc-bind-address": "0.0.0.0",
    "rpc-enabled": true,
    "rpc-host-whitelist": "",
    "rpc-host-whitelist-enabled": true,
    "rpc-password": "{f3052668aaa8487e22c777327ad83dc79ed25ff6mhRGXhjC", # 这里设置 web 访问的秘密，设置成明文，重新启动后会自动变成密文
    "rpc-port": 9091, # web 访问端口
    "rpc-url": "/transmission/",
    "rpc-username": "ubuntu", # web 访问用户名
    "rpc-whitelist": "*.*.*.*",
    "rpc-whitelist-enabled": true,
    "scrape-paused-torrents-enabled": true,
    "script-torrent-done-enabled": false,
    "script-torrent-done-filename": "",
    "seed-queue-enabled": false,
    "seed-queue-size": 10,
    "speed-limit-down": 2048,
    "speed-limit-down-enabled": false,
    "speed-limit-up": 5,
    "speed-limit-up-enabled": true,
    "start-added-torrents": true,
    "trash-original-torrent-files": false,
    "umask": 0, # 下载目录权限设置为 777
    "upload-limit": 100,
    "upload-limit-enabled": 0,
    "upload-slots-per-torrent": 14,
    "utp-enabled": true # 允许 web 访问
}
```

修改完成后保存并启动服务

```bash
sudo systemctl start transmission-daemon.service
```

之后在浏览器中访问 *raspberry-ip:9091* 进去管理界面
