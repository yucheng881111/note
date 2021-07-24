# Binary Search + 01背包問題

Problem from leetcode 1751: Maximum Number of Events That Can Be Attended II
![](https://i.imgur.com/kWi1BYq.jpg)
每天僅能執行一種event，給定k值，例如k=3，則只能選3個event參加。以上圖為例，event1和event2在第三天重複，則這兩個event不能同時參加。找出能讓value最大的組合。上例若k=3，則答案為36，分別選event1,3,4。

## 解題思路
1. event依順序一個一個加入，每個event可以選或不選: **01背包問題**。
2. 加入新event = 本event value值 + 上一個不overlap的event之dp值。
3. 找出上一個不overlap的event: **binary search**。

## 解題方法
1. sort events by its end-days(小到大)。
2. 畫出dp表格，並初始化為0。

| event\k |  0  |  1  |  2  |  3  |
|:-------:|:---:|:---:|:---:|:---:|
|    0    |  0  |  0  |  0  |  0  |
|    1    |  0  |  0  |  0  |  0  |
|    2    |  0  |  0  |  0  |  0  |
|    3    |  0  |  0  |  0  |  0  |
|    4    |  0  |  0  |  0  |  0  |

從k=1開始填，event少填到多，一個一個加入。
| event\k |  0  |  1  |  2  |  3  |
|:-------:|:---:|:---:|:---:|:---:|
|    0    |  0  |  0  |  0  |  0  |
|    1    |  0  | 10  | 10  |     |
|    2    |  0  | 15  |     |     |
|    3    |  0  | 15  |     |     |
|    4    |  0  | 20  |     |     |

接著以k=2, event=2為例: 若加入event2，則event2之value為15，而event2之上一個event為空(event1發生overlap)，因此找到k=1, event0的值，為0。最後k=2, event=2的值為**max(加入event2, 不加入event2)** = max(15+0, 10) = 15。
| event\k |  0  |  1  |  2  |  3  |
|:-------:|:---:|:---:|:---:|:---:|
|    0    |  0  |  0  |  0  |  0  |
|    1    |  0  | 10  | 10  |     |
|    2    |  0  | 15  | 15  |     |
|    3    |  0  | 15  |     |     |
|    4    |  0  | 20  |     |     |

再以k=2, event=3為例: **dp[3][2] = max(加入event3, 不加入event3)**。event3的上一個event為event1(event2 overlap)。因此**加入event3**的話，其值為event3的value 6，加上k=1, event1的值，為10。**不加入event3**的話，值同k=2, event2的值15。而16大於15，因此得知**加入event3**是較好的選擇。
| event\k |  0  |  1  |  2  |  3  |
|:-------:|:---:|:---:|:---:|:---:|
|    0    |  0  |  0  |  0  |  0  |
|    1    |  0  | 10  | 10  |     |
|    2    |  0  | 15  | 15  |     |
|    3    |  0  | 15  | 16  |     |
|    4    |  0  | 20  |     |     |

整理後得知 **dp[i][j] = max(events[i-1][2] + dp[prev(events,i)][j-1] , dp[i-1][j])**。events[i-1][2]為該event value值，i-1是因為陣列從0開始。prev(events,i)為**binary search**，找出event i的上一個event並回傳。例如prev(events, 3)=1，而prev(events, 4)=3。另外，我們會發現prev(events, 1)、prev(events, 2)、prev(events, 3)、prev(events, 4) 每次算都會是0,0,1,3。因此可以先算好將其存著，進一步加速程式的執行。最後，將dp表格填完。
| i \ j   |  0  |  1  |  2  |  3  |
|:-------:|:---:|:---:|:---:|:---:|
|    0    |  0  |  0  |  0  |  0  |
|    1    |  0  | 10  | 10  | 10  |
|    2    |  0  | 15  | 15  | 15  |
|    3    |  0  | 15  | 16  | 16  |
|    4    |  0  | 20  | 35  | 36  |

表格最右下的值，dp[4][3]即為所求，答案為36。

## sort by end
最後，說明為什麼要根據end-days來排序。因為這個dynamic programming是一個bottom-up方法，他會去找**上一個不overlap的event**。以下圖k=2為例:
**sort by start:**
![](https://i.imgur.com/Tkbvsm5.jpg)
在k=2加入event3的時候，用prev找出的上一個event會是event2，而event2, k=1的值會是47，加入event3的答案會變成47+43=90，而這明顯是錯的。
**sort by end:**
![](https://i.imgur.com/xn84aXE.jpg)
這時用prev找到的上一個event是event1，答案就會是8+43=51，是正確的。

## Code
c++ code:
```
#include<bits/stdc++.h>

using namespace std;

bool cmp(vector<int> &a,vector<int> &b){
    if(a[1]==b[1]) return a[0]<b[0];
    return a[1]<b[1];
}

int prev(vector<vector<int>> &events,int n){
    int k = n-1;
    int cur = events[k][0];
    int ans = -1;
    int h = k;
    int l = 0;
    while(h >= l){
        int mid = (h+l)/2;
        if(events[mid][1] >= cur){ //overlap
            h = mid-1;
        }else{
            l = mid+1;
            ans = mid;
        }
    }

    return ans+1;
}


int maxValue(vector<vector<int>>& events, int k) {
    int num_event = events.size();
    int dp[num_event+1][k+1];
    for(int i=0;i<num_event+1;++i){
        for(int j=0;j<k+1;++j){
            dp[i][j] = 0;
        }
    }


    sort(events.begin(),events.end(),cmp);

    for(int j=1;j<k+1;++j){
        for(int i=1;i<num_event+1;++i){
            dp[i][j] = max(events[i-1][2]+dp[prev(events,i)][j-1] , dp[i-1][j]);
        }
    }

    for(int i=0;i<num_event+1;++i){
        for(int j=0;j<k+1;++j){
            cout<<dp[i][j]<<" ";
        }
        cout<<endl;
    }

    return dp[num_event][k];
}



int main(){
    vector<vector<int>> events;
    //[[21,77,43],[2,74,47],[6,59,22],[47,47,38],[13,74,57],[27,55,27],[8,15,8]] 4

    int a1[3]={21,77,43};
    int a2[3]={2,74,47};
    int a3[3]={6,59,22};
    int a4[3]={47,47,38};
    int a5[3]={13,74,57};
    int a6[3]={27,55,27};
    int a7[3]={8,15,8};
    vector<int> e1,e2,e3,e4,e5,e6,e7;
    e1.assign(a1,a1+3);
    e2.assign(a2,a2+3);
    e3.assign(a3,a3+3);
    e4.assign(a4,a4+3);
    e5.assign(a5,a5+3);
    e6.assign(a6,a6+3);
    e7.assign(a7,a7+3);
    events.push_back(e1);
    events.push_back(e2);
    events.push_back(e3);
    events.push_back(e4);
    events.push_back(e5);
    events.push_back(e6);
    events.push_back(e7);

    cout<<maxValue(events,4)<<endl;

    return 0;
}
```

leetcode problem 1751:
```
bool cmp(vector<int> &a,vector<int> &b){
    if(a[1]==b[1]) return a[0]<b[0];
    return a[1]<b[1];
}

int prev(vector<vector<int>> &events,int n){
    int k = n-1;
    int cur = events[k][0];
    int ans = -1;
    int h = k;
    int l = 0;
    while(h >= l){
        int mid = (h+l)/2;
        if(events[mid][1] >= cur){ //overlap
            h = mid-1;
        }else{
            l = mid+1;
            ans = mid;
        }
    }

    return ans+1;
}

class Solution {
public:
    int maxValue(vector<vector<int>>& events, int k) {
        int num_event = events.size();
        int dp[num_event+1][k+1];
        for(int i=0;i<num_event+1;++i){
            for(int j=0;j<k+1;++j){
                dp[i][j] = 0;
            }
        }


        sort(events.begin(),events.end(),cmp);

        for(int j=1;j<k+1;++j){
            for(int i=1;i<num_event+1;++i){
                dp[i][j] = max(events[i-1][2]+dp[prev(events,i)][j-1] , dp[i-1][j]);
            }
        }

        /*for(int i=0;i<num_event+1;++i){
            for(int j=0;j<k+1;++j){
                cout<<dp[i][j]<<" ";
            }
            cout<<endl;
        }*/

        return dp[num_event][k];
    }
};
```
