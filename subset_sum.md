# Subset Sum

![](https://i.imgur.com/1ssWz5k.jpg)


```
#include<bits/stdc++.h>

using namespace std;
// see that if array [a] can choose some of the number that their sum is y.
// dynamic programming, bitset
int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    const int N=6;
    int a[N]={1,3,4,6,6,9};
    const int y=27;
    bitset<y+1> b(0);
    b[a[0]]=1;

    for(int i=1;i<N;++i){
        b|=(b<<a[i]);
        if(b[a[i]]==0){
            b[a[i]]=1;
        }
    }
    cout<<b<<endl;
    if(b[y]==1){
        cout<<"yes"<<endl;
    }else{
        cout<<"no"<<endl;
    }





return 0;
}

```