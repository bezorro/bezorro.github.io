---
layout: post
title:  "Leetcode: Ugly Number"
categories: [leetcode]
comments: true
modified: 2017-03-01 18:07:03
---
## Question

>Write a program to check whether a given number is an ugly number.

>Ugly numbers are positive numbers whose prime factors only include 2, 3, 5. For example, 6, 8 are ugly while 14 is not ugly since it includes another prime factor 7.

>Note that 1 is typically treated as an ugly number. 
<!--more-->

## Solution
{% highlight cpp %}
class Solution {
public:
    bool isUgly(int num) {
    if(num<=0) return 0;
       for(;num%2==0;num/=2);
       for(;num%3==0;num/=3);
       for(;num%5==0;num/=5);
        return num==1;
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)