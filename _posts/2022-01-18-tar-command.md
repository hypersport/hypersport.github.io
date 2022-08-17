---
title: Tar 命令
author: hypersport
date: 2022-01-18 22:01:18 +0800
categories: [Linux]
tags: [tar]
render_with_liquid: false
---

### 常用命令选项

|命令选项                          |解释说明                               |
|--------------------------------|--------------------------------------|
|-c 或 --create                   |建立新的备份文件                        |
|-C <目录>                        |解压到指定目录                          |
|-x 或 --extract 或 --get         |从备份文件中还原文件                     |
|-t 或 --list                     |列出备份文件的内容                      |
|-z 或 --gzip 或 --ungzip         |通过 gzip 指令处理备份文件               |
|-f <备份文件> 或 --file=<备份文件>  |指定备份文件                           |
|-j                               |支持 bzip2 解压文件                    |
|-p 或 --same-permissions         |用原来的文件权限还原文件                 |
|-r                               |添加文件到已经压缩的文件                 |
|-A                               |将一个备份文件追加到另一个备份文件的后面    |
|-d                               |记录文件的差别                         |
|-v 或 --verbose                  |显示指令执行过程                        |
|-u                               |添加改变了和现有的文件到已经存在的压缩文件  |
|-k                               |保留原有文件不覆盖                      |
|-w                               |确认压缩文件的正确性                    |
|-P 或 --absolute-names           |文件名使用绝对名称，不移除文件名称前的“/”号 |
|-N <日期格式> 或 --newer=<日期时间> |只将较指定日期更新的文件保存到备份文件里    |
|--exclude=<范本样式>              |排除符合范本样式的文件                   |

### 常用命令举例

压　缩：tar -jcv -f filename.tar.bz2 要被压缩的文件或目录名称

解压缩：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录

查　询：tar -jtv -f filename.tar.bz2

只将tar内的部分文件解压出来： tar -zxvf test/log.tar.gz log.log
