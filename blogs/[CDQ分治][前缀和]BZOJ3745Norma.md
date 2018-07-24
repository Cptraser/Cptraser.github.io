### BZOJ3745:[Coci2015]Norma

[题目传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=3745)

首先考虑暴力，复杂度O(N^2)。

题目叫我们求$ \sum_{i=1}^{n}\sum_{j=i}^{n}(j-i+1)*Min(A_i,A_{i+1}...,A_j)*Max(A_i,A_{i+1}...,A_j)$

即对于任意区间$[i,j](i<j)$求区间长度乘以区间最小值乘以区间最大值的总和。



考虑CDQ分治。

设我们当前分治到的区间为$[L,R]$，有$Mid=(L+R)/2$。

倒序枚举$i,i\in[L,Mid]$，同时记录$a,b$，$a$表示$[Mid+1,a]$中的最小值大于$[i,Mid]$的最小值的最右边，$b$表示$[Mid+1,b]$中的最大值小于$[i,Mid]$的最大值的最右边，$Mx$为$[i,Mid]$的最大值，$Mi$为$[i,Mid]$的最小值。



考虑下面这些情况（令$a<b$）：

1.$Mid+1\leq j \leq a$

​          $ Ans+=Mx*Mi*\sum_{Mid+1}^{a}(j-i+1) $

​          直接等差数列公式即可。



2.$a<j\leq b$

​          $Ans+=Mx*\sum_{a+1}^{b}Min(A_{Mid+1},...,A_j))*(j-i+1)\Rightarrow$

​          $Ans+=Mx*\sum_{a+1}^{b}Min(A_{Mid+1},...,A_j)*j-Min(A_{Mid+1},...,A_j)*(i+1)$

​          这预处理出$Min(A_{Mid+1,...,A_j})*j$和$Min(A_{Mid+1},...,A_j)$即可。



3.$b<j\leq R$

​          $Ans+=\sum_{b+1}^{R}Max(A_{b+1},...,A_j)*Min(A_{b+1},...,A_j)*(j-i+1)\Rightarrow$

​          $Ans+=\sum_{b+1}^{R}Max(A_{Mid+1},...,A_j)*Min(A_{Mid+1},...,A_j)*j-Max(A_{Mid+1},...,A_j)*Min(A_{Mid+1},...,A_j)*(i+1)$

​          预处理出$Max(A_{Mid+1},...,A_j)*Min(A_{Mid+1},...,A_j)*j$,

​                         $Max(A_{Mid+1},...,A_j)*Min(A_{Mid+1},...,A_j)$即可



*Code：*

~~~c++
#include <cstdio>
#include <algorithm>
using namespace std;
long long read(){
	char c;while(c=getchar(),c<'0'||c>'9');
	long long x=c-'0';while(c=getchar(),c>='0'&&c<='9')x=x*10+c-'0';
	return x;
}
const long long Maxn=5e5+5,Mod=1e9;
long long N,Ans=0,A[Maxn];
long long P1[Maxn],P2[Maxn],P3[Maxn],P4[Maxn],P5[Maxn],P6[Maxn];
void CDQ(long long L,long long R){
	if(L==R){(Ans+=A[L]*A[R])%=Mod;return ;}
	long long Mid=L+R>>1;
	CDQ(L,Mid),CDQ(Mid+1,R);
	P1[Mid]=P2[Mid]=P3[Mid]=P4[Mid]=P5[Mid]=P6[Mid]=0;
	for(long long i=Mid+1,Mx=0,Mi=2e9;i<=R;i++){
		Mi=min(Mi,A[i]),Mx=max(Mx,A[i]);
		P1[i]=(P1[i-1]+Mi*i%Mod)%Mod,P2[i]=(P2[i-1]+Mi)%Mod;//P1:Min(a+1,j]*j,P2:Min(a+1,j];
		P3[i]=(P3[i-1]+Mx*i%Mod)%Mod,P4[i]=(P4[i-1]+Mx)%Mod;//P3:Max(a+1,j]*j,P4:Max(a+1,j];
		P5[i]=(P5[i-1]+Mx*Mi%Mod*i)%Mod;//P5:Max(b+1,j]*Min(b+1,j]*j
		P6[i]=(P6[i-1]+Mx*Mi%Mod)%Mod;//P6:Max(b+1,j]*Min(b+1,j]
	}
	for(long long i=Mid,Mx=0,Mi=2e9,ar=Mid,br=Mid;i>=L;i--){
		Mx=max(Mx,A[i]),Mi=min(Mi,A[i]);
		for(;ar<R&&A[ar+1]>=Mi;ar++);
		for(;br<R&&A[br+1]<=Mx;br++);
		long long a=min(ar,br),b=max(ar,br);
		(Ans+=Mx*Mi%Mod*((Mid+1-i+1+a-i+1)*(a-Mid)/2%Mod))%=Mod;//j<=a
		if(ar<br)(Ans+=Mx*(P1[b]-P1[a]-(i-1)*(P2[b]-P2[a])%Mod)%Mod+Mod)%=Mod;
		else 	 (Ans+=Mi*(P3[b]-P3[a]-(i-1)*(P4[b]-P4[a])%Mod)%Mod+Mod)%=Mod;
		//a<j<=b，需要分类讨论（ar<br||ar>br） 
		(Ans+=(P5[R]-P5[b]-(i-1)*(P6[R]-P6[b])%Mod)%Mod+Mod)%=Mod;//b<j<=R
	}
}
int main()
{
	N=read();
	for(long long i=1;i<=N;i++)A[i]=read();
	CDQ(1,N),printf("%lld",Ans);
	return 0;
}
~~~
