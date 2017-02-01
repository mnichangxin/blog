---
layout: post
title: "句子的逆序"
categories: Algorithm
tags: 算法 字符串
---

* content
{:toc}

>题目：
对于一个字符串，请设计一个算法，只在字符串的单词间做逆序调整，也就是说，字符串由一些由空格分隔的部分组成，你需要将这些部分逆序。给定一个原字符串A和他的长度，请返回逆序后的字符串。



```java
    /* 句子的逆序 */
    public class Reverse {
        public char[] reverse(char[] str, int start, int end) {
            while (start < end) {
                char temp = str[start];
                str[start] = str[end];
                str[end] = temp;
                start++;
                end--;
            }
            
            return str;
        }
        
        public String reverseSentence(String A, int n) {
            char[] str = A.toCharArray();
            
            //整体逆序
            str = reverse(str, 0, n - 1);
            
            //寻找单个单词，并逆序
            int i = 0;
            
            for (int j = 0; j < n; j++) {
                if (str[j] == ' ') {
                    str = reverse(str, i, j - 1);
                    i = j + 1;
                }
                //最后单独判断
                if (j == n - 1) {
                    str = reverse(str, i, j);
                }
            }
            
            //输出
            String s = "";
            
            for (int k = 0; k < n; k++) {
                s += str[k];
            }
            
            return s;
        }
    }
```