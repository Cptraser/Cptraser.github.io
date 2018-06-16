#### 树上启发式合并，Dsu on tree.

一类可以解决**无修改**的子树特征查询的算法***（基于树链剖分的轻重链思想）***。

看下面这样一个问题：

给定一棵树，求每个节点子树内颜色最多的所有节点的颜色和。

这个问题可以用Dsu很好的解决。

首先对该数进行轻重链剖分（一个DFS），然后进行第二个DFS。

**[DFS实现]**

首先先求出每个轻儿子的答案，然后在你记录颜色数量的数组上消除他们的影响。

然后解决重儿子，不清空影响，再将轻儿子的影响添加进去，记录答案。

**[复杂度]**

可以这么考虑：只有DFS到了轻儿子时，才会将轻儿子的贡献合并到上一级重儿子上。

树链剖分将树分割为不超过$log_2N$条重链，每个节点最多向上合并$log_2N$次。

所以整体复杂度O（$Nlog_2N$）。



[**Educational Codeforces Round 2 E**](http://codeforces.com/contest/600/problem/E)

```c++
#include <cstdio>
#include <vector>
using namespace std;
long long read(){
	char c;while(c=getchar(),c<'0'||c>'9');
	long long x=c-'0';while(c=getchar(),c>='0'&&c<='9')x=x*10+c-'0';
	return x;
}

const int Maxn=1e5+5;
long long N,C[Maxn];
vector<long long>A[Maxn];
long long Sz[Maxn],f[Maxn],Son[Maxn];
void dfs1(long long Node,long long fa){
	Sz[Node]=1,f[Node]=fa;
	for(int i=0;i<A[Node].size();i++){
		if(A[Node][i]!=fa){
			dfs1(A[Node][i],Node);
			Sz[Node]+=Sz[A[Node][i]];
			if(Sz[Son[Node]]<Sz[A[Node][i]])
				Son[Node]=A[Node][i];
		}
	}
}
long long vis[Maxn],Mx=0,Ans[Maxn],Cnt[Maxn],Sum=0;
void G(long long Node,long long v){
	Cnt[C[Node]]+=v;
	if(v>0&&Cnt[C[Node]]>=Mx){
		if(Cnt[C[Node]]>Mx)Sum=0,Mx=Cnt[C[Node]];
		Sum+=C[Node];
	}
	for(int i=0;i<A[Node].size();i++){
		if(!vis[A[Node][i]]&&f[Node]!=A[Node][i]){
			G(A[Node][i],v);
		}
	}
}
void dfs2(long long Node,long long Cl){
	for(int i=0;i<A[Node].size();i++){
		if(A[Node][i]!=f[Node]&&A[Node][i]!=Son[Node])
			dfs2(A[Node][i],0);
	}
	if(Son[Node])dfs2(Son[Node],1),vis[Son[Node]]=1;
	G(Node,1);Ans[Node]=Sum;
	if(Son[Node])vis[Son[Node]]=0;
	if(!Cl)G(Node,-1),Mx=Sum=0;
}

int main()
{
	N=read();
	for(int i=1;i<=N;i++)C[i]=read();
	for(int i=1;i<N;i++){
		int x=read(),y=read();
		A[x].push_back(y);
		A[y].push_back(x);
	}
	dfs1(1,0);
	dfs2(1,0);
	for(int i=1;i<=N;i++)printf("%lld ",Ans[i]);
	return 0;
}
```

