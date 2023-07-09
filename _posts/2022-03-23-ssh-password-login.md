---
title: SSH 使用密码登录服务器
author: hypersport
date: 2022-03-23 13:23:33 +0800
toc: false
categories: [Linux]
tags: [SSH]
render_with_liquid: false
---

云服务器使用 `SSH` 密码登录，以 `Ubuntu` 为例，适用于 `Oracle`、`Google` 等

```bash
# 切换root账户
sudo -i
# 开启root登录，如不需要跳过
vim /root/.ssh/authorized_key
# 将 ssh-rsa 前面的内容全部删除保存退出
# ubuntu 用户目录下的 .ssh/authorized_key 同样处理

# 修改 SSH 连接配置文件
vim /etc/sshd/sshd_config

Port 22 # 将前面的 # 号删除以启用 22 端口，可修改为自定义端口，但注意服务器安全组需要提前放行
PermitRootLogin yes # 将前面的 # 号删除，改成加空格 yes 代表启用 root 登录，如不需要跳过
PubkeyAuthentication yes # 在前面添加 # 号代表关闭密钥认证登录
PasswordAuthentication yes # 将前面 # 删除并加空格 yes 代表启用密码登录
# 修改完成后保存退出

# 重启 SSH 服务
systemctl restart sshd.service
# 修改root密码
passwd
# 如果 ubuntu 用户没有设置密码，同样需要设置密码
passwd ubuntu
```
