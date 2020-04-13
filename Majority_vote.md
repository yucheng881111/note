# Boyer-Moore majority vote algorithm

## Problem
Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.  The algorithm should run in linear time and in O(1) space.

### Solution
決鬥法: 只有一個元素佔超過一半的量。遍歷陣列一次，把值和次數存起來，要是下一個元素值和目前的值不一樣，就把次數減1(殺死)，反之加1(累積)。要是次數等於0，就把值換成下一個元素。
Example:
array=[2,2,3,3,3,2,2,2,1]
決鬥過程:
value = 2 2 2 2 3 3 2 2 2
times = 1 2 1 0 1 0 1 2 1

經過一番廝殺後，存活下來的是2，因此答案為2。
原理: **要是有一個元素的量超過一半，那他一定承受得起這種消耗。**

```
// leetcode problem 169
int majorityElement(vector<int>& nums) {
    int ans=INT_MIN;
    int times=0;
    for(int i=0;i<nums.size();++i){
        if(times==0){
            ans=nums[i];
            times++;
        }else{
            if(nums[i]==ans){
                times++;
            }else{
                times--;
            }
        }
    }
    return ans;
}
```

## Extension Problem
Given an integer array of size n, find all elements that appear more than ⌊ n/3 ⌋ times. The algorithm should run in linear time and in O(1) space.

### Solution
一樣決鬥法，只是這次要決鬥出前兩多的元素，因為超過1/3的元素有可能有兩個，在這題裡面也可能沒有。因此要遍歷陣列兩次，第一次先決鬥，第二次統計這兩個元素的個數，如果超過n/3，就放入答案陣列。因為只需遍歷兩次和儲存兩個元素的值和個數，時間複雜度為2n=O(n)，空間複雜度為O(1)，符合題目需求，也絕對比排序或統計所有數字還快。

```
// leetcode problem 229
vector<int> majorityElement(vector<int>& nums) {
    vector<int> ans;
    long long v1=LLONG_MIN,v2=LLONG_MIN;
    int t1=0,t2=0;
    for(int i=0;i<nums.size();++i){
        if(v1==nums[i]){
            t1++;
            continue;
        }
        if(v2==nums[i]){
            t2++;
            continue;
        }

        if(t1==0){
            v1=nums[i];
            t1++;
            continue;
        }   
        if(t2==0){
            v2=nums[i];
            t2++;
            continue;
        }
        t1--;
        t2--; 
    }

    int c1=0,c2=0;
    for(int i=0;i<nums.size();++i){
        if(nums[i]==v1){
            c1++;
        }
        if(nums[i]==v2){
            c2++;
        }
    }
    if(c1>(int)nums.size()/3){
        ans.push_back(v1);
    }
    if(c2>(int)nums.size()/3){
        ans.push_back(v2);
    }


    return ans;
}
```






