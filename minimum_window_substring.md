# Sliding Window (貪食蛇字串切割法)

leetcode problem 76: Minimum Window Substring

以例子來說明題目。
Example 1:
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.

## 解法
![](https://i.imgur.com/DtGSPU3.jpg)

用兩個變數i, j來迭代s。其中i負責增長貪食蛇的身體，直到全身都包含t。這時代表找到了一個substring是包含t的，但它不一定是最小的。因此紀錄len和起始index之後要繼續找下去。移動j來縮短貪食蛇的身體，若減少的字元包含在t中，代表減少了這個字元，該substring將不再是一組解。因此我們要再移動i來增長身體。這樣不斷地增加和減少substring長度，就像貪食蛇的身體會增加和縮短一樣。

而s中的字元是否包含在t中，都用一個cnt陣列和一個total變數來做判斷。詳見以下code。

## Code
```
class Solution {
public:
    string minWindow(string s, string t) {
        int cnt[128];
        memset(cnt,0,sizeof(cnt));
        int s_len = s.size();
        int t_len = t.size();
        
        for(int i=0;i<t_len;++i){
            cnt[t[i]]++;
        }
        
        int total = t_len;
        int sub_len = 9999999;
        int sub_idx = -1;
        for(int i=0,j=0;i<s_len;++i){
            // i goes first
            cnt[s[i]]--;
            if(cnt[s[i]] >= 0){
                total--;
            }
            // j goes after i
            while(total == 0){
                if(i - j + 1 < sub_len){
                    sub_len = i - j + 1;
                    sub_idx = j;
                }
                if(cnt[s[j]] == 0){
                    total++;
                }
                cnt[s[j]]++;
                j++;
            }
        }
        
        return sub_idx == -1 ?  "" : s.substr(sub_idx, sub_len);
    }
};
```


