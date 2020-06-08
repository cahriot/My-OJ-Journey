## 第二章
### 2.1 穷竭搜索
- 递归函数：必须有终止条件;重复调用数可用记忆化搜索或动态规划优化
- 栈: 移除s.pop();插入n s.push(n);取顶n=s.top()
- 队列: 移除que.pop();插入n que.push(n);取顶n=que.front()
- 深度优先搜索DFS O(2^n)
1. 部分和问题
```cpp
bool dfs(int i,int sum){
    if(i==n) return sum==k;
    if(dfs(i+1,sum)) return true;
    if(dfs(i+1,sum+a[i])) return true;
    return false;
}
```
2. 水洼问题：求8连通积水
```cpp
循环至有W的地方开始dfs
void dfs(int x,int y){
    field[x][y]='.';
    for(int dx=-1;dx<=1;dx++){
        for(int dy=-1;dy<=1;dy++){
            int nx=x+dx,ny=y+dy;
            if(0<=nx&&nx<N&&0<=ny&&ny<M&&field[nx][ny]=='W')
            dfs(nx,ny);
        }
    }
    return ;
}
res++;
``` 
- 深度优先算法BFS O(状态数*转移方式)
DFS：栈；BFS: 队列
1. 迷宫最短路径步数
```cpp
int d[MAX_N][MAX_M];//到各个位置最短距离的数组
typedef pair<int,int> P;
int bfs(){
    queue<p> que;
    **初始化前N*M个d[i][j]为INF**
    que.push(P(sx,sy));
    d[sx][sy]=0;//将起点加入队列，并把这一地点的距离置为0

    //不断循环直至队列长度为0
    while(que.size()){
        //从前端取出元素
        P p=que.front();
        que.pop();
        //若取出的状态是终点，则结束搜索
        if(p.first==gx&&p.second==gy) break;

        //四个方向循环
        for(int i=0;i<4;i++){
            int nx=p.first+dx[i],ny=p.second+dy[i];
            if(0<=nx&&nx<N&&0<=ny&&ny<M&&maze[nx][ny]!='#'&&d[nx][ny]==INF){
                que.push(P(nx,ny));
                d[nx][ny]=d[p.first][p.second]+1;
            }
        }
    }
    return d[gx][gy];
}
```
- 建议：遍历使用DFS（递归和状态管理更简单）；最短路径使用BFS（DFS会重复经过同样状态）



### 2.2 贪心算法
### 2.3 记录结果再利用的“动态规划”
DP:Dynamic Programming 动态规划 算法
#### 2.3.1 记忆化搜索与动态规划
- 背包问题
![20191216191508.png](https://i.loli.net/2019/12/16/RLV8laHeYMG2Xyn.png)
1. 穷竭方法
``` cpp
int n, W;
int w[MAX_N], v[MAX_N];
//从第i个物品挑选总重小于j的部分
int rec(int i, int j) {
    int res;
    if (i == n) {
        //已经没有剩余物品了
        res = 0;
    }
    else if (j < w[i]) {
        //无法挑选这个物品
        res = rec (i + 1, j);
    } else {
        //挑选和不挑选的两种情况都尝试一下
        res = max(rec(i + 1, j), rec(i + 1, j - w[i]) + v[i]);
    }
    return res;
}
void solve() {
    printf("%d\n", rec(0, W));
}
```

搜索深度n，且每一层搜索需要两次分支，最坏下O(2^n)时间（n较大就凉了...

- 改进：
记录重复调用的结果，省略第二次以后的重复计算
```cpp
int dp[MAX_N + 1][MAX_W + 1];//记忆化数组
int rec(int i, int j){
    if(dp[i][j]>=0){
        //已经计算过的话直接使用之前的结果
        return dp[i][j];
    }
    int res;
    if(i==n){
        res=0;
    }else if(j<w[i]){
        res=rec(i+1,j);
    }else{
        res=max(rec(i+1,j),rec(i+1,j-w[i])+v[i]);
    }
    //将结果记录在数组中
    return dp[i][j]=res;
}
void solve(){
    //用-1表示尚未计算过，初始化整个数组
    memset(dp,-1,sizeof(dp));
    printf("%d\n",rec(0,W));
}
```
这种微小的改进，只需要O(nW)的复杂度，记忆化搜索

>memset按1字节为单位对内存进行填充，可以0或-1（每一位二进制位都是1），可以快速填充高维数组，但无法初始化成1之类的数值


Another version(终极简洁版O(nW))

```cpp
int dp[MAX_N+1][MAX_W+1];//DP数组
void solve(){
    for(int i = n-1;i>=0;i--){
        for(int j=0;j<W;j++){
            if(j<w[i]){
                dp[i][j]=dp[i+1][j];
            }else{
                dp[i][j]=max(dp[i+1][j],dp[i+1][j-w[i]]+v[i])
            }
        }
    }
    printf("%d\n",dp[0][W]);
}
```
- 全局数组内容会被初始化为0

- ps：发现在笔记中敲代码实在太耗时间了...了解书也不够深入；有人整理了本书的题目网站，准备在那打代码练习，笔记还是就记录思路和一些细节吧...

### 2.4 加工并存储数据的数据结构
#### 2.4.1 树和二叉树
1. 节点、边、根、叶子
2. 父亲、兄弟、儿子