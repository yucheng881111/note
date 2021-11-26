# Min-Max Heap (Double-ended Priority Queue)

https://medium.com/%E7%8B%97%E5%A5%B4%E5%B7%A5%E7%A8%8B%E5%B8%AB/%E5%9C%96%E8%A7%A3-double-ended-priority-queue-%E9%80%B2%E9%9A%8E%E6%A8%B9-1ae18d2ca402

```
#include<bits/stdc++.h>

using namespace std;

typedef long long ll;

class minmax_heap{
public:
    vector<int> mmheap;
    minmax_heap(){
        mmheap.push_back(INT_MAX);
    }

    void print(){
        for(const auto &h: mmheap){
            cout << h << " ";
        }
        cout << endl;
    }

    int mmheap_size(){
        return mmheap.size() - 1;
    }

    void VerifyMin_upward(int idx){
        int grand_idx;
        while(1){
            grand_idx = idx / 4;
            if(grand_idx == 0){
                break;
            }
            if(mmheap[idx] < mmheap[grand_idx]){
                swap(mmheap[idx], mmheap[grand_idx]);
                idx = grand_idx;
            }else{
                break;
            }
        }
    }

    void VerifyMax_upward(int idx){
        int grand_idx;
        while(1){
            grand_idx = idx / 4;
            if(grand_idx == 0){
                break;
            }
            if(mmheap[idx] > mmheap[grand_idx]){
                swap(mmheap[idx], mmheap[grand_idx]);
                idx = grand_idx;
            }else{
                break;
            }
        }
    }

    void mmheap_insert(int a){
        mmheap.push_back(a);
        int idx = mmheap.size() - 1;
        int level = log2(idx);
        int parent_level = level - 1;
        int parent_idx = idx / 2;
        if(level % 2){      // parent in min_level
            if(a < mmheap[parent_idx]){
                swap(mmheap[idx], mmheap[parent_idx]);
                VerifyMin_upward(parent_idx);
            }else{
                VerifyMax_upward(idx);
            }
        }else{              // parent in max_level
            if(a > mmheap[parent_idx]){
                swap(mmheap[idx], mmheap[parent_idx]);
                VerifyMax_upward(parent_idx);
            }else{
                VerifyMin_upward(idx);
            }
        }

    }

    void VerifyMin_downward(int idx){
        // idx had no child
        if(idx * 2 > mmheap.size() - 1){
            return;
        }

        // idx had no grand child
        int child_idx1 = idx * 2;
        int child_idx2 = idx * 2 + 1;
        if(idx * 4 > mmheap.size() - 1){
            int k;
            if(child_idx2 == mmheap.size()){
                k = mmheap[child_idx1];
            }else{
                k = min(mmheap[child_idx1], mmheap[child_idx2]);
            }

            if(k < mmheap[idx]){
                if(k == mmheap[child_idx1]){
                    swap(mmheap[idx], mmheap[child_idx1]);
                }else{
                    swap(mmheap[idx], mmheap[child_idx2]);
                }
            }
            return;
        }

        //int grand_child_idx1 = idx * 4;
        //int grand_child_idx2 = idx * 4 + 1;
        //int grand_child_idx3 = idx * 4 + 2;
        //int grand_child_idx4 = idx * 4 + 3;
        int min_idx = -1;
        int min_val = INT_MAX;
        for(int i=0;i<4;++i){
            int idx_tmp = idx * 4 + i;
            if(idx_tmp == mmheap.size()){
                break;
            }
            if(mmheap[idx_tmp] < min_val){
                min_val = mmheap[idx_tmp];
                min_idx = idx_tmp;
            }
        }

        if(mmheap[idx] > min_val){
            swap(mmheap[idx], mmheap[min_idx]);
            if(mmheap[min_idx / 2] < mmheap[min_idx]){
                swap(mmheap[min_idx / 2], mmheap[min_idx]);
                VerifyMin_downward(min_idx);
            }
        }
    }

    void VerifyMax_downward(int idx){
        // idx had no child
        if(idx * 2 > mmheap.size() - 1){
            return;
        }

        // idx had no grand child
        int child_idx1 = idx * 2;
        int child_idx2 = idx * 2 + 1;
        if(idx * 4 > mmheap.size() - 1){
            int k;
            if(child_idx2 == mmheap.size()){
                k = mmheap[child_idx1];
            }else{
                k = max(mmheap[child_idx1], mmheap[child_idx2]);
            }

            if(k > mmheap[idx]){
                if(k == mmheap[child_idx1]){
                    swap(mmheap[idx], mmheap[child_idx1]);
                }else{
                    swap(mmheap[idx], mmheap[child_idx2]);
                }
            }
            return;
        }

        //int grand_child_idx1 = idx * 4;
        //int grand_child_idx2 = idx * 4 + 1;
        //int grand_child_idx3 = idx * 4 + 2;
        //int grand_child_idx4 = idx * 4 + 3;
        int max_idx = -1;
        int max_val = INT_MIN;
        for(int i=0;i<4;++i){
            int idx_tmp = idx * 4 + i;
            if(idx_tmp == mmheap.size()){
                break;
            }
            if(mmheap[idx_tmp] > max_val){
                max_val = mmheap[idx_tmp];
                max_idx = idx_tmp;
            }
        }

        if(mmheap[idx] < max_val){
            swap(mmheap[idx], mmheap[max_idx]);
            if(mmheap[max_idx / 2] > mmheap[max_idx]){
                swap(mmheap[max_idx / 2], mmheap[max_idx]);
                VerifyMax_downward(max_idx);
            }
        }
    }

    int mmheap_extract_min(){
        int res = mmheap[1];
        swap(mmheap[1], mmheap[mmheap.size()-1]);
        mmheap.pop_back();
        VerifyMin_downward(1);
        return res;
    }

    int mmheap_extract_max(){
        int res = max(mmheap[2], mmheap[3]);
        if(res == mmheap[2]){
            swap(mmheap[2], mmheap[mmheap.size()-1]);
            mmheap.pop_back();
            VerifyMax_downward(2);
        }else{
            swap(mmheap[3], mmheap[mmheap.size()-1]);
            mmheap.pop_back();
            VerifyMax_downward(3);
        }
        return res;
    }

    int mmheap_min_top(){
        return mmheap[1];
    }

    int mmheap_max_top(){
        if(mmheap.size() == 2){
            return mmheap[1];
        }
        if(mmheap.size() == 3){
            return mmheap[2];
        }
        return max(mmheap[2], mmheap[3]);
    }

};

int main(){
	
    minmax_heap heap;
    int a[11] = {2,6,0,7,6,5,1,6,12,14,9};
    vector<int> A(a,a+11);

    for(const auto &b: A){
        heap.mmheap_insert(b);
        heap.print();
    }

    cout << heap.mmheap_extract_max() << endl;
    heap.print();
    cout << heap.mmheap_extract_min() << endl;
    heap.print();
    cout << heap.mmheap_extract_max() << endl;
    heap.print();
    cout << heap.mmheap_extract_min() << endl;
    heap.print();
    cout << heap.mmheap_extract_max() << endl;
    heap.print();


    return 0;
}

```

