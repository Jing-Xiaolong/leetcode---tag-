[h2pl/leetcode](https://github.com/h2pl/leetcode)的C++实现，附带一些扩充。用于秋招第一遍分tag刷题，查漏补缺，并建立手撸算法的基本手感。



- [算法思想](#算法思想)
  - [二分查找](二分查找)
  - [贪心思想](#贪心思想)

# 算法思想

## 二分查找

[leecode.69 x的平方根 easy](https://leetcode-cn.com/problems/sqrtx/)

```C++
int mySqrt(int x) {
    if(x <= 1)
        return x;
    int low = 1, high = x;
    while(low <= high){
        int mid = (high - low) / 2 + low;
        if(x / mid == mid)
            return mid;
        else if(x / mid < mid)
            high = mid - 1;
        else
            low = mid + 1;
    }
    return high;
}
```

[leetcode.367 有效的完全平方数 easy](https://leetcode-cn.com/problems/valid-perfect-square/)

```C++
bool isPerfectSquare(int num) {
    if(num < 1)
        return false;
    int low = 1, high = num;
    while(low <= high){
        int mid = (high - low) / 2 + low;
        if(num / mid == mid && num % mid == 0)
            return true;
        else if(num / mid < mid)
            high = mid - 1;
        else
            low = mid + 1;
    }
    return false;
}
```

[leetcode.441 排列硬币 easy](https://leetcode-cn.com/problems/arranging-coins/)

```C++
int arrangeCoins(int n) {
    if(n < 1)
        return 0;
    int low = 1, high = n;
    while(low <= high){
        int mid = (high - low) / 2 + low;
        long long total = (mid + 1) * (long long)mid / 2;
        if(total == n)
            return mid;
        else if(total < n)
            low = mid + 1;
        else
            high = mid - 1;
    }
    return high;
}
```

[leetcode.50 Pow(x,n)](https://leetcode-cn.com/problems/powx-n/)

```C++
// 思路：对x依次做1,2,4,...次幂，得到x, x*x, x*x*x*x，直到指数次数为不大于n的最大值
// 保存x*x*x*x...，并从n中减去该指数次数
// 重复操作，将所有x*x...连续乘起来即为所求
double myPow(double x, int n) {
    bool sign = n < 0;
    long long exp = abs((long long)n);  // 先转化为longlong再abs，防止出现n=INT_MIN
    double res = 1.0;
    while(exp > 0){
        long long e = 1;// 指数  1 -> 2   -> 4 ...
        double mul = x; // 结果  x -> x*x -> x*x*x*x ...
        while((e*2) < exp){   // 直到e*2仍小于exp
            e = e << 1;
            mul = mul * mul;
        }
        exp -= e;
        res *= mul;
    }
    return sign ? 1.0/res : res;
}
```

[剑指offer 数字在排序数组中出现的次数](https://www.nowcoder.com/practice/70610bf967994b22bb1c26f9ae901fa2?tpId=13&tqId=11190&tPage=2&rp=2&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```C++
// 思路：二分搜索num+0.5，num-0.5的位置，它们区间内个数即为所求
int biSearch(const vector<int> &data, double num){
    // 对数组data = 1 2 3a 3b 3c 4 4 5
    // num = 2.5 -> 返回2所在下标
    // num = 3.5 -> 返回3c所在下标
    int low = 0, high = data.size() - 1;
    while(low <= high){
        int mid = (high - low) / 2 + low;
        if(data[mid] < num)
            low = mid + 1;
        else
            high = mid - 1;
    }
    return high;
}
int GetNumberOfK(vector<int> data ,int k) {
    return biSearch(data, k + 0.5) - biSearch(data, k - 0.5);
}
```

## 贪心思想

[leetcode.445 分发饼干 easy](https://leetcode-cn.com/problems/assign-cookies/)

```c++
// 思路：从胃口最小的孩子开始，从最小的饼干开始喂直到能满足位置
int findContentChildren(vector<int>& g, vector<int>& s) {
    sort(g.begin(), g.end());
    sort(s.begin(), s.end());
    int cnt = 0;
    int i = 0, j = 0;
    // 从胃口最小的孩子依次开始喂饼干
    while(i < g.size() && j < s.size()){
        if(g[i] <= s[j]){
            ++cnt;
            ++i;
            ++j;
        }else
            ++j;
    }
    return cnt;
}
```

[leetcode.452 用最少数量的箭引爆气球](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/)

```c++
// 思路：左往右依次射箭，每次保证引爆最多的气球
int findMinArrowShots(vector<vector<int>>& points) {
    if(points.size() <= 1)
        return points.size();

    sort(points.begin(), points.end());
    int cnt = 1;
    int rightEdge = points[0][1];   // 第一个圆右侧为右边界
    for(int idx = 1; idx < points.size(); ++idx){
        // 圆在边界内，需更新边界为它们右侧的较小值
        if(points[idx][0] <= rightEdge)  
            rightEdge = min(rightEdge, points[idx][1]);
        // 圆不在边界内，引爆气球++cnt，并更新右边界为新的右侧值
        else{   
            ++cnt;
            rightEdge = points[idx][1];
        }
    }

    return cnt;
}
```

[leetcode.135 分发糖果 hard](https://leetcode-cn.com/problems/candy/)

```c++
// 思路：
// 步骤1. 先每个人发一颗糖果
// 步骤2. 从左往右遍历，i比i-1表现好就在i-1基础上+1糖果
// 步骤3. 从右往左遍历，i比i+1表现好就在i+1基础上+1糖果（需判断已有糖果是否已经比i+1多）
int candy(vector<int>& ratings) {
    vector<int> candies(ratings.size(), 1);

    for(int i = 1; i < candies.size(); ++i)
        if(ratings[i] > ratings[i - 1])
            candies[i] = candies[i - 1] + 1;

    for(int i = candies.size() - 2; i >= 0; --i)
        if(ratings[i] > ratings[i + 1])
            candies[i] = max(candies[i], candies[i + 1] + 1);

    return accumulate(candies.begin(), candies.end(), 0);
}
```



