---
layout: post
title: "最长无重复子串"
categories: Algorithm
tags: 算法 字符串
---

* content
{:toc}

>题目：对于一个字符串,请设计一个高效算法，找到字符串的最长无重复字符的子串长度。给定一个字符串A及它的长度n，请返回它的最长无重复字符子串长度。保证A中字符全部为小写英文字符，且长度小于等于500。


考虑用Hash表来实现，通过判断以每一个字符结尾为的最长子串的长度，并比较出最长子串：

```java
    /* 最长无重复子串 */
    public class DistinctSubstring {
        public int longestSubstring(String A, int n) {
            char[] s = A.toCharArray();
            
            int[] map = new int[256];
            
            //构造Hash表
            for (int i = 0; i < 256; i++) {
                map[i] = -1;
            }
            
            int len = 0; //
            int pre = -1; //每个字符上次出现的位置
            int cur = 0; //当前字符为止的最长无重复字符串的长度
            
            for (int i = 0; i < n; i++) {
                pre = Math.max(pre, map[s[i]]);
                cur = i - pre;
                len = Math.max(len, cur);
                map[s[i]] = i;
            }
            
            return len;
        }
    }
```



