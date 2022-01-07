# Strongly Connected Component (SCC)

## Reference
1. http://alrightchiu.github.io/SecondRound/graph-li-yong-dfsxun-zhao-strongly-connected-componentscc.html
2. https://web.ntnu.edu.tw/~algo/Component.html

## 找出所有SCC

![](https://i.imgur.com/wYUef8S.png)

1. 第一次DFS得到的finish之由大到小順序 (finish order)
2. 在GT上進行第二次DFS，就可以分出所有的SCC

```
void dfs(int i, vector<vector<int>> &adj, vector<bool> &visit, vector<int> &finish_order){
    visit[i] = 1;
    for(int j=0;j<adj[i].size();++j){
        if(!visit[adj[i][j]]){
            dfs(adj[i][j], adj, visit, finish_order);
        }
    }
    finish_order.push_back(i);
}

void dfs2(int i, vector<vector<int>> &adj, vector<bool> &visit, int group_idx, vector<int> &result_group, vector<int> &group_size){
    result_group[i] = group_idx;
    group_size[group_idx]++;
    visit[i] = 1;
    for(int j=0;j<adj[i].size();++j){
        if(!visit[adj[i][j]]){
            dfs2(adj[i][j], adj, visit, group_idx, result_group, group_size);
        }
    }
}

int get_SCC(vector<vector<int>> &adj, vector<vector<int>>& transpose_adj, vector<int> &result_group, vector<int> &group_size){
    vector<bool> visit(N, 0);
    vector<int> finish_order;
    // dfs(adj) to get finish order
    for(int i=0;i<N;++i){
        if(!visit[i]){
            dfs(i, adj, visit, finish_order);
        }
    }

    vector<bool> visit2(N, 0);
    int idx = 0;
    // dfs(transpose adj) again in reversed finish order
    for(int i=N-1;i>=0;--i){
        if(!visit2[finish_order[i]]){
            dfs2(finish_order[i], transpose_adj, visit2, idx, result_group, group_size);
            idx++;
        }
    }

    return idx;
}
```

## 畫出SCC Graph
收縮所有有向圖的環，最後畫出的圖是一個DAG。

![](https://i.imgur.com/65jiloS.png)

畫法: 檢查原本的adj list(matrix)，若node i和node j有連線但分屬不同的SCC group；假設node i屬group a、node j屬group b，則a、b間建一條SCC edge。

```
void build_SCC_graph(int component_size, vector<vector<int>> &adj, vector<int> &group, vector<vector<int>> &SCC_graph_adj){
    for(int i=0;i<N;++i){
        for(int j=0;j<adj[i].size();++j){
            // build edge between different group
            if(group[i] != group[adj[i][j]]){
                SCC_graph_adj[group[i]].push_back(group[adj[i][j]]);
            }
        }
    }

}
```

## Example Problem and Code

### 題目
一個有環的有向圖，找到一條含最多不重複的node的路徑(最長路徑)。

### 解
必須先用SCC收縮環，再以DFS搜尋收縮後的SCC Graph。最後DP從leaf至root加總路徑上SCC group的所有node。

### code
```
#include<bits/stdc++.h>

using namespace std;

typedef long long ll;

int M, N;

void dfs(int i, vector<vector<int>> &adj, vector<bool> &visit, vector<int> &finish_order){
    visit[i] = 1;
    for(int j=0;j<adj[i].size();++j){
        if(!visit[adj[i][j]]){
            dfs(adj[i][j], adj, visit, finish_order);
        }
    }
    finish_order.push_back(i);
}

void dfs2(int i, vector<vector<int>> &adj, vector<bool> &visit, int group_idx, vector<int> &result_group, vector<int> &group_size){
    result_group[i] = group_idx;
    group_size[group_idx]++;
    visit[i] = 1;
    for(int j=0;j<adj[i].size();++j){
        if(!visit[adj[i][j]]){
            dfs2(adj[i][j], adj, visit, group_idx, result_group, group_size);
        }
    }
}

int get_SCC(vector<vector<int>> &adj, vector<vector<int>>& transpose_adj, vector<int> &result_group, vector<int> &group_size){
    vector<bool> visit(N, 0);
    vector<int> finish_order;
    // dfs(adj) to get finish order
    for(int i=0;i<N;++i){
        if(!visit[i]){
            dfs(i, adj, visit, finish_order);
        }
    }

    vector<bool> visit2(N, 0);
    int idx = 0;
    // dfs(transpose adj) again in reversed finish order
    for(int i=N-1;i>=0;--i){
        if(!visit2[finish_order[i]]){
            dfs2(finish_order[i], transpose_adj, visit2, idx, result_group, group_size);
            idx++;
        }
    }

    return idx;
}

void build_SCC_graph(int component_size, vector<vector<int>> &adj, vector<int> &group, vector<vector<int>> &SCC_graph_adj){
    for(int i=0;i<N;++i){
        for(int j=0;j<adj[i].size();++j){
            // build edge between different group
            if(group[i] != group[adj[i][j]]){
                SCC_graph_adj[group[i]].push_back(group[adj[i][j]]);
            }
        }
    }

}

void dfs_SCC(int i, vector<vector<int>> &adj, vector<bool> &visit, vector<int> &finish_order){
    visit[i] = 1;
    for(int j=0;j<adj[i].size();++j){
        if(!visit[adj[i][j]]){
            dfs_SCC(adj[i][j], adj, visit, finish_order);
        }
    }
    finish_order.push_back(i);
}

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    vector<vector<int>> adj(200001);
    vector<vector<int>> transpose_adj(2000001);

    cin >> N >> M;

    for(int i=0;i<M;++i){
        int x, y;
        cin >> x >> y;
        adj[x].push_back(y);
        transpose_adj[y].push_back(x);
    }

    vector<int> result_group(N, -1);
    vector<int> group_size(N, 0);

    int component_size = get_SCC(adj, transpose_adj, result_group, group_size);
    vector<vector<int>> SCC_graph_adj(component_size);
    build_SCC_graph(component_size, adj, result_group, SCC_graph_adj);

    vector<bool> visit(component_size, 0);
    vector<int> finish_order;
    for(int i=0;i<component_size;i++){
        if(!visit[i]){
            dfs_SCC(i, SCC_graph_adj, visit, finish_order);
        }
    }

    int ans = -1;
    for(int i=0;i<component_size;i++){
        int m = 0;
        for(int j=0;j<SCC_graph_adj[finish_order[i]].size();++j){
             m = max(m, group_size[SCC_graph_adj[finish_order[i]][j]]);
        }
        group_size[finish_order[i]] += m;
        ans = max(ans, group_size[finish_order[i]]);
    }
    cout << ans << endl;



    return 0;
}

```

