# 矩陣快速冪
費式數列為例:

![](https://i.imgur.com/mzc9xJb.png =10%x)![](https://i.imgur.com/wdqAr7F.png =15%x)=![](https://i.imgur.com/bwgPf37.png =15%x)

f(0)=1; f(1)=1; f(2)=2; f(3)=3; f(4)=5...
n=1 代入得到f(2)=2為左乘狀態轉移矩陣一次。
求f(x)必須左乘狀態轉移矩陣x-1次: 
![](https://i.imgur.com/g1qW0TD.png =15%x)![](https://i.imgur.com/dZmzvO6.png =4%x)=![](https://i.imgur.com/xvtKfNl.png =15%x)

若轉移方程出現常數，則轉移矩陣必須多一個維度的常數項。
ex: f(x+1) = a * f(x) + b
則:
![](https://i.imgur.com/BL0Q8FZ.jpg)


## Example
f(x+1)=2f(x)+4f(x-1); f(0)=1, f(1)=4
答案mod1000000007

```
#include<bits/stdc++.h>

using namespace std;

void mul(vector<vector<long long>> &a,vector<vector<long long>> &b,vector<vector<long long>> &ans){
    //size=2x2
    vector<vector<long long>> temp(2,vector<long long>(2,0));
    for(int i=0;i<2;++i){
        for(int j=0;j<2;++j){
            for(int k=0;k<2;++k){
                temp[i][j]+=(a[i][k]*b[k][j])%1000000007;
            }
        }
    }
    for(int i=0;i<2;++i){
        for(int j=0;j<2;++j){
            temp[i][j]%=1000000007;
        }
    }
    ans=temp;
}

vector<vector<long long>> fastpow(long long n){
    vector<vector<long long>> base(2,vector<long long>(2,0));
    base[0][0]=2;base[0][1]=4;
    base[1][0]=1;base[1][1]=0;
    vector<vector<long long>> ans(2,vector<long long>(2,0));
    ans[0][0]=1;ans[0][1]=0;
    ans[1][0]=0;ans[1][1]=1;
    while(n!=0){
        if(n&1){
            mul(base,ans,ans);
        }
        mul(base,base,base);
        n>>=1;
    }
    return ans;
}

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    long long n;
    cin>>n;
    if(n==1){
        cout<<"1"<<endl;
        return 0;
    }else if(n==2){
        cout<<"4"<<endl;
        return 0;
    }

    vector<vector<long long>> vec=fastpow(n-2);

    long long sum=vec[0][0]*4+vec[0][1]*1;
    cout<<sum%1000000007;




return 0;
}

```






