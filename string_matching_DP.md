# String Matching (Dynamic Programming)

leetcode problem 44: Wildcard Matching

'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).

Ex 1:
Input: s = "adceb", p = "\*a*b"
Output: true

Ex 2:
Input: s = "acdcb", p = "a*c?b"
Output: false

## 解法
![](https://i.imgur.com/KcEBqAJ.jpg)
![](https://i.imgur.com/Bcz2Bn5.jpg)
![](https://i.imgur.com/TdJ2WL5.jpg)

## Code

leetcode problem 44

```
class Solution {
public:
    bool isMatch(string s, string p) {
        string p1 = "";
        char c = '0';
        for(int i=0;i<p.size();++i){
            if(p[i] == '*' && c == '*'){
                c = p[i];
                continue;
            }
            p1 += p[i];
            c = p[i];
        }
        
        int row = s.size()+1;
        int col = p1.size()+1;
        bool dp[row][col];
        for(int i=0;i<row;++i){
            for(int j=0;j<col;++j){
                dp[i][j] = 0;
            }
        }
        dp[0][0] = 1;
        
        string s1 = " " + s;
        p1 = " " + p1;
        
        if(p1[1] == '*'){
            dp[0][1] = 1;
        }
        
        for(int i=1;i<row;++i){
            for(int j=1;j<col;++j){
                if(s1[i] == p1[j] || p1[j] == '?'){
                    dp[i][j] = dp[i-1][j-1];
                }
                
                if(p1[j] == '*'){
                    dp[i][j] = dp[i-1][j] || dp[i][j-1];
                }
            }
        }
        
        return dp[row-1][col-1];
        
    }
};
```
