# Competitive Programming C++ format

```
#include <bits/stdc++.h>
using namespace std;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    
    
    return 0;
}
```

1. 在sync_with_stdio(false)後將無法使用printf(),scanf()
2. endl清空緩衝區很耗時間，可用\n取代。但若配合while迴圈輸入，則輸出時必須用endl將緩衝區輸出，否則將一直卡在輸入狀態
```
while(cin>>a){
    ......
    cout<<a<<endl; //endl為必須
}
```



