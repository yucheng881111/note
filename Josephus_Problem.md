# Josephus Problem

## 題目
n個人圍成一個環，數k個人後殺掉那個人，剩下的人再圍，直到殺到剩下最後一個，最後存活下來的那個人就贏了。給定n,k，求出最後贏的人。

## 公式解
上一個位置，往後數k格，再mod人數。人數從2迭代到n。
```
//公式: f(n,k)=(f(n-1,k)+k)%n
int josephus(int n, int k) { //非遞回版本
	int s = 0; //只有一個人時，一定剩下他(編號0) f(1,k)=0
	for (int i = 2; i <= n; i++){
        s = (s + k) % i;  //上一個位置，往後數k格，再%人數
	}

	return s;
}
int josephus_recursion(int n, int k) { //遞回版本
	return n > 1 ? (josephus_recursion(n - 1, k) + k) % n : 0;
}
```

## 公式證明

https://www.youtube.com/watch?v=Yeh1_2GyS5s

## Uva 440
先將所有n對所有k的表建出來，再從表裡面找比較快。因為1一定會被刪，所以直接把2(目標)當成0來執行。

```
#include<bits/stdc++.h>

using namespace std;
int survivor[150][600];
void josephus(){
    memset(survivor,0,sizeof(survivor));
    for(int n=2;n<150;++n){
        for(int k=1;k<600;++k){
            survivor[n][k]=(survivor[n-1][k]+k)%n;
        }
    }
}

int main(){
    int n;
    josephus();
    while(cin>>n&&n){
        for(int k=1;k<600;++k){
            if(survivor[n-1][k]==0){
                cout<<k<<endl;
                break;
            }
        }
    }


return 0;
}

```