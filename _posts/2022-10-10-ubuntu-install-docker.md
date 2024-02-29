---
title: Ubuntu 22.04 安装 Docker
author: hypersport
date: 2022-10-10 10:10:10 +0800
categories: [Linux]
tags: [docker]
toc: true
render_with_liquid: false
---

### 1. 安装证书并允许 *apt* 通过 *HTTPS* 使用存储库

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg lsb-release
```

### 2. 添加 *Docker* 官方 *GPG* 密钥

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### 3. 添加 *Docker* 官方库

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 4. 更新源列表并安装 *Docker* 和 *docker-compose*

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose docker-compose-plugin
```

### 5. 查看 *Docker* 服务状态

```bash
sudo systemctl status docker.service
```

如果服务没有启动，启动 *Docker* 服务

```bash
sudo systemctl start docker.service
```
