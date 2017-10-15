---
title: 'Leetcode-#135Candy算法正确性的证明'
tags:
  - Leetcode
  - proof
  - algorithm
date: 2017-01-19 16:13:16
---

## 问题描述

- There are N children standing in a line. Each child is assigned a rating value.
- You are giving candies to these children subjected to the following requirements:
  - Each child must have at least one candy.
  - Children with a higher rating get more candies than their neighbors.
- What is the minimum candies you must give?

来源：[https://leetcode.com/problems/candy/](https://leetcode.com/problems/candy/)

<!--more-->

## 代码

每个小孩都会至少拿到一颗糖，所以将每个小孩应得得糖果数初始化为1，之后先从左往右扫一遍，再从右左扫一遍，使得有更高分数的小孩，会比邻近的小孩拿到更多的糖果，算法的正确性是肯定的。

```cpp
 class Solution{
 public:
     int candy(vector<int>& ratings){
         vector<int> portion(ratings.size(),1);

         for(unsigned i = 1;i<ratings.size();i++){
             if((ratings[i]>ratings[i-1])){
                 portion[i] = portion[i-1] + 1;
             }
         }

         int total = 0;
         for(unsigned i = ratings.size() - 1 ; i > 0;i--){
             if((ratings[i-1]>ratings[i])&&portion[i-1]<=portion[i]){
                 portion[i-1] = portion[i] + 1;
             }
             total += portion[i];
         }
         total += portion[0];

         return total;
     }
 };
```
## 最优解证明

- 一般的情况，用柱状图的高度代表每个孩子获得的糖果数量多少，糖果的分配会呈现多个峰形，也有只升不降以及只降不升的峰，我们统称这些形状都为峰。
- 每一座峰都是独立的，相邻的峰之间互不影响。
- 如果能够证明每座峰所代表的糖果分配是最优的，那么总的糖果分配便是最优的。
- 很容易证明，每一座峰的结构都是最优的。

{% asset_img peaks.png Peaks %}

## 另一个算法

由上面的证明很容易得到下面一个改进的算法：
```java
public class Solution {
    public int candy(int[] ratings) {
        if (ratings == null || ratings.length == 0) return 0;
        int total = 1, prev = 1, countDown = 0;
        for (int i = 1; i < ratings.length; i++) {
            if (ratings[i] >= ratings[i-1]) {
                if (countDown > 0) {
                    total += countDown*(countDown+1)/2; // arithmetic progression
                    if (countDown >= prev) total += countDown - prev + 1;
                    countDown = 0;
                    prev = 1;
                }
                prev = ratings[i] == ratings[i-1] ? 1 : prev+1;
                total += prev;
            } else countDown++;
        }
        if (countDown > 0) { // if we were descending at the end
            total += countDown*(countDown+1)/2;
            if (countDown >= prev) total += countDown - prev + 1;
        }
        return total;
    }
}
```

来源：https://discuss.leetcode.com/topic/8208/one-pass-constant-space-java-solution

## 其它资料
PDF：{% asset_link candy.pdf candy.pdf %}
