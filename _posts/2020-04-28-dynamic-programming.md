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

### 示例 1:

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```
### 示例 2:

```
输入: "cbbd"
输出: "bb"
```
### 示例 3:

```
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

# 思路：
### 1暴力解法 列举所有子串,检测是否是回文
          
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

如何移动？

我们只要把队列的左边的元素移出就行了，直到满足题目要求！

一直维持这样的队列，找出队列出现最长的长度时候，求出解！

时间复杂度：O(n);

```php


```


### 2.1 直接用字符串好像更快些
```php
//一开始就陷入误区...用了数组
function lengthOfLongestSubstring($s) {
    $len = strlen($s);
    $max = 0;
    $temp = '';
    for($i = 0;$i < $len;$i++){
        $live = strpos($temp,$s[$i]);
        if($live !== false){
            $temp .=$s[$i];
            $temp = substr($temp,$live+1);
        }else{
            $temp .=$s[$i];
        }
        $max = max(strlen($temp),$max);
    }
    return $max;
}
```
