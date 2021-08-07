# Monotonic Stack

## 特性
1. Monotonic Stack 內的元素一定維持 monotonic 特性 (單調遞增或單調遞減) 。
2. 每個元素都會加入 stack ，如果加入新元素會破壞 monotonic ，就必須將 stack 頂端元素移除，不斷移除 stack 頂端元素直到不違反 monotonic ，接著再加入元素。
3. Monotonic Stack 可以幫助你在時間複雜度O(n)內找到陣列中所有元素向左/向右走訪第一個比當前元素小/大的元素，左/右跟小/大總共有四種組合。

## 例題

### leetcode problem 84: Largest Rectangle in Histogram

題: 找出直方圖圍出的長方形面積最大值。

![](https://i.imgur.com/WcbX2Do.png)

解: 維持一個單調遞增的monotonic stack。stack裡儲存index而不是height。對於每個直方，我們只要找出它的左界和右界，就能求得面積height[i] * (R[i] - L[i] + 1)。

![](https://i.imgur.com/dtMOU9O.jpg)


如上圖，以index 4為基準，其高度是4，可以左移3格、右移3格。左界為1、右界為7，面積為4 x ( 7 - 1 + 1 ) = 28。

尋找i之左界時，若stack為空，直接令L[i] = 0；若不為空，則取top值比較其height。若height[i] > stack.top()，將i加入並不會破壞monotonic stack單調遞增的特性，因此直接把i加入，並令L[i] = stack.top() + 1。但若height[i] <= stack.top()，就必須將元素從stack中pop出來，直到height[i]可以加入為止。並也要令L[i] = stack.top() + 1。如此一來就可以找到所有元素的左界。而右界就只要把陣列倒過來，套用一樣的流程，就可以求出。

![](https://i.imgur.com/YboZ3Tg.jpg)



```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int len = heights.size();
        vector<int> L_edge(len,-1);
        vector<int> R_edge(len,-1);
        stack<int> s;
        s.push(0);
        L_edge[0] = 0;
        for(int i=1;i<len;++i){
            while(!s.empty() && heights[i] <= heights[s.top()]){
                s.pop();
            }
            if(s.empty()){
                L_edge[i] = 0;
            }else{
                L_edge[i] = s.top() + 1;
            }
            s.push(i);
        }
        
        stack<int> s2;
        s2.push(len - 1);
        R_edge[len-1] = len - 1;
        for(int i=len-2;i>=0;--i){
            while(!s2.empty() && heights[i] <= heights[s2.top()]){
                s2.pop();
            }
            if(s2.empty()){
                R_edge[i] = len - 1;
            }else{
                R_edge[i] = s2.top() - 1;
            }
            s2.push(i);
        }
        
        int m = -1;
        for(int i=0;i<len;++i){
            m = max(heights[i] * (R_edge[i] - L_edge[i] + 1), m);
        }
        return m;
    }
};
```

### leetcode problem 42: Trapping Rain Water

題:
![](https://i.imgur.com/lWqWuVg.png)

解: 維持一個遞減的stack，這題不需要單調遞減。對於每一個bar，只需要找到上一個大於或等於他的bar就行(代表中間能集水)。雖然我用pair同時存了height和index，但這題可以像上題一樣存index就行。

```
class Solution {
public:
    int trap(vector<int>& height) {
    int len = height.size();
    int ind = 0;
    for(int i=0;i<len;++i){
        if(height[i] != 0){
            ind = i;
            break;
        }
    }
    vector<int> h;
    h.assign(height.begin() + ind, height.end());
    len = h.size();
    if(len == 0){
        return 0;
    }
    int prev[len];
    for(int i=0;i<len;++i){
        prev[i] = -1;
    }
    stack<pair<int,int>> s;
    s.push({h[0],0});

    for(int i=1;i<len;++i){
        if(s.top().first >= h[i]){
            s.push({h[i],i});
        }else{
            pair<int,int> pre;
            while(s.top().first < h[i]){
                pre = s.top();
                s.pop();
                if(s.empty()){
                    break;
                }
            }
            if(s.empty()){
                prev[i] = pre.second;
            }else{
                prev[i] = s.top().second;
            }
            s.push({h[i],i});
        }
    }

    int ans = 0;
    for(int i=len-1;i>=0;--i){
        if(prev[i] != -1){
            int h1 = min(h[i], h[prev[i]]);
            int water = h1 * (i - prev[i] + 1);
            for(int j=prev[i];j<=i;++j){
                if(h[j] > h1){
                    water -= h1;
                }else{
                    water -= h[j];
                }
            }

            ans += water;
            i = prev[i] + 1;
        }

    }

    return ans;
    }
};
```
