---
layout: post
title: "小范围排序（非递归堆排序）"
categories: Algorithm
tags: 算法 排序
---

* content
{:toc}

题目是这样的：

>已知一个几乎有序的数组，几乎有序是指，如果把数组排好顺序的话，每个元素移动的距离可以不超过k，并且k相对于数组来说比较小。请选择一个合适的排序算法针对这个数据进行排序。给定一个int数组A，同时给定A的大小n和题意中的k，请返回排序后的数组。




堆排序的时间复杂度为 `O(nlogn)` ，但递归方式会增加其时间复杂度，故用非递归方式实现：

```java 
    /* 小范围排序 */
    import java.util.Arrays;

    public class ScaleSort {    
        //堆的调整，小顶堆
        public void heapAdjust(int[] A, int parent, int n) {
            int temp = A[parent]; //保存当前父结点
            int child = 2 * parent + 1; //获得左孩子
            
            while (child < n) {
                //如果右孩子结点值大于左孩子结点值，则选取右孩子结点
                if (child + 1 < n && A[child] > A[child + 1]) {
                    child++;
                }
                //如果父结点值大于孩子结点的值，则直接结束
                if (temp <= A[child]) {
                    break;
                }
                
                A[parent] = A[child]; //把孩子结点的值赋给父结点
                
                parent = child;
                child = 2 * parent + 1;
            }
            
            A[parent] = temp;
        }
        
        public int[] sortElement(int[] A, int n, int k) {
            if (n == 0 || n < k) {
                return A;
            }
            
            int[] heap = Arrays.copyOf(A, k);
            
            //对前k个元素建立初始堆
            for (int i = k / 2; i >= 0; i--) {
                heapAdjust(heap, i, k);
            }
            
            //对从k开始后的元素进行建立初始堆操作
            for (int i = k; i < n; i++) {
                A[i - k] = heap[0];
                heap[0] = A[i];
                heapAdjust(heap, 0, k);
            }
            
            //最后一个堆
            for (int i = n - k; i < n; i++) {
                A[i] = heap[0];
                
                int temp = heap[0];
                heap[0] = heap[k - 1];
                heap[k - 1] = temp;
                heapAdjust(heap, 0, --k);
            }
            
            return A;
        }
    }

```
