# Find Median by Heap

動態地維護了一個大頂堆max_heap和一個小頂堆min_heap，這兩個堆各自存放「一半」的資料（這裡的「一半」是指兩個堆的size()相差小於等於1），且維持max_heap的堆頂<=min_heap的堆頂。

1. 在插入新元素x時，如果max_heap為空，直接將x壓入max_heap；

2. 否則，先比較 max_heap.size() 與 min_heap.size()：

    若max_heap與min_heap元素個數相同：

        若 x < max_heap 的堆頂，將x壓入 max_heap；

        否則，將x壓入 min_heap；

    若 max_heap 比 min_heap 的size小（需要通過調整兩個堆，以保證平衡）：

        若 x < max_heap 的堆頂，直接將x壓入 max_heap；

        否則，將 min_heap 的堆頂彈出並壓入 max_heap，再將x壓入min_heap

   若 max_heap 比 min_heap 的size大（同樣需要通過調整兩個堆）：

        若 x > max_heap 的堆頂，直接將x壓入 min_heap；

        否則，將 max_heap 的堆頂彈出並壓入 min_heap，再將x壓入max_heap

3. 求中位數:

    若max_heap與min_heap元素個數相同：

        median = max_heap 的堆頂與 min_heap 的堆頂的算數平均值

    若 max_heap 比 min_heap 的size小：

        median = min_heap 的堆頂

    否則：

        median = max_heap 的堆頂


## leecode problem 295

```
#include<bits/stdc++.h>

using namespace std;

typedef long long ll;

// leetcode problem 295

int main(){
    priority_queue<int> max_heap;
    priority_queue<int, vector<int>, greater<int>> min_heap;

    int x;
    cin >> x;
    max_heap.push(x);
    while(cin >> x){
        if(x == 0){
            break;
        }
        if(max_heap.size() > min_heap.size()){
            if(x >= max_heap.top()){
                min_heap.push(x);
            }else{
                min_heap.push(max_heap.top());
                max_heap.pop();
                max_heap.push(x);
            }

        }else if(max_heap.size() < min_heap.size()){
            if(x <= min_heap.top()){
                max_heap.push(x);
            }else{
                max_heap.push(min_heap.top());
                min_heap.pop();
                min_heap.push(x);
            }
        }else{
            if(x >= max_heap.top()){
                min_heap.push(x);
            }else{
                max_heap.push(x);
            }
        }

        if(max_heap.size() > min_heap.size()){
            cout << max_heap.top() << endl;
        }else if(max_heap.size() < min_heap.size()){
            cout << min_heap.top() << endl;
        }else{
            cout << (float)(max_heap.top() + min_heap.top()) / 2 << endl;
        }

    }

	return 0;
}

```


