---
title: Find 命令
author: hypersport
date: 2022-03-03 22:03:03 +0800
toc: false
categories: [Linux]
tags: [find]
render_with_liquid: false
---

### 常用命令

1. 直接使用 `find path` 命令, 可以列出该 `path` 下的所有文件及文件夹, 相当于 `find path -print`, 以换行符为分割, 如果使用 `-print0` 以 `"\0"` 作为分隔符

2. 根据文件名搜索

    > `find path -name "filename"`

    其中 `filename` 中可以使用正则表达式, 如: `find /home -name "*.py"`, 如果是 `-iname` 搜索时会忽略大小写, 想要查找多个条件中的一个, 使用 `OR(-o)` 操作, 如: `find /home \( -name "*.py" -o -name "*.rb" \)`, 注意 **\( 的后面和 \)** 的前面要有空格

3. 根据路径进行搜索

    > `find path -path "path"`

    如: `find /home -path "*/test/*"`

4. 根据正则表达式进行搜索

    > `find path -regex "regex"`

    如: `find /home -regex ".*\(\.py\|\.sh\)$"`, 如果是 `-iregex` 搜索时会忽略大小写

5. 否定搜索, 使用 `!` 操作

    > `find /home ! -name "*.py"`

6. 指定搜索时的目录深度

    > `find path -maxpath n` 或者 `find path -minpath n`

    分别指定搜索时的最大和最小深度, `-maxpath` 和 `-minpath` 尽量放在其他搜索条件的前面, 可以提高搜索效率, 如: `find /home -maxpath 3 -name "*.py"`

7. 根据文件类型搜索

    > `find path -type type`

    如: `find /home -type f`, 其中

    | f | 普通文件 |
    | l | 符号链接 |
    | d | 目录    |
    | c | 字符设备 |
    | b | 块设备  |
    | s | 套接字  |
    | p | FIFO   |

8. 根据时间搜索

    | -atime/-amin | 最后一次访问的时间, 分别以天和分钟为单位                        |
    | -mtime/-mmin | 内容最后一次被修改的时间, 分别以天和分钟为单位                   |
    | -ctime/-cmin | 文件元数据(如权限\所有权等)最后一次改变的时间, 分别以天和分钟为单位 |

    > 举例说明

    | `find /home -atime 7`                | 恰好在7天前访问过            |
    | `find /home -atime +7`               | 访问超过7天的               |
    | `find /home -atime -7`               | 7天之内访问的               |
    | `find /home -type f -newer file.txt` | 在 file.txt 修改后修改的文件 |

9. 根据文件大小搜索

    | b | 块( 512 字节) |
    | c | 字节          |
    | w | 字( 2 字节)   |
    | k | 1024 字节     |
    | M | 1024k        |
    | G | 1024M        |

    > 举例说明

    | `find /home -size 200k`  | 等于 200k 的文件 |
    | `find /home -size +200k` | 大于 200k 的文件 |
    | `find /home -size -200k` | 小于 200k 的文件 |

10. 根据权限搜索

    > `find path -perm xxx`

    其中 `xxx` 为权限数字, 如: `find /home -perm 644`

11. 根据所有权搜索

    > `find path -user USER`

    其中 `USER` 为用户名, 如: `find /home -user root`

12. 删除匹配的文件, 使用 `-delete` 操作

    > `find /home -name "*.py" -delete`

13. 对匹配到的文件进行操作, 使用 -exec 操作, 注意格式

    > `find /home -name "*.py" -exec rm {} \;` 删除以 `.py` 结尾的文件, 注意***最后是空格和斜线分号***

    > `find /home -name "*.c" -exec cat {} \;>all_c_file.txt`, 将 `/home` 下所有以 `.c` 结尾的文件拼接后写入 `all_c_file_txt` 文件中

    > `-exec` 后面只能跟一个命令, 如果要执行多个命令, 可以把这些命令写入一个脚本中, 在 -exec 后执行该脚本即可

14. 跳过特定的路径, 使用 -prune 操作

    > `find /home \( -name ".git" -prune \) -o \( -type f -print \)`, 会跳过 `.git` 目录进行匹配
