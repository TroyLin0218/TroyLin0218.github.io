---

layout: post
title: 【每日一题】3月3日:合并排序的数组
categories: LeetCode_Daily
description: LeetCode
keywords: LeetCode,刷题

---

### 面试题 10.01. 合并排序的数组

LeetCode每日一题,3月3日合并排序的数组:

#### 题目:

> 给定两个排序后的数组 A 和 B，其中 A 的末端有足够的缓冲空间容纳 B。 编写一个方法，将 B 合并入 A 并排序。
>
> 初始化 A 和 B 的元素数量分别为 m 和 n。
>
> 给定两个排序后的数组 A 和 B，其中 A 的末端有足够的缓冲空间容纳 B。 编写一个方法，将 B 合并入 A 并排序。
>
> 初始化 A 和 B 的元素数量分别为 m 和 n。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/sorted-merge-lcci
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
>
> ```java
> 输入:
> A = [1,2,3,0,0,0], m = 3
> B = [2,5,6],       n = 3
> 
> 输出: 
> [1,2,2,3,5,6]
> ```

#### 题解:

按照给的例子,我理解的`排序后`的是指升序,并且A足够容纳B,因此想到解法是:

由于是升序的,直接取A和B的最大值(也就是A[m-1]和B[n-1])比较,然后把较大值放到A数组的A[m+n-1]位置上.可以预测这时候的A数组仍然是有序的.

此外需要考虑一些其他情况:假设A数组的某写元素比B数组的元素大,那么上面的操作会把A数组的所有所有比B数组元素大的元素复制到从A[m]到A[m+n-1]位置的某一个上,此时并没有把B数组的全部元素合并到A数组里面,这时候再遍历一次B数组,把B数组剩下的元素放到A数组从A[0]到A[n-1]的位置.

#### 代码:

代码如下:


```java
public class No0303_MergeArray {

    public static int[] merge(int[] A, int m, int[] B, int n) {
        int index = m + n - 1;
        int i = m - 1;
        int j = n - 1;
        //A[m-1]和B[n-1]比较,把较大值放到A数组的A[m+n-1]位置上
        while (i >= 0 && j >= 0) {
            if (A[i] < B[j]) {
                A[index] = B[j];
                //谁被赋值了谁的指针就往前移动一个
                j--;
            } else {
                A[index] = A[i];
                i--;
            }
            //位置指针往前移动一个位置
            index--;
        }
        //处理B数组剩下的元素
        while(j>=0) {
            A[index]=B[j];
            index--;
            j--;
        }
        return A;
    }

    //测试
    public static void main(String[] args) {
        int[] a = new int[]{2, 5, 8, 0, 0, 0};
        int[] b = new int[]{1, 6, 9};
        System.out.println(Arrays.toString(merge(a, 3, b, 3)));
        //输出结果 [1, 2, 5, 6, 8, 9]
    }
}
```
