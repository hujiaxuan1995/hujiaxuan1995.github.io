title: leetcode 3:最长字串长度
date: 2016-03-30 10:53:41
tags: leetcode
categories: oj
---
#### 题目描述
[Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

给一个字符串，找到其中字符不重复的最长字串。例如，“abcabcbb”的不重复最长子串是“abc”，长度为3。
“bbbb”的最长不重复字串为“b”,长度为1。
输入格式：abcabcbb
输出格式：3
#### 算法思路
- 用hash来存储字符，用来判断字符是否重复int tmp[256] 初始值为false，表示字符未出现，遍历字符时
令tmp[i] = true。
- 用i，j来表示从i位置到j位置的子串。初始i=0，j=0。然后j+=1对字符串遍历，直到tmp[j]==true说明
字符重复了，此时i不必加1，而是i往前遍历直到s[i]==s[j]，因为i+1到j的字串长度肯定比i到j的子串长度
小。

#### 代码
·
    int max(int a, int b){
                    return a > b ? a : b;
                }
                int lengthOfLongestSubstring(char* s){
                    int tmp[256];
                    int maxsub = 0;
                    int i = 0, j = 0;
                    int n = 0;
                    memset(tmp, -1, 256);
                    n = strlen(s);
                    while (j < n){
                        if (tmp[s[j]] == 1){
                            maxsub = max(maxsub, j - i);
                            while (s[i] != s[j]){
                                tmp[s[i]] = -1;
                                i++;
                            }
                            i++;
                            j++;
                        }
                        else{
                            tmp[s[j]] = 1;
                            j++;
                        }
                    }
                    maxsub = max(maxsub, n - i);
                    if (n == 0){
                        return 0;
                    }
                    else{
                        return maxsub;
                    }

                }
·
            