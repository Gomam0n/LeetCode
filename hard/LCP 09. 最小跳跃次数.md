为了给刷题的同学一些奖励，力扣团队引入了一个弹簧游戏机。游戏机由 N 个特殊弹簧排成一排，编号为 0 到 N-1。初始有一个小球在编号 0 的弹簧处。若小球在编号为 i 的弹簧处，通过按动弹簧，可以选择把小球向右弹射 jump[i] 的距离，或者向左弹射到任意左侧弹簧的位置。也就是说，在编号为 i 弹簧处按动弹簧，小球可以弹向 0 到 i-1 中任意弹簧或者 i+jump[i] 的弹簧（若 i+jump[i]>=N ，则表示小球弹出了机器）。小球位于编号 0 处的弹簧时不能再向左弹。

为了获得奖励，你需要将小球弹出机器。请求出最少需要按动多少次弹簧，可以将小球从编号 0 弹簧弹出整个机器，即向右越过编号 N-1 的弹簧。

## 示例 1：

输入：jump = [2, 5, 1, 1, 1, 1]

输出：3

解释：小 Z 最少需要按动 3 次弹簧，小球依次到达的顺序为 0 -> 2 -> 1 -> 6，最终小球弹出了机器。

## 限制：

1 <= jump.length <= 10^6
1 <= jump[i] <= 10000

## 解法一：bfs
注意记录已被更新过的最大下标以保证时间复杂度为O（n）
```cpp
class Solution {
public:
    int minJump(vector<int>& jump) {
        int n = jump.size();
        vector<int> v(n,n+1);
        int last = 0,cnt = 0;
        queue<int>q;
        q.push(0);
        while(!q.empty()){
            cnt++;
            for(int i = q.size();i>0;i--){
                int idx = q.front();
                q.pop();
                if(idx+jump[idx]>=n)
                    return cnt;
                else if(v[jump[idx]+idx]==n+1){
                    q.push(jump[idx]+idx);
                }
                if(idx>last+1){
                    for(int j = last+1;j<idx;j++){
                        if(v[j]==n+1)
                            q.push(j);
                    }
                    last = max(last,idx);
                }
                v[idx] = cnt-1;
            }
            
        }
        return 0;
    }
};

```
## 解法一：动态规划
到达一个点的最少跳跃次数来自两种可能：从左往右直接跳到，或是从右往左跳回来。直接跳到的点很容易更新，想要知道从右往左跳回来的最少次数，我们需要记录最少需要几次跳跃才能超过这个点。因此除了dp数组外，我们另外需要维护一个数组用来记录w次跳跃最远可以跳到什么位置。
```cpp
class Solution {
public:
    int minJump(vector<int>& jump) {
        int n = jump.size();
        vector<int> dp(n+1,n+1);
        vector<int>maxdis(n,0);
        int w= 0;
        int res = n+1;
        dp[0] = 0;
        for(int i = 0;i<n;i++){
            // we need to make sure maxdis[w]>i ,in order to jump from right to left, it may be strange when i=0
            if(i==maxdis[w])
                w++;
            dp[i] = min(dp[i],w+1);
            int next = i+jump[i];
            if(next>=n){
                res = min(res,dp[i]+1);
            }else{
                dp[next] = min(dp[next],dp[i]+1);
                maxdis[dp[next]] = max(next, maxdis[dp[next]]); 
            }
        }    
        return res;    
    }
};
```