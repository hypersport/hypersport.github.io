---
title: Ruby on Rails 自定义安全凭证
author: hypersport
date: 2022-10-20 10:20:30 +0800
categories: [Ruby]
tags: [Ruby on Rails]
render_with_liquid: false
---

*Rails* 将保密信息保存在 `config/credentials.yml.enc` 文件中，该文件已被加密，因此无法直接编辑。

*Rails* 使用 `config/master.key` 或者通过环境变量 `ENV["RAILS_MASTER_KEY"]` 来加密安全凭证文件。由于凭证文件是加密的，可以放到版本控制中，但是要注意，***必须保证主秘钥是安全的*** 。

默认情况下，安全凭据文件包含应用程序的 *secret_key_base* 。当然它还可用于存储其他保密信息，例如外部 *API* 的访问密钥等。

### 编辑安全凭证

运行下面的命令

```bash
bin/railscredentials:edit
```

如果安全凭据不存在，此命令将创建该文件。另外，如果未定义主密钥，此命令将创建 `config/master.key` 。

### 使用安全凭证

安全凭证中保存的保密信息可通过 ` Rails.application.credentials` 访问。例如，使用以下信息加密的  `config/credentials.yml.enc`

```ruby
secret_key_base: 3b7cd72...
some_api_key: SOMEKEY
system:
  access_key_id: 1234AB
```

*Rails.application.credentials.some_api_key* 返回 *SOMEKEY* 。 

*Rails.application.credentials.system.access_key_id* 返回 *1234AB* 。

下面的配置可以在信息为空时抛出异常

```ruby
# 当 some_api_key 为空时
Rails.application.credentials.some_api_key!
# => KeyError: :some_api_key is blank
```
