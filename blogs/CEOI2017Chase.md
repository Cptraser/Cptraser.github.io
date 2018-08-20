## CEOI2017Chase

*HHHOJ#244 Park*

[题面传送门](https://loj.ac/problem/2485)

我们开两个DP数组,$f[i][j]$和$g[i][j]$分别表示从子树到$i$点，放了$j$次磁铁和从$i$点到子树内某点，放了$j$次磁铁的最大差异值。

同时考虑一条路径上的差异值贡献，推下式子可知是路径上点相连的所有点的权值和减去路径的权值（视DP方程情况而定）。

然后我们一边DFS一边用已知的$f$更新未知的$g$，当然还有反着做一遍。

~~~C++
#include <cstdio>
#include <cctype>
#include <vector>
#include <algorithm>
using namespace std;

char tc(){static char tr[100000],*A=tr,*B=tr;return A==B&&(B=(A=tr)+fread(tr,1,100000,stdin),A==B)?EOF:*A++;}
void read(int &x){char c;for(;!isdigit(c=tc()););for(x=c-48;isdigit(c=tc());x=(x<<1)+(x<<3)+c-48);}
void read(int &x,int &y){read(x),read(y);}

const int S=1e5+5;
int N,V,P[S],x,y;
long long Sum[S],Ans,f[S][105],g[S][105];
vector<int>A[S];
void dfs(int x,int fa){
	for(int i=1;i<=V;i++)f[x][i]=Sum[x],g[x][i]=Sum[x]-P[fa];
	for(auto &to:A[x])if(fa^to){
		dfs(to,x);
		for(int i=1;i<V;i++)Ans=max(Ans,f[x][i]+g[to][V-i]);
		for(int i=1;i<=V;i++){
			f[x][i]=max(f[x][i],max(f[to][i],f[to][i-1]+Sum[x]-P[to]));
			g[x][i]=max(g[x][i],max(g[to][i],g[to][i-1]+Sum[x]-P[fa]));
		}
	}
	reverse(A[x].begin(),A[x].end());
	for(int i=1;i<=V;i++)f[x][i]=Sum[x],g[x][i]=Sum[x]-P[fa];
	for(auto &to:A[x])if(fa^to){
		for(int i=1;i<V;i++)Ans=max(Ans,f[x][i]+g[to][V-i]);
		for(int i=1;i<=V;i++){
			f[x][i]=max(f[x][i],max(f[to][i],f[to][i-1]+Sum[x]-P[to]));
			g[x][i]=max(g[x][i],max(g[to][i],g[to][i-1]+Sum[x]-P[fa]));
		}
	}
	Ans=max(Ans,max(f[x][V],g[x][V]));
}
int main()
{
	read(N,V);
	for(int i=1;i<=N;i++)read(P[i]);
	for(int i=1;i<N;i++)read(x,y),A[x].push_back(y),A[y].push_back(x),Sum[x]+=P[y],Sum[y]+=P[x];
	return dfs(1,0),printf("%lld",Ans),0;
}
~~~

