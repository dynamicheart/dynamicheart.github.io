---
title: Proof of The algorithm In Leetcode#135 Candy
tags:
  - Leetcode
  - proof
  - algorithm
date: 2017-01-19 16:13:16
---

## Problem Description

> - There are N children standing in a line. Each child is assigned a rating value.
> - You are giving candies to these children subjected to the following requirements:
>   - Each child must have at least one candy.
>   - Children with a higher rating get more candies than their neighbors.
> - What is the minimum candies you must give?

From: [https://leetcode.com/problems/candy/](https://leetcode.com/problems/candy/)

<!--more-->

## Solution
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
## Proof of Correctness

Every kid will have at least one candy, we give every kid one candy at the beginning. Assumed that kids stand in aline, we scan from left to right. If 
a kid has a higher rating than his left kid, he can get one more candy than his left kid. Then we do this again from right to left except that we compare a kid with his right kid.

After that, you can not find an exception that a kid with higher rating has
less candies or same candies than any of his neighbours.

At this point, the algorithm is definitely correct.

## Proof of Optimum

- We use histogram to show how many candies each child gets. In general, the allocation of candies will form one or more peak patterns. 
- Each peak is independent. Neighbors do not affect each other.
- If we can prove that the allocation represented by each peak is optimal, the overall allocation is optimal.
- It's easy to prove that the structure of each peak is optimal.

{% asset_img peaks.png Peaks %}

## Another Solution

It's easy to get an improved algorithm from the proof above:
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

Reference: https://discuss.leetcode.com/topic/8208/one-pass-constant-space-java-solution

## Useful Resources
PDF：{% asset_link candy.pdf candy.pdf %}
