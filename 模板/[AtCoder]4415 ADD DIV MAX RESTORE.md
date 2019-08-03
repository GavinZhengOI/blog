# [AtCoder]4415 ADD DIV MAX RESTORE

## 题面

[AtCoder4415](https://jag2018summer-day2.contest.atcoder.jp/tasks/jag2018summer_day2_i?lang=en)

[vjudge-AtCoder4415](https://vjudge.net/problem/AtCoder-4415)

## 思路

先考虑如何合并线段树操作的tag。我们可以把线段树中的权值按照分数的方式存储，$ADD$操作的方式是给分子加上$add\_val*divisor$，$DIV$操作的方式是给分母乘上$div\_val$。



 这样我们发现有一个锅，那就是除数如果多次操作后会变得很大。我们只需要判一下，当分母超过分子时，向下取整结果就是0，就不需要再乘了。