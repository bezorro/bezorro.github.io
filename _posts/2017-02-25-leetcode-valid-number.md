---
layout: post
title:  "Leetcode: Valid Number"
categories: [leetcode]
comments: true
modified: 2017-01-25 21:46:03
---
## Question

>Validate if a given string is numeric.
>You may assume that each input would have exactly one solution.
>
> ### Some examples:
>
>     "0" => true
>     " 0.1 " => true
>     "abc" => false
>     "1 a" => false
>     "2e10" => true
>
<!--more-->

## Recursive Descent Parsing
By observing the samples above, we can draw an LL(1) grammar:  
&emsp;&emsp;Number => Spaces,RealNumber,Exponent,Spaces  
&emsp;&emsp;Exponent => Empty  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; => 'e',Integer  
&emsp;&emsp;Integer => '+',RInteger  
&emsp;&emsp;&emsp;&emsp;&emsp; => '-',RInteger  
&emsp;&emsp;&emsp;&emsp; &emsp;=> RInteger  
&emsp;&emsp;RInteger => '0',EInteger  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;...  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;=> '9',EInteger  
&emsp;&emsp;EInteger => Empty  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;=> '0',EInteger  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;...  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;=> '9',EInteger  
&emsp;&emsp;RealNumber => '+',RRealNumber  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; => '-',RRealNumber  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;=> RRealNumber  
&emsp;&emsp;Spaces => Empty  
&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; => '&ensp;',Spaces  
{% highlight cpp %}
class Solution {
public:
    char readChar(const string &s, const int cur)
    {
        if(cur >=0 && cur < s.size())
            return s[cur];
        else
            return -1;
    }

    int matchEInteger(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        if(isdigit(t))//[0~9][EInteger]
            return matchEInteger(s, cur + 1);
        else//[Empty]
            return cur;
    }

    int matchRInteger(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        if(isdigit(t))//[0~9][EInteger]
            return matchEInteger(s, cur + 1);
        else
            return -1;
    }

    int matchInteger(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        if(t == '+' || t == '-')//+[RInteger] | -[RInteger]
            return matchRInteger(s, cur + 1);
        else//[RInteger]
            return matchRInteger(s, cur);
    }

    int matchExp(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        if(t == 'e')//e[Integer]
            return matchInteger(s, cur + 1);
        else//[Empty]
            return cur;
    }
    int matchRReal(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        if(t == '.')//.[RInteger]
            return matchRInteger(s, cur + 1);

        int c = matchRInteger(s, cur);//[RInteger].[EInteger]
        t = readChar(s, c);
        if(t == '.')
            c = matchEInteger(s, c + 1);
        else
            c = -1;
        if(c != -1) return c;

        c = matchRInteger(s, cur);//[RInteger]
        if(c != -1) return c;

        return -1;
    }

    int matchReal(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        if(t == '+' || t == '-')//+[RReal] | -[RReal]
            return matchRReal(s, cur + 1);
        else//[RReal]
            return matchRReal(s, cur);
    }

    int matchNumber(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        int c = matchReal(s, cur);//[Real][Exp]
        c = matchExp(s, c);
        return c;
    }

    int matchSpaces(const string & s, const int cur)
    {
        if(cur == -1) return -1;

        char t = readChar(s, cur);
        if(t == ' ')//_[Spaces]
            return matchSpaces(s, cur + 1);
        else
            return cur;
    }

    bool isNumber(string s)
    {
        int c = matchSpaces(s, 0);//[Spaces][Number][Spaces]
        c = matchNumber(s, c);
        c = matchSpaces(s, c);
        return c == s.size();
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)

## Deterministic Finite Automata
By observing the samples above, we can draw a DFA.
The structure is shown in the code.
{% highlight cpp %}
class Solution {
public:
    map<char,int> DFA[10];
    #define ACCEPT 999
    int cnt;
    int state;
    
    void init()
    {
        cnt = 0;
        state = 1;
        DFA[1][' '] = 1; DFA[1]['s'] = 2; DFA[1]['d'] = 3; DFA[1]['.'] = 9;
        DFA[2]['d'] = 3; DFA[2]['.'] = 9;
        DFA[3]['d'] = 3; DFA[3]['.'] = 4; DFA[3]['e'] = 5; DFA[3]['#'] = ACCEPT; DFA[3][' '] = 8;
        DFA[4]['d'] = 4; DFA[4]['e'] = 5; DFA[4]['#'] = ACCEPT; DFA[4][' '] = 8;
        DFA[5]['d'] = 7; DFA[5]['s'] = 6;
        DFA[6]['d'] = 7;
        DFA[7]['d'] = 7; DFA[7][' '] = 8; DFA[7]['#'] = ACCEPT;
        DFA[8][' '] = 8; DFA[8]['#'] = ACCEPT;
        DFA[9]['d'] = 4;
    }
    
    inline char readOne(string & s)
    {   
        char ans;
        if(cnt >= s.size()) ans = '#';
        else if(isdigit(s[cnt])) ans = 'd';
        else if(s[cnt] == '+' || s[cnt] == '-') ans = 's';
        else ans = s[cnt];
        ++cnt;
        return ans;
    }

    bool isNumber(string s) {
        init();
        while((state = DFA[state][readOne(s)]) && state != ACCEPT);
        return state == ACCEPT;
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)

## Finding law
By observing the samples above, we can find the law and solve it directly
{% highlight cpp %}
class Solution {
public:
    void init(string & s)
    {
        int low=0,high=s.size()-1;
        while(s[low]==' ') ++low;
        while(s[high]==' ') --high;
        s = s.substr(low,high-low+1);
    }

    bool isNum(string s,bool dot)
    {
        if(s.size()==0) return false;
        if(s[0]=='-' || s[0]=='+') s = s.substr(1,s.size()-1);
        int n = s.size();
        if(n==0) return false;
        if(s[0]=='.' && n==1) return false;

        bool dotcnt=0;
        for(int i=0;i<n;++i)
            if(!isdigit(s[i]))
            {
                if(s[i]=='.')
                {
                    if(dot == false) return false;
                    if(dotcnt) return false;
                    else dotcnt = true;
                }
                else return false;
            }
    }

    bool isNumber(string s) {

        init(s);
        if(s=="") return false;

        for(int i=0;s[i];++i)
            if(s[i]=='e')
                return isNum(s.substr(0,i),true) && isNum(s.substr(i+1,s.size()-i-1),false);

        return isNum(s,1);
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)
