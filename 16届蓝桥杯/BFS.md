## 走迷宫

```c
#include<iostream>
#include<cstring>
#include<algorithm>
#include<queue>

#define x first
#define y second

using namespace std;

typedef pair<int, int> PII;
const int N = 110;

int g[N][N];
int n, m;
int d[N][N];
int dx[] = {-1, 0, 1, 0};
int dy[] = {0, 1, 0, -1};

int bfs(){
    memset(d, -1, sizeof d);
    queue<PII> q;
    d[0][0] = 0;
    q.push({0, 0});
    
    while(q.size()){
        auto t = q.front();
        q.pop();
        // int tx = t.x, ty = t.y;
        
        for(int u = 0; u < 4; u ++){
            int tx= t.x + dx[u], ty = t.y + dy[u];
            if(tx >= 0 && tx < n && ty >= 0 && ty < m && d[tx][ty] == -1 && !g[tx][ty]){
                d[tx][ty] = d[t.x][t.y] + 1;
                q.push({tx, ty});
                
            }
        }
    }
    return d[n - 1][m - 1];
}

int main(){
    cin >> n >> m;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
            cin >> g[i][j];
            
    cout << bfs();
    
}
```

