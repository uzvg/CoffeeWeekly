---
title: 我的regex101答案
summary: regex101 quiz 1-8,12题的答案
description: regex101习题答案
categories: []
tags:
    - regex
    - regex101
    - 正则表达式
date: 2024-03-25T12:23:01.621Z
lastmod: 2024-03-25T16:32:24.184Z
draft: false
showComments: true
series:
    - 正则表达式
series_order: 2
seriesOpened: true
slug: my-anwser-of-regex101-quiz
type: posts
keywords:
    - regex101
    - 答案
---

## 前言

做[regex101](https://regex101.com/quiz)练习题的时候，发现答案不好找，搜了一圈，社区的态度是，为了防止大家抄袭答案，所以不公布答案为好。

这样做，好处当然有，但做不出来的题，找不到正确答案就很难受，更不要说借鉴那些表达式最短的答案了。

我自己总共做了前13道题，其中1-8道以及12道，答案通过，9-11、还有13道，答案未能通过。这里也是提醒大家，作者的答案肯定不是最优答案，只是给大家提供借鉴，如果大家有更好的答案，或者有其他我没解决的题的答案，欢迎大家评论区流言交流。

## 第一题——匹配`word`字符模式

题目：Check if a string contains the word `word` in it (case insensitive). If you have no idea, I guess you could try `/word/`.

答案：`/\bword\b/i`
    
    `\b`表示单词边界，表示单词跟空格间的位置。

## 第二题——将小写字母`i`替换为`I`

题目：Use substitution to replace every occurrence of the word i with the word I (uppercase, I as in me). E.g.: `i'm replacing it. am i not?` -> `I'm replacing it. am I not?`.

A regex match is replaced with the text in the Substitution field when using substitution.

答案：将`/\bi\b/g`替换为`I`。

## 第三题——找到大写的辅音字母

题目：With regex you can count the number of matches. Can you make it return the number of uppercase consonants (B,C,D,F,..,X,Y,Z) in a given string? E.g.: it should return 3 with the text ABcDeFO!. Note: Only ASCII. We consider Y to be a consonant!

Example: the regex /./g will return 3 when run against the string abc.

答案：`/[BCDFGHJ-NP-TV-Z]/g`

## 第四题——匹配整数

题目：Count the number of integers in a given string. Integers are, for example: 1, 2, 65, 2579, etc.

答案：`/\d+/g`

    `\d`代表所有的阿拉伯数字字符，另外，`\D`代表所有的非阿拉伯数字字符。

## 第五题——匹配等于或者超过4个的空格字符

题目：Find all occurrences of 4 or more whitespace characters in a row throughout the string.

答案：`/\s{4,}/g`

    `\s`代表任意空白字符，`{4,}`表示至少前面的`\s`至少出现4次。

## 第六题——去除重复字符

题目：Oh no! It seems my friends spilled beer all over my keyboard last night and my keys are super sticky now. Some of the time whennn I press a key, I get two duplicates.

Can you ppplease help me fix thhhis?

答案：将`/(\s|\S)\1{2}/g`替换为`$1`

    意思是他的键盘出了问题，按一次键帽，会多出两个连续的字符，比如`ppplease`和`thhhis`，所以思路就是先找到重复3次的任意字符，不管是空白字符还是非空白字符，然后删除掉重复的两个字符就好。
    
    `\s`代表任意空白字符，而`\S`代表任意非空白字符，所以先用`(\s|\S)`就是将所有字符分组，不管是空白字符还是非空白字符，同时将其捕获为分组1；`\1{2}`是说指前面捕获到的1号分组又连续出现两次，这种情况下，就将该连续出现三次的字符替换为分组1。

## 第七题

题目：
## 第八题

题目：Strip all HTML tags from a string. HTML tags are enclosed in < and >.

The regex will be applied on a line-by-line basis, meaning partial tags will need to be handled by the regex. Don't worry about opening or closing tags; we just want to get rid of them all.

Note: This task is meant to be a learning exercise, and not necessarily the best way to parse HTML.

答案：将`/.*>|<[^<>]*>?/g`替换为空。

## 第十二题

题目：Could you help me validate my input and only match positive integers between the range of 0 and 100?

There can be several numbers in a string which I would want to retrieve.

Try out these example strings:

`Sam has 200 apples. He gives Todd 20 and Mary 125.
The weather is -5 C today, but will be +5 C tomorrow.`

答案：`/\b(?:(?<!-)\d|(?<!-)[1-9]\d|(?<!-)100)\b/gm`