# 執行時間測量
用兩個clock()將要測量的範圍包起來。

```
#include<bits/stdc++.h>

using namespace std;

int main() {
    double s=clock();
    for(int i=0;i<1000000000;++i){

    }
    double e=clock();
    cout<<"time: "<<e-s<<" ms"<<endl;

    return 0;
}

```