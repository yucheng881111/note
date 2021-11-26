# Huffman Code (Priority Queue)

## Huffman code tree
統計字元(串)出現的次數(頻率)做成node，存入priorty queue裡。每次取出前兩個頻率低的node，結合成一個新的node並push回priorty queue裡。push回去之前要記得將left, right child的指標接好。如此操作每次queue中node的數量會少1，因此執行node_num-1次，queue中就會剩下一個root node，huffman code tree也隨之建好。下圖是一個huffman code tree範例。

![](https://i.imgur.com/9Nwd28t.png)

上圖表示a這個元素要用4個bits(tree depth = 4)、pen則要用2 bits(tree depth = 2)。

## Priorty Queue用法整理
預設由大排到小:
```
priority_queue<T> pq; 
```
改成由小排到大:
```
priority_queue<T, vector<T>, greater<T> > pq;
```
自定義cmp排序:
```
priority_queue<T, vector<T>, cmp> pq;
```
cmp寫法:
```
struct cmp{
    bool operator()(T a, T b){
        return ...;
    }
};
```

## 範例code
```
#include<bits/stdc++.h>

using namespace std;

typedef long long ll;

class Node{
public:
    int freq;
    Node* left_child;
    Node* right_child;
    Node* parent;
    string str;
    bool is_leaf;
    Node(string s1, int n, bool b){
        left_child = nullptr;
        right_child = nullptr;
        parent = nullptr;
        is_leaf = b;
        str = s1;
        freq = n;
    }

};

struct cmp{
    bool operator()(Node* a, Node* b){
        if(a->freq != b->freq){
            return a->freq > b->freq;
        }
        return a->str > b->str;
    }
};

void compress(unordered_map<string,int> &m, Node *r, int d){

    if(r->is_leaf){
        m[r->str] = d;
        return ;
    }

    if(r->left_child != nullptr){
        compress(m, r->left_child, d+1);
    }
    if(r->right_child != nullptr){
        compress(m, r->right_child, d+1);
    }

}

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    int n;
    cin >> n;
    cin.ignore();
    unordered_map<string, int> m;
    vector<vector<string>> input(n);
    for(int i=0;i<n;++i){
        string s;
        getline(cin, s);
        stringstream ss(s);
        string tmp;
        while(ss >> tmp){
            input[i].push_back(tmp);
            if(m.count(tmp) == 0){
                m[tmp] = 1;
            }else{
                m[tmp]++;
            }
        }
    }

    int len = m.size();

    priority_queue<Node*,vector<Node*>,cmp> pq;

    for(const auto &v: m){
        Node* node = new Node(v.first, v.second, 1);
        pq.push(node);
    }

    for(int i=0;i<len-1;++i){
        Node* t1 = pq.top();
        pq.pop();
        Node* t2 = pq.top();
        pq.pop();
        string s_tmp = t1->str < t2->str ? t1->str: t2->str;
        Node* new_node = new Node(s_tmp, t1->freq+t2->freq, 0);
        new_node -> left_child = t1;
        new_node -> right_child = t2;
        pq.push(new_node);
    }

    Node* root = pq.top();

    unordered_map<string,int> res;
    compress(res, root, 0);

    for(int i=0;i<n;++i){
        ll cnt = 0;
        ll l = input[i].size();
        for(int j=0;j<l;++j){
            cnt += res[input[i][j]];
        }
        printf("%.10f\n", (double)cnt / (l * ceil(log2(len))));
    }

    return 0;
}

```