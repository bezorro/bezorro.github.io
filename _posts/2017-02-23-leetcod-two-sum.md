---
layout: post
title:  "Leetcode: Two Sum"
date:   2017-01-23 09:58:03
categories: [leetcode]
comments: true
---
<!--more-->
## Question

>Given an array of integers, return indices of the two numbers such that they add up to a specific target.
>You may assume that each input would have exactly one solution.
>
> ### Example:
>
>     Given nums = [2, 7, 11, 15], target = 9,
>     Because nums[0] + nums[1] = 2 + 7 = 9,
>     return [0, 1].
>


## Brute Force
A simple brute force by Looping through each element x and find if there is another value that equals to target - x.
{% highlight cpp %}
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for(int i = 0; i < nums.size(); i++)
            for(int j = i + 1; j < nums.size(); j++)
                if(nums[i] + nums[j] == target)
                    return vector<int>{i,j};
    }
};
{% endhighlight %}
- Time Complexity: O(n^2)
- Space Complexity: O(1)

## Binary Search
It is similar to brute force, but replace the linear search with a binary search.
{% highlight cpp %}
class Solution {
    struct node
    {
        int val;
        int idx;
        node(const int v = 0,const int id = 0):idx(id),val(v) {};
        bool operator < (const node & rhs) const
        {
            return val < rhs.val;
        }
    };
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        const int len = nums.size();
        vector< node > a(len);
        for(int i = 0; i < len; ++i)
            a[i] = node(nums[i],i);
        
        sort(a.begin(),a.end());
        
        for(auto cur = a.begin(); cur != a.end(); ++cur)
        {
            auto it = lower_bound(cur + 1, a.end(), node(target - cur -> val));
            if(it != a.end() && it -> val == target - cur -> val)
                return vector<int> {cur -> idx,it -> idx};
        }
    }
};
{% endhighlight %}
- Time Complexity: O(nlog(n))
- Space Complexity: O(n)

## Hash Table
It is similar to brute force, but replace the linear search with a hash table.
{% highlight cpp %}
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mp;
        for(int i = 0; i < nums.size(); ++i)
        {
            const int tmp = target - nums[i];
            if(mp.count(tmp))
                return vector<int> {mp[tmp], i};
            mp[nums[i]] = i;
        }
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(n)
