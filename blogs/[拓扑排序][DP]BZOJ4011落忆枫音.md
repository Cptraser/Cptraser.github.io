## [HNOI2015]落忆枫音

[题目传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=4011)

*题目大意：给定一个拓扑图，点1只有出度，及一条额外边。求加上那条边以后图中不同形态树的数量。*

假设没有额外边，总方案数就是$\prod_{i=2}^n DegreeIn[i]$

加边以后有一些方案是错的，考虑怎样统计错误方案。

发现对于一条路径$x\rightarrow y$，增加一条$y\rightarrow x$就会形成环，此时减少的方案数为$\prod_{i=2}^nDegreeIn[i],i\notin x\rightarrow y$

表示这条路径上的点已经选定，其他无论怎么选都是错的。

令$f[i]$表示$y\rightarrow i$时错误的方案总数。

$f[y]=\frac{initAns}{DegreeIn[y]}$

$f[i]=\frac{\sum f[j]}{DegreeIn[i]},j对i有边$

*TIPS：*DP中的除法可以理解为求$f[j]$时其实是乘上了$DegreeIn[i]$，但是对于转移到$i$需要除去。

*Code：*

~~~c++
#include <cstdio>
#include <cctype>
#include <cstring>
#include <algorithm>
#define V 100005
#define E 200005
using namespace std;
 
char tc(){static char tr[10000],*A=tr,*B=tr;return A==B&&(B=(A=tr)+fread(tr,1,10000,stdin),A==B)?EOF:*A++;}
void read(int &x){char c;while(!isdigit(c=tc()));for(x=c-48;isdigit(c=tc());x=(x<<1)+(x<<3)+c-48);}
 
const int Mod=1e9+7;
 
long long Ans=1,f[V],Inv[E];
int N,M,S,T,Gi[V],_Gi[V];
int head[V],to[E],nxt[E],Cnt;
#define Add(x,y) (nxt[Cnt]=head[x],to[Cnt]=y,head[x]=Cnt++)
 
void Topo(void){
    static int Q[V];
    int h=0,t=1;f[T]=Ans;
    for(int i=1;i<=N;i++)if(!Gi[i])Q[++t]=i;
    while(h<t){
        int front=Q[++h];
        f[front]=f[front]*Inv[_Gi[front]]%Mod;
        for(int i=head[front];~i;i=nxt[i]){
            (f[to[i]]+=f[front])%=Mod;
            if(!--Gi[to[i]])Q[++t]=to[i];
        }
    }printf("%lld",(Ans-f[S]+Mod)%Mod);
}
 
int main()
{
    read(N),read(M),read(S),read(T),memset(head,-1,sizeof head);
    for(int i=1,x,y;i<=M;i++)read(x),read(y),Add(x,y),Gi[y]++,_Gi[y]++;_Gi[T]++;
    for(int i=2;i<=N;i++)Ans=Ans*_Gi[i]%Mod;Inv[1]=1;
    for(int i=2;i<=M+1;i++)Inv[i]=(Mod-Mod/i)*Inv[Mod%i]%Mod;
    if(T==1)return printf("%lld",Ans),0;
    return Topo(),0;
}
~~~

