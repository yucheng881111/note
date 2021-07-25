# Prefix Sum + Dynamic Programming

leetcode problem 813: Largest Sum of Averages

給定k值，將一個陣列分成至多k份，找到每一份平均的總和最大的分法，並回傳總和值。例如:
array = [9,1,2,3,9], k = 3，
則最好分法為 9 / 1 2 3 / 9，總和為9/1+(1+2+3)/3+9/1=9+2+9=20。
## 解法說明
用圖來說明此題解法。
![](https://i.imgur.com/6seqtCH.jpg)

![](https://i.imgur.com/hCmnNT7.jpg)

![](https://i.imgur.com/kmWa40S.jpg)

## Code
leetcode problem 813
```
class Solution {
public:
    double largestSumOfAverages(vector<int>& nums, int k) {
        int len = nums.size();
        double prefix_sum[len];
        prefix_sum[0] = nums[0];
        for(int i=1;i<len;++i){
            prefix_sum[i] = nums[i] + prefix_sum[i-1];
        }
        double dp[k][len];
        for(int i=0;i<k;++i){
            for(int j=0;j<len;++j){
                dp[i][j] = 0;
            }
        }
        for(int i=0;i<len;++i){
            dp[0][i] = prefix_sum[i]/(i+1);
        }
        
        for(int i=1;i<k;++i){
            for(int j=i;j<len;++j){
                for(int n=i-1;n<j;++n){
                    dp[i][j] = max(dp[i-1][n] + (prefix_sum[j]-prefix_sum[n])/(j-n) , dp[i][j]);
                }
            }
        }
        
        
        return dp[k-1][len-1];
        
    }
};
```




