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


### leetcode problem 44

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

### leetcode problem 10

星號變成只代表前一個字元可以出現0次或重複次，另外'.'和'?'意義相同。解法差不多，只需稍作修改即可。要注意的地方是，星號代表前一個字元出現一次以上時，除了要dp[i-1][j]成立，還要現在這個字元和\*前的字元相同。整個判斷式就會變成:
```
(((s1[i] == p1[j-1]) || (p1[j-1] == '.')) && dp[i-1][j])
```
另外，初始化的地方也要特別注意。不只要將dp[0][0]設為1，星號出現的那格要設為和前兩格相同的值。如下:
```
dp[0][0] = 1;
for(int j=2;j<col;++j){
    if(p1[j] == '*'){
        dp[0][j] = dp[0][j-2];
    }
}
```
因為星號可以代表前一個字元出現0次，也就是不出現。因此"a*"是match空字串的。"a\*b*"也是match ""的。但"a\*bc*"就不會match ""了。


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
        
        int row = s.size() + 1;
        int col = p1.size() + 1;
        bool dp[row][col];
        for(int i=0;i<row;++i){
            for(int j=0;j<col;++j){
                dp[i][j] = 0;
            }
        }
        
        // padding
        string s1 = " " + s;
        p1 = " " + p1;
        
        dp[0][0] = 1;
        for(int j=2;j<col;++j){
            if(p1[j] == '*'){
                dp[0][j] = dp[0][j-2];
            }
        }
        
        for(int i=1;i<row;++i){
            for(int j=1;j<col;++j){
                if(s1[i] == p1[j] || p1[j] == '.'){
                    dp[i][j] = dp[i-1][j-1];
                }
                
                if(p1[j] == '*'){
                    // more than 1 chars || 1 char || 0 char
                    dp[i][j] = (((s1[i] == p1[j-1]) || (p1[j-1] == '.')) && dp[i-1][j]) || dp[i][j-1] || dp[i][j-2];
                    
                }
            }
        }
        
        return dp[row-1][col-1];
    }
};
```