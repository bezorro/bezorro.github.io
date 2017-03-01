---
layout: post
title:  "Leetcode: Regular Expression Matching"
categories: [leetcode]
comments: true
modified: 2017-03-01 14:34:03
---
## Question

>Implement regular expression matching with support for '.' and '*'.
>
>      '.' Matches any single character.
>      '*' Matches zero or more of the preceding element.
>
>      The matching should cover the entire input string (not partial).
>
>      The function prototype should be:
>      bool isMatch(const char *s, const char *p)
>
>      Some examples:
>      isMatch("aa","a") → false
>      isMatch("aa","aa") → true
>      isMatch("aaa","aa") → false
>      isMatch("aa", "a*") → true
>      isMatch("aa", ".*") → true
>      isMatch("ab", ".*") → true
>      isMatch("aab", "c*a*b") → true
<!--more-->

## Dynamic Programming
f(i,j) represents whether the first i letters of string s can be matched by the first j letters of regular expression p(0 means no and 1 means yes).
Therefore, the recursion formula can be written as:  
&emsp;&emsp;(1) if `p[i] == '.'`, f(i,j) = f(i-1,j-1)  
&emsp;&emsp;(2) if `p[i] == '*'`, 
            f(i,j) = f(i,j-2) or { `s[i] match p[j-1]` and f(i-1,j) }  
&emsp;&emsp;(3) if `p[i] != '.'` and `p[i] != '*'`,   
&emsp;&emsp;&emsp;&emsp;f(i,j) = f(i-1,j-1), when `s[i] == p[j]`  
&emsp;&emsp;&emsp;&emsp;f(i,j) = 0, when `s[i] != p[j]`  

{% highlight c %}
bool isMatch(char * s,char * p)
{
    int lens = strlen(s--), lenp = strlen(p--);
    bool f[lens + 1][lenp + 1];
    
    memset(f,0,sizeof(f));
    f[0][0] = 1;
    for(int j = 1;j <= lenp;++j)
        f[0][j] = p[j] == '*' && f[0][j - 2];//init

    for(int j = 1;j <= lenp;++j)
    for(int i = 1;i <= lens;++i)
        if(p[j] == '*')
            f[i][j] = f[i][j - 2] || (s[i] == p[j - 1] || p[j - 1] == '.') && f[i - 1][j];
        else if(s[i] == p[j] || p[j] == '.') 
            f[i][j] = f[i-1][j-1];
    return f[lens][lenp];
}
{% endhighlight %}
- Time Complexity: O(n^2)
- Space Complexity: O(n^2)

## Finding law
By observing the samples above, we can find the law and solve it directly
{% highlight c %}
bool isMatch(char *s, char *p)
{
    if (*s==0 && *p==0) return true;
    if (*p==0 || (*s==0 && *(p+1)!='*') ) return false;

    if (*(p + 1) != '*')
        return (*p == *s || *p == '.') ? isMatch(s + 1, p + 1) : false;

    do if (isMatch(s, p + 2)) return true;
    while (*s && (*(s++) == *p || *p == '.'));
    return false;
}
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)
