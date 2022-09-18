---
title: Wget 命令
author: hypersport
date: 2022-01-31 22:01:31 +0800
categories: [Linux]
tags: [wget]
render_with_liquid: false
---

### 命令格式

``wget <选项> url``

### 常用命令选项

| 命令选项             | 解释说明                                       |
|:--------------------|:---------------------------------------------|
| -c                  | 继续执行上次终端的任务                           |
| -i \<文件\>          | 从指定文件获取要下载的 URL 地址                   |
| -r                  | 递归下载                                       |
| -nc                 | 文件存在时，下载文件不覆盖原有文件                  |
| -nv                 | 下载时只显示更新和出错信息，不显示指令的详细执行过程   |
| -b                  | 以后台方式运行                                  |
| -q                  | 不显示指令执行过程                               |
| -O                  | 重命名下载文件                                  |
| \-\-reject=jpg      | 过滤掉 jpg 文件，不下载 jpg                      |
| \-\-limit-rate 512k | 限速最高512k                                   |
| -A \<后缀名\>        | 指定要下载文件的后缀名，多个后缀名之间使用逗号进行分割 |
| -e \<指令\>          | 作为文件 ".wgetrc" 中的一部分执行指定的指令          |
| -h                  | 显示帮助信息                                    |
| -L                  | 仅顺着关联的连接                                 |
| -nh                 | 不查询主机名称                                  |
| -v                  | 显示详细执行过程                                 |

### 常用命令举例

1. 将下载文件重命名为 hello.zip

    > wget -O hello.zip http://www.xxx.com/download.aspx?id=9468

     wget 默认保存文件名是最后一个 / 后的部分，上面的连接如果不使用 -O 重命名的话，下载的文件就会保存为 download.aspx?id=9468

2. 伪装代理下载

    > wget \-\-user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" http://pythonista.cn/testwget.zip

    有些网站能通过根据判断代理名称不是浏览器而拒绝下载请求，可以使用 --user-agent 伪装

3. 镜像网站

    > wget \-\-mirror -p \-\-convert-links -P /home http://pythonista.cn

    说明：

    | \-\-miror         | 开户镜像下载                      |
    | -p                | 下载所有为了html页面显示正常的文件   |
    | \-\-convert-links | 下载后，转换成本地的链接            |
    | -P home           |保存所有文件和目录到本地 /home 目录中 |

4. 下载指定的文件格式

    > wget -r -A.pdf url

    递归下载所有的 pdf 文件

5. 下载信息不显示在终端而是在一个日志文件

    > wget -o download.log URL

6. 限制总下载大小

    >wget -Q5m -i filelist.txt

    当想要下载的文件超过5M而退出下载时使用。注意：这个参数对单个文件下载不起作用，只能递归下载时才有效
