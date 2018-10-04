## 奥义商店

*from* *BZOJ* *NOI十连测* *R1T1*

题目大意：给定$N$个物品，每个物品的价格是$V_i$，同时给定$t$种颜色，给定一个下标$k$，和一个方差$d$，让你给所有物品染色，每种颜色必须染$C_i$个，同时第$k$个物品可以染任意一种（不包括在$C_i$内），即$\sum_{i=1}^n C_i=N-1$。求包含$k$的与$k$同色的、下标与$k$构成等差数列且方差为$d$的期望花费（$V_i$），同时支持单点修改$V_i$的值。

$Example：$

$Input:$
> 3 3
> 1 1 1
> 2 2 1 1 1 1//2 t k d接下来t个整数表示$C_i$
> 1 2 2//1 x y表示将$V_x$修改为$y$
> 2 2 3 1 1 1

$Output：$
> 1.5000
> 2.0000

$Solution:$

考虑$O(N^2)$的做法，对于一个点的期望值可推得（这里以$k$右半部分为例，注意左右被选概率相等，对于一个点Q为等差数列上的点，令$P$为最小的$C_i$）为$V_Q*\prod_{i=1且k+i*d\leq Q}\frac{P-i+1}{N-i}$，就可以用期望的线性性质求解。

看题目，由于题目只要求到小数点后4位，当上面的$\prod$里的$i$逐渐增大，连乘的结果会以指数级递减。

故当$i>100$时就没必要继续进行下去。（$Tips$:不一定必须要是100）。

*Code：*

~~~c++
#include <cstdio>
#include <cctype>
#include <cstring>
#include <algorithm>
#define Tort 300
using namespace std;

char tc(){static char tr[10000],*A=tr,*B=tr;return A==B&&(B=(A=tr)+fread(tr,1,10000,stdin),A==B)?EOF:*A++;}
void read(int &x){char c;int y=1;for(;!isdigit(c=tc())&&c!='-';);for(c=='-'?y=-1:x=c-48;isdigit(c=tc());x=(x<<1)+(x<<3)+c-48);x*=y;}

int N,M,V[100005];
double Ans,Sum[Tort][Tort],f[Tort];
int main()
{
 	freopen("lzz.in","r",stdin);
 	freopen("lzz.out","w",stdout);
	read(N),read(M);
	for(int i=1;i<=N;i++){read(V[i]);for(int j=1;j<Tort;j++)Sum[j][i%j]+=V[i];}
	for(int i=1,x,y,t,k,d;i<=M;i++){
		int opt;read(opt);
		if(opt==1){
		    read(x),read(y);
		    for(int j=1;j<Tort;j++)Sum[j][x%j]+=y-V[x];V[x]=y;
		}
		else{
			double P=1;
			read(t),read(k),read(d),x=N,Ans=V[k];
			if(t==1){
				read(x);
				if(d>=Tort){Ans=V[k];
					for(int j=k+d;j<=N;j+=d)Ans+=V[j];
					for(int j=k-d;j>=1;j-=d)Ans+=V[j];
					printf("%.6lf\n",Ans);
				}
				else printf("%.6lf\n",Sum[d][k%d]); 
				continue;
			}Ans=V[k];
			for(int j=1;j<=t;j++)read(y),x=min(x,y);int Pl=min(x,100);
			f[0]=1;for(int j=1;j<=Pl;j++)f[j]=f[j-1]*(1.0*(x-j+1)/(N-j));
			for(int j=k+d,l=1;j<=N&&l<=Pl;j+=d,l++)Ans+=f[l]*V[j];
			for(int j=k-d,l=1;j>=1&&l<=Pl;j-=d,l++)Ans+=f[l]*V[j];
			printf("%.6lf\n",Ans);
		}
	}
}
~~~

