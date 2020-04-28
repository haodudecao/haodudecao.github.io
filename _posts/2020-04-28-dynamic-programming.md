---
layout: post
title: "动态规划"
subtitle: "Dynamic Programming"
author: "HaoDu"
catalog: true
tags:
  - Algorithm
---
# 题目
leetcode 0005 题
给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。
> 回文:正读反读一样，例如：上海自来水来自海上

#### 示例 1:

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```
#### 示例 2:

```
输入: "cbbd"
输出: "bb"
```
#### 示例 3:

```
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

# 思路：
### 1.暴力解法 列举所有子串,检测是否是回文
          
//看到这道题就想到了按照冒泡排序类似算法,两个 for循环找出,时间复杂度 O(n²);

```php
function longestPalindrome($s)
{
    $length = strlen($s);
    $count = 0;
    $longestLen = 0;
    $longestSubstr = '';
    for ($i = 0; $i < $length; $i++) {
        if ($longestLen > $length - $i)
            break;//若不加这一行,超长回文串会超时(有效避免无效循环)
        for ($j = 1; $j <= $length - $i; $j++) {
            $count++;
            $substr = substr($s, $i, $j);
            //检测子串是否是回文
            if ($substr == strrev($substr)) {
                $substrLen = strlen($substr);
                if ($substrLen > $longestLen) {
                    $longestLen = $substrLen;
                    $longestSubstr = $substr;
                }
            }
        }
    }
    return $longestSubstr;
}
```
### 2.动态规划
按照定义，动态规划是把一个大问题拆解成一堆小问题，这个本身没啥问题，但是我觉得的这个不是动态规划的核心思想 取决于该问题是否能用动态规划解决的是这些”小问题“会不会被被重复调用。

动态规划：就是用空间的代价来争取时间，将中间结果保存下来，后面循环使用供，减少重复计算次数。

#### 2.1 基本思路是将字符串翻转,然后求两个串的最长公共子串
#### 2.2 求最长公共子串的过程用到动态规划算法，
>矩阵思想：定义一个矩阵，宽和高分别为两个字符串的长度。从上到下、从左到右逐个扫描，每次扫描要比较矩阵中每个点对应的行列字符是否相等， 相等的话等于左上邻+1，不相等则置为0。
                            
时间复杂度：矩阵中的长和宽的乘积即为复杂度。复杂度为O(mn)。***(并不是两个 for 循环，时间复杂度就是平方级)***

以 `hello` 和 `loop` 两个词为例

^|h|e|l|l|o
---|---|---|---|---|---
l|0|0|1|0|0
o|0|0|0|1|1
o|0|0|0|1|2
p|0|0|0|0|0

```php


```


### 3.中心扩展

### 4.马拉车...
