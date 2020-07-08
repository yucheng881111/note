# TSP(Traveling Salesman Problem)

## Dynamic Programming常用技巧
通常dp的題目解法會用到一個二維陣列，關鍵就在如何規劃此陣列的縱軸和橫軸。通常橫軸會是所有狀態集合，而縱軸是狀態量。舉例來說，01背包問題的縱軸是一一放入每個物品(狀態量)，橫軸是所有可能的重量(所有可能狀態)，而儲存的值便是價格(所求)；tsp裡縱軸是每個節點，橫軸是所有節點構成的集合(下方會有詳細解說)，儲存的值是縱軸節點到橫軸集合總共所需花費的cost，路徑便用額外的一個二維陣列存末端節點，也比較方便計算回到出發點的cost。
## Bitset狀態壓縮技巧
一個set裡有n個元素，就能構成2^n個subset，也就是所有可能的狀態集合。以三個節點(1,2,3)為例:


| 000  | 001  | 010  | 011 | 100 | 101 | 110 | 111 |
| ---- | ---- | ---- | --- | --- | --- | --- | --- |
| 全不包含 | 包含1 | 包含2 | 包含1,2 | 包含3 | 包含1,3 | 包含2,3 | 包含1,2,3 |

這樣就能用for迴圈跑遍所有狀態
```
for(int i=0;i<8;++i){
    bitset<n> b(i);
    ...
}
```
接著只要熟悉位元運算(&,|,^,>>,<<)，就能依照題意寫出判斷式。

## TSP code
### 迴圈實現
```
#include<bits/stdc++.h>

using namespace std;

int dp[20+1][1<<20];
int r[20+1][1<<20];

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    int n;
    cin>>n;

    for(int i=0;i<n+1;++i){
        for(int j=0;j<(1<<n);++j){
            dp[i][j]=1023456789;
            r[i][j]=0;
        }
    }

    int N;
    cin>>N;
    for(int i=0;i<N;++i){
        int u,v,w;
        cin>>u>>v>>w;
        dp[u][1<<(v-1)]=w;
        dp[v][1<<(u-1)]=w;
        r[u][1<<(v-1)]=v;
        r[v][1<<(u-1)]=u;
    }

    vector<int> Map(1<<n,1023456789);
    for(int i=1;i<=n;++i){
        Map[1<<(i-1)]=i;
    }


    for(int t=2;t<n;++t){
        for(int i=1;i<=n;++i){
            for(int j=0;j<(1<<n);++j){
                bitset<20> b(j);
                if(b.count()==t&&(((1<<(i-1))&j)==0)){
                    int temp=1;
                    int Min=1023456789;
                    for(int m=0;m<n;++m){
                        if((j&temp)!=0){
                            int tmp=dp[i][temp]+dp[Map[temp]][temp^j];
                            if(tmp<Min){
                                Min=tmp;
                                dp[i][j]=tmp;
                                r[i][j]=r[Map[temp]][temp^j];
                            }
                        }
                        temp<<=1;
                    }
                }
            }
        }

    }

    for(int i=1;i<=n;++i){
        for(int j=0;j<(1<<n);++j){
            if(dp[i][j]==1023456789){
                cout<<"oo"<<" ";
            }else{
                cout<<dp[i][j]<<" ";
            }
        }
        cout<<endl;
    }

    //start from 1, pass all node, back to 1
    int start=1;
    int Min=1023456789;
    for(int i=1;i<=n;++i){
        if(i!=start){
            int temp=dp[start][1<<(i-1)]+dp[i][(((1<<n)-1)^(1<<(start-1))^(1<<(i-1)))]+dp[r[i][((1<<n)-1)^(1<<(start-1))^(1<<(i-1))]][1<<(start-1)];
            // temp<0 => int overflow
            if(temp>0&&temp<Min){
                Min=temp;
            }
        }

    }
    cout<<endl<<Min<<endl;


return 0;
}

/*
test:
8(nodes) 13(paths)
1 2 4
1 4 8
2 3 4
3 4 2
1 3 7
1 8 4
3 6 10
4 5 4
5 6 5
5 8 6
6 8 3
6 7 6
7 8 4
*/

```
### 遞迴實現

```
//download from internet
#include <vector>
#include <iostream>

using namespace std;

/**
    \brief Given a complete, undirected, weighted graph in the form of an adjacency matrix,
           returns the smallest tour that visits all nodes and starts and ends at the same
           node. This dynamic programming solution runs in O(n * 2^n).
    \return the minimum cost to complete the tour
*/
int tsp(const vector<vector<int>>& cities, int pos, int visited, vector<vector<int>>& state)
{
    if(visited == ((1 << cities.size()) - 1))
        return cities[pos][0]; // return to starting city

    if(state[pos][visited] != INT_MAX)
        return state[pos][visited];

    for(int i = 0; i < cities.size(); ++i)
    {
        // can't visit ourselves unless we're ending & skip if already visited
        if(i == pos || (visited & (1 << i)))
            continue;

        int distance = cities[pos][i] + tsp(cities, i, visited | (1 << i), state);
        if(distance < state[pos][visited])
            state[pos][visited] = distance;
    }

    return state[pos][visited];
}

int main()
{
    vector<vector<int>> cities = { { 0, 20, 42, 35 },
                                   { 20, 0, 30, 34 },
                                   { 42, 30, 0, 12 },
                                   { 35, 34, 12, 0 }
                                 };

    vector<vector<int>> state(cities.size());
    for(auto& neighbors : state)
        neighbors = vector<int>((1 << cities.size()) - 1, INT_MAX);

    cout << "minimum: " << tsp(cities, 0, 1, state) << endl;

    return 0;
}
```













