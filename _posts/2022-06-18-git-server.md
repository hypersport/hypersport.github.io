---
title: Linux 搭建 git 服务器
author: hypersport
date: 2022-06-18 23:06:18 +0800
categories: [Linux]
tags: [git]
render_with_liquid: false
---

### 1. 安装 *git*

各个发行版都可以使用自带的包管理工具直接下载，下面以 *apt* 为例

```bash
sudo apt update
sudo apt install git -y
```

### 2. 添加 *git* 用户

```bash
sudo adduser git
```

### 3. 给 *git* 用户创建 *.ssh* 目录并设置相应的权限

```bash
su git                            # 切换用户
cd                                # 切换到 git 用户目录
mkdir .ssh                        # 创建 .ssh 目录
chmod 700 .ssh                    # 修改权限
touch .ssh/authorized_keys        # 创建 authorized_keys
chmod 600 .ssh/authorized_keys    # 修改权限
```

之后将需要使用 *git* 服务的用户的 *public key* 拷贝到上述 *authorized_keys* 文件中

```bash
cat id_rsa.pub >> ~/.ssh/authorized_keys
```

### 4. 创建 *git* 仓库

```bash
cd /srv
sudo mkdir git            # 创建 git 目录
sudo chown git:git git    # 修改目录属主
mkdir project.git         # 创建 git 仓库
cd project.git
git init --bare           # 初始化 git 仓库
```

*git* 服务搭建完成，用户可以关联该仓库进行克隆、提交、拉取等操作。关联仓库命令

```bash
git remote add origin git@gitserver:/srv/git/project.git
```

### 5. 禁止使用 *ssh* 通过 *git* 账号登录服务器

查看 */etc/shells* 文件里是否有 *git-shell*

```bash
cat /etc/shells
```

如果已经存在，不需要进行下面的操作，如果不存在执行

```bash
which git-shell
```

将上面的输出保存到 */etc/shells* 中

```bash
sudo -e /etc/shells
```

最后执行

```bash
sudo chsh git -s $(which git-shell)
```

禁用端口转发登录，在 *.ssh/authorized_keys* 文件每个 *ssh-rsa* 之前添加
`no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty `,
例如：

```bash
no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEwENNMomTboYI+LJieaAY16qiXiH3wuvENhBG...
```
