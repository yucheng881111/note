# Karatsuba Algorithm

X=XhB+Xl
Y=YhB+Yl
where Xl<B,Yl<B and Y<=X<(B^2)

XY=XhYhB^2+XlYlB+XhYhB+XlYl
Z=XY=ZhB^2+ZmB+Zl

Zh=XhYh
Zm=XlYh+XhYl
Zl=XlYl

Zm=(Xh+Xl)(Yh+Yl)-XhYh-XlYl=(Xh+Xl)(Yh+Yl)-Zh-Zl

## Complexity
O(n^(lg3)) = O(n^(1.59))

## Pseudo code
```
procedure karatsuba(num1, num2)
  if (num1 < 10) or (num2 < 10)
    return num1*num2
  /* calculates the size of the numbers */
  m = max(size_base10(num1), size_base10(num2))
  m2 = m/2
  /* split the digit sequences about the middle */
  high1, low1 = split_at(num1, m2)
  high2, low2 = split_at(num2, m2)
  /* 3 calls made to numbers approximately half the size */
  z0 = karatsuba(low1,low2)
  z1 = karatsuba((low1+high1),(low2+high2))
  z2 = karatsuba(high1,high2)
  return (z2*10^(2*m2))+((z1-z2-z0)*10^(m2))+(z0)
```
## Example
x=1234=12x100+34
y=5678=56x100+78
Zh=12x56=672
Zl=34x78=2652
Zm=(12+34)x(56+78)-Zh-Zl=2840

xy=672x10000+2840x100+2652=7006652


## Use in polynormial
將上述方法推廣至多項式

![](https://i.imgur.com/ZOeexzZ.jpg)

程式實作時，將n設為高於a,b的degree的2的冪次並遞迴重複執行，至n=1時return。用陣列來存多項式的係數，index為degree。例: a=1+x^2，則陣列a[]={1,0,1}。c++時用vector來存。注意axb的degree會是2n。


```
#include<bits/stdc++.h>

using namespace std;

const int Size=16;

void add(vector<int> &a,vector<int> &b){
    for(int i=0;i<a.size();++i){
        a[i]=a[i]+b[i];
    }
}

void sub(vector<int> &a,vector<int> &b){
    for(int i=0;i<a.size();++i){
        a[i]=a[i]-b[i];
    }
}

void mul(vector<int> &a,vector<int> &b,vector<int> &store){
    if(a.size()==1){
        store[0]=a[0]*b[0];
        store[1]=0;
        return ;
    }
    int n=a.size()/2;
    vector<int> ah(n),bh(n),al(n),bl(n);
    for(int i=0;i<n;++i){
        ah[i]=a[i+n];
        bh[i]=b[i+n];
        al[i]=a[i];
        bl[i]=b[i];
    }
    vector<int> ahbh(n*2),albl(n*2);
    mul(ah,bh,ahbh);
    mul(al,bl,albl);

    add(ah,al); //ah=ah+al
    add(bh,bl); //bh=bh+bl
    vector<int> c(n*2);
    mul(ah,bh,c); //c=(ah+al)*(bh+bl)
    sub(c,ahbh); //c=c-ahbh
    sub(c,albl); //c=c-albl

    for(int i=0;i<n*2;++i){
        store[i]+=albl[i];
    }
    for(int i=0;i<n*2;++i){
        store[i+n]+=c[i];
    }

    for(int i=0;i<n*2;++i){
        store[i+n*2]+=ahbh[i];
    }
}

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    int a[Size]={1,0,3,0,0,5,0,0,0,6,0,0,0,0,0,0};
    int b[Size]={0,1,0,4,0,0,0,7,10,0,0,0,0,0,0,0};
    //Let a,b size be the smallest power of 2, so we can easily split a into ah and bh.
    vector<int> vec1(a,a+Size);
    vector<int> vec2(b,b+Size);
    vector<int> ans(2*Size);
    mul(vec1,vec2,ans);

    for(int i=0;i<18;++i){ //最高次項為x^17
        cout<<ans[i]<<" ";
    }

return 0;
}

```

## Example
### NCTUOJ 853 Dragon Raiders
#### 題目:
給兩排數字，再給一堆qi，問第一排數字和第二排數字各選一個，可以組出幾個qi。 (i=1,2,3......)
#### 解:
暴力法需要O(n^2) ，會TLE。用Karatsuba只需要O(n^1.59) 。把給的兩排數字分別當成a和b兩多項式的X次方，axb的係數便會是答案。例: a: 0,1,1,2,3,3,3,3->1 +2X +X^2 +4X^3。
```
#include<bits/stdc++.h>

using namespace std;

void add(vector<long long> &a, vector<long long> &b, vector<long long> &res) {
    for (int i = 0; i < a.size(); i++)
        res[i] = a[i] + b[i];
}

void sub(vector<long long> &a, vector<long long> &b, vector<long long> &res) {
    for (int i = 0; i < a.size(); i++)
        res[i] = a[i] - b[i];
}

void mul(vector<long long> &a, vector<long long> &b, vector<long long> &res) {
    if (a.size() <= 64) {
        for (int i = 0; i < a.size(); i++)
            for (int j = 0; j < b.size(); j++)
                res[i+j] += a[i]*b[j];
        return;
    }
    int n = a.size();
    int h = n >> 1;
    vector<long long> ah(h), al(h), bh(h), bl(h);
    for (int i = 0; i < h; i++) {
        ah[i] = a[h+i];
        al[i] = a[i];
        bh[i] = b[h+i];
        bl[i] = b[i];
    }
    vector<long long> ahbh(n), albl(n), c(n);
    mul(ah, bh, ahbh);
    mul(al, bl, albl);
    // make ah to store ah+al and bh to bh+bl
    add(ah, al, ah);
    add(bh, bl, bh);
    mul(ah, bh, c);
    sub(c, ahbh, c);
    sub(c, albl, c);
    for (int i = 0; i < n; i++) res[i] += albl[i];
    for (int i = 0; i < n; i++) res[n+i] += ahbh[i];
    for (int i = 0; i < n; i++) res[h+i] += c[i];
}

int main() {
    int n, m, q;
    scanf("%d%d%d",&n,&m,&q);
    vector<long long> a(65536), b(65536), ans(1<<17);
    for (int i = 0; i < n; i++) {
        int x;
        scanf("%d",&x);
        a[x]++;
    }
    a[0]++;
    for (int i = 0; i < m; i++) {
        int x;
        scanf("%d",&x);
        b[x]++;
    }
    b[0]++;
    mul(a, b, ans);
    for(int i = 0; i < q; i++) {
        int x;
        scanf("%d",&x);
        printf("%lld\n", x > 100000 ? 0LL : ans[x]);
    }
    return 0;
}

```











