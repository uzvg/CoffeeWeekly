---
title: $*,$@,傻傻分不清楚？
summary: bash、zsh脚本中，$*变量跟$@变量的区别
description: bash、zsh脚本中，$*变量跟$@变量的区别
categories:
  - Shell
tags:
  - bash
  - zsh
  - Shell
date: 2024-03-31T15:24:42.140Z
lastmod: 2024-03-31T15:24:42.140Z
draft: false
showComments: true
series: []
series_order: 0
seriesOpened: false
slug: the-difference-of -variable
type: posts
keywords:
  - bash
  - 参数
---

刚开始学习Linux Shell的时候，有两个变量总是让人傻傻分不清楚，它们就是`$@`跟`$*`，还有`"$@"`跟`"$*"`。

## 结论：

先说结论，网上的解释一大堆，但其实可以用两句话来概括：

- 不加引号的时候，`$@`跟`$*`的作用一致
- 加引号的时候，`"$@"`的作用仍然跟`$@`保持一致，没发生变化，而`"$*"`会将所有参数视为单个完整参数。

## 示例：

新建如下脚本文件`test.sh`：
```bash
#!/usr/bin/bash
count=1
for name in $*
do
  echo "Parameter #$count = $name"
   (( count++ ))
done
echo "=============================="
count=1
for name in $@
do
  echo "Parameter #$count = $name"
  (( count = count+1 ))
done
```

使用`chmod+x test.sh`使其具有执行权限，然后：

```bash
./test.sh zhangsan lisi wangmazi ergouzi
```

可以看到如下结果：

```bash
❯❯❯ ./test2.sh zhangsan lisi wangmazi ergouzi
Parameter #1 = zhangsan
Parameter #2 = lisi
Parameter #3 = wangmazi
Parameter #4 = ergouzi
==============================
Parameter #1 = zhangsan
Parameter #2 = lisi
Parameter #3 = wangmazi
Parameter #4 = ergouzi
```

可以看到，在没有引号的情况下，$@跟$*的作用是一致的，都是将传入的参数单个区分对待。

再然后分将其中的`$@`跟`$*`两边加上引号，如下：

```bash
#!/usr/bin/bash
count=1
for name in "$*"
do
  echo "Parameter #$count = $name"
   (( count++ ))
done

echo "=============================="
count=1
for name in "$@"
do
  echo "Parameter #$count = $na**me"
  (( count = count+1 ))
done
```

再次执行，可以看到如下结果：

```bash
❯❯❯ ./test2.sh zhangsan lisi wangmazi ergouzi
Parameter #1 = zhangsan lisi wangmazi ergouzi
==============================
Parameter #1 = zhangsan
Parameter #2 = lisi
Parameter #3 = wangmazi
Parameter #4 = ergouzi
```

可以看到，与没有引号的`$*`不同，`"$*"`会将传入的所有参数，当成单个整体的参数来对待。