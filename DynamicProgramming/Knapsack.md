

## REsources

https://stackoverflow.com/questions/17246670/0-1-knapsack-dynamic-programming-optimization-from-2d-matrix-to-1d-matrix

https://atcoder.jp/contests/dp/tasks


## Given capacity N and a set K, maximize or count number of ways to fill N.

There are 𝑁 items. The 𝑖-th item has weight 𝑤𝑖 (positive) and value 𝑣𝑖. 
Find a set 𝑆 such that ∑𝑖∈𝑆 (𝑤𝑖≤𝐶) and ∑𝑖∈𝑆(𝑣𝑖) is maximized.

Two variants:
1. unbounded - same item can be picked any number of times
2. bounded - each item can be picked exactly once.

## 0-1 knapsack

you can either pick or ignore an item completely. No fractional stuff allowed.

Base cases:
1. you can always create a capacity of 0 by choosing no coins/items to 
fill the knapsack = 1 way

### Grid size is weight + 1 (W+1) because we want to fill table for 0 .. W

e.g.
```cpp
int dp[N][W+1];
```

Recursive knapsack (with memo):
```cpp
#include<cstdio>
#include<algorithm>
#include<vector>

int N, W;
long long dp[101][100001];// limits are N+1 x W+1, where N is no of items, W is possible weights

// recursive knapsack
long long solveDP(int itemId, int remW, std::vector<std::pair<int, int>>& items){
    // printf("itemId = %d remW = %d\n", itemId, remW);
    // base case
    if(itemId == N || remW <= 0) {
        return 0ll;
    }

    // 
    if(dp[itemId][remW] != -1ll) {
        // printf("found in memo: %d %d\n", itemId, remW);
        return dp[itemId][remW];
    }

    // case where cannot fit
    if(items[itemId].first > remW) {
        long long ans = solveDP(itemId+1, remW, items);
        dp[itemId][remW] = ans;
        return ans;
    }
    // case where we pick and don't pick
    long long ans = std::max(
        // not picked
        solveDP(itemId+1, remW, items),
        // picked
        items[itemId].second + solveDP(itemId+1, remW - items[itemId].first, items)
    );
    dp[itemId][remW] = ans;
    // printf("Final return ans = %lld for itemId = %d, remW = %d\n", ans, itemId, remW);
    return ans;
}

int main(){
    scanf("%d %d", &N, &W);
    std::vector<std::pair<int,int>> items;
    memset(dp, -1ll, sizeof dp);
    for(int i=0;i < N; i++) {// items are indexed from 0 to 99
        int wi, vi;
        scanf("%d %d", &wi, &vi);
        items.push_back({ wi, vi});
    }
    printf("%lld", solveDP(0, W, items));
    return 0;
}
```

Tabulation based knapsack (1-indexed table for dp-table and 0 to W for weight):
So `dp[N+1][W+1]`

```cpp
#include<cstdio>
#include<algorithm>
#include<vector>

int N, W;
// N * W table
// 1-indexed tabulation, items will be considered from 1 to N
// Weights will be considered from 0 to W, so total W+1 needed.
long long dp[101][100001];// limits are N+1 x W+1, where N is no of items, W is possible weights

// tabulated knapsack
long long solveDP(std::vector<std::pair<int, int>>& items){
    // base case 1 - N items allowed to pick,but 0 weight constraint
    for(int i=0;i<N;i++) {
        dp[i][0] = 0;// 0 because there is no weight is allowed and each item weighs something positive
    }

    // base case 2 - 0 items allowed to pick, all possible weights
    for(int j=0;j<=W;j++) {
        dp[0][j] = 0;
    }
    
    // dp is 1-indexed, items is 0-indexed
    for(int i=1;i<=N;i++) {// outer loop on items
        for(int j=1;j<=W;j++) {// inner loop on weights
            // we are now considering item i for upto J weight
            if(items[i-1].first > j) {// cannot fit coz weight of item under consideration is larger than capacity, cannot be picked
                dp[i][j] = dp[i-1][j];// not picking ith item, get from upper row i.e. previous items
            } else {
                dp[i][j] = std::max(
                    dp[i-1][j],// not picking i'th item, get from upper row previous items
                    items[i-1].second + dp[i-1][j-items[i-1].first]// picking ith item, remaining weight was filled with previous items
                );
            }
        }
    }

    return dp[N][W];
}

int main(){
    scanf("%d %d", &N, &W);
    std::vector<std::pair<int,int>> items;// items is 0-indexed collection of items.
    memset(dp, 0ll, sizeof dp);// in tabulation init all with 0
    for(int i=0;i < N; i++) {// items are indexed from 0 to 99
        int wi, vi;
        scanf("%d %d", &wi, &vi);
        items.push_back({ wi, vi});
    }
    printf("%lld\n", solveDP(items));
    return 0;
}
```

## One dimensional 0-1 knapsack

Outer items loop is same - 1 to N.
Inner weights loop is reversed i.e. from W down to 0.



## Unbounded knapsack

Repitition of picking of items is allowed

There are 𝑁 items. The 𝑖-th item has weight 𝑤𝑖 and value 𝑣𝑖. 
Find a multiset 𝑆 such that ∑𝑖∈𝑆 (𝑤𝑖≤𝐶) and ∑𝑖∈𝑆(𝑣𝑖) is maximized.

### Integer partition problem (Same as coin change ways)

Count no. of partitions e.g.
no. partitions of 5 = 7
1 + 1 + 1 + 1 + 1
1 + 1 + 1 + 2
1 + 1 + 3
1 + 4
1 + 2 + 2
2 + 3
5 + 0

A variation on unbounded knapsack. a 2-d dp on all targets vs items/coins.
We are looking for all multiset S such that sum of elements in multi set equals Target C.


## Subset sum

There are 𝑁 items. The 𝑖-th item has weight 𝑤𝑖. 
Find a set 𝑆 such that ∑𝑖∈𝑆(𝑤𝑖=𝐶)

It is variant of knapsack where we are looking for subset that sums exact weight C.
Another variant: count such subsets which add up to weight C.
Another variant: canSum -> returning true/false if a subset exists that can sum to weight C.