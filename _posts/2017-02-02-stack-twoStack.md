---
layout: post
title: "Java实现双栈队列"
categories: Algorithm
tags: 算法 栈 队列
---

* content
{:toc}

>编写一个类,只能用两个栈结构实现队列,支持队列的基本操作(push，pop)。
给定一个操作序列ope及它的长度n，其中元素为正数代表push操作，为0代表pop操作，保证操作序列合法且一定含pop操作，请返回pop的结果序列。
>>测试样例：
[1,2,3,0,4,0],6
返回：[1,2]




Java虽然有队列和栈相应的API，但实现起来难度并不大。关键点在于：

1. 压入栈向压出栈倒入的时候，要保证全部倒入
2. 倒入时还要保证压出栈为空栈

下面是具体实现代码：

```java
    /* 双栈队列 */
    import java.util.Stack;

    class Queue {
        private Stack<Integer> pushStack = new Stack<Integer>();
        private Stack<Integer> popStack = new Stack<Integer>();
        
        public void push(int node) {
            pushStack.push(node);
        }
        
        public int pop() {
            if (!popStack.isEmpty()) {
                return popStack.pop(); 
            }
            
            while (!pushStack.isEmpty()) {
                popStack.push(pushStack.pop()); //pushStack倒入popStack，关键:保证pushStack为空！
            }
            
            return popStack.pop();
        }
    }

    public class TwoStack {
        public int[] twoStack(int[] ope, int n) {
            Queue q = new Queue();
            
            int j = 0;
            int k = 0;
            
            for (int i = 0; i < ope.length; i++) {
                if (ope[i] == 0) {
                    j++;
                }
            }
            
            int[] arr = new int[j];
            
            for (int i = 0; i < ope.length; i++) {
                if (ope[i] > 0) {
                    q.push(ope[i]);
                } else {
                    arr[k++] = q.pop();
                }
            }       
            
            return arr;
        }
    }
```

