---
title: Ubuntu 使用 rbenv 安装任意版本 Ruby
author: hypersport
date: 2023-01-23 23:01:23 +0800
categories: [Linux]
tags: [Ruby]
toc: true
render_with_liquid: false
---

*Ubuntu* 使用 *apt* 包管理器安装的 *Ruby* 版本较老，可以使用 *rbenv* 安装最新或其他版本并管理 *Ruby* 版本。

### 1. 安装 *rbenv*

```bash
sudo apt install rbenv
```

### 2. 安装最新的 *ruby-build*

默认安装的 *ruby-build* 版本较老，导致只能安装老版本的 *Ruby*。

- 卸载默认的老版本 *ruby-build*

>```bash
>sudo apt remove ruby-build
>```

- 下载最新版本的 *ruby-build*

>```bash
>git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
>```

- 手动安装最新版本的 *ruby-build*

>```bash
>cd "$(rbenv root)"/plugins/ruby-build
>sudo ./install.sh
>```

### 3. 安装 *Ruby* 及常用命令

- 列出所有稳定版本

>```bash
>rbenv install -l
>```

- 列出所有版本

>```bash
>rbenv install -L
>```

- 安装指定版本

>```bash
>rbenv install 3.1.4
>```

- 指定系统版本

>```bash
>rbenv global 3.1.4
>```

- 指定当前目录版本

>```bash
>rbenv local 3.1.4
>```

- 撤销当前目录版本

>```bash
>rbenv local --unset
>```

- 安装 *gem* 后如果不起作用，可以手动执行

>```bash
>rbenv rehash
>```
