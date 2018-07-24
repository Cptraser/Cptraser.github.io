## ZZY dalao 数论选讲刷题录

* [$[NOI2002]Savage$](https://www.lydsy.com/JudgeOnline/problem.php?id=1407)

$Solution$：枚举$M$，同时$N^2$枚举两点，那么我们只要知道在什么时候可得这两点相交，且判断是否小于两个人的生命最小值。

> $(C_i+P_i*x)\equiv (C_j+P_j*x)(Mod$ $M)\Rightarrow$$(P_i-P_j)*x+M*y=C_j-C_i$
>
> ​                                                                                           $a$  $*$  $x$ $+$   $b*y$  $=$  $c$

~~~c++
#include <cstdio>
#include <algorithm>

int gcd(int x,int y){return !y?x:gcd(y,x%y);}
void exgcd(int a,int b,int &x,int &y){
	if(!b){x=1,y=0;return ;}
	exgcd(b,a%b,y,x);
	y-=a/b*x;
}
int N,C[16],P[16],L[16],Mx;
int CK(int t){
	for(int i=1;i<=N;i++)
		for(int j=i+1,x,y;j<=N;j++){
			int A=P[i]-P[j],B=t,K=C[j]-C[i],tk=gcd(A,B);
			if(!(K%tk)){
				A/=tk,B/=tk,K/=tk;
				exgcd(A,B,x,y);
				B=abs(B),x*=K,x=(x%B+B)%B;
				if(!x)x=B;
				if(x<=std::min(L[i],L[j]))return 0;
			}
		}
	return 1;
}
int main()
{
	scanf("%d",&N);
	for(int i=1;i<=N;i++)scanf("%d%d%d",&C[i],&P[i],&L[i]),Mx=std::max(Mx,C[i]);
	for(int t=Mx;t<=1e6;t++)
		if(CK(t))return printf("%d",t),0;
    return 0;
}
~~~



* [$[2016-2017$ $ACM-ICPC$ $China-Final][GYM$ $101194$ $H]$ $Great$ $Cells$](http://codeforces.com/gym/101194/attachments/download/5009/20162017-acmicpc-china-final-en.pdf)

$Description$：在一个$n*m$的矩阵内填整数，数字在$[1...K]$范围内。矩阵中某格的数为$great$ number当且仅当与它同行同列的数字都严格比它小。记$A_g$为矩阵中恰有$g$个$great$ $number$的填数方案数，求$\sum_{g=0}^{nm}(g+1)A_g$$(n,m,k<=200)$

$Solution$：首先可得答案化为$\sum_{g=0}^{min(n,m)}(g+1)A_g$，然后对将原式拆为$\sum_{g=0}^{min(n,m)}A_g+\sum_{g=0}^{min(n.m)}g*A_g$，可以发现前半部分答案显然为$K^{nm}$，因为前半部分表示总方案数，即对于任意矩阵，都包含于$\sum_{g=0}^{nm}A_g$中。后半部分考虑$g*A_g$的本身意义，$A_g$表示存在$g$个$great$ $number$的方案，然后又乘以$g$其实就是对于方案中的所有$great$ $number$对答案贡献都是$1$，那么后半部分即：$\sum_{g=0}^{min(n,m)}=nm\sum_{i=2}^{k}(i-1)^{n+m-2}*k^{(n-1)(m-1)}$。

~~~c++
#include <cstdio>

const int Mod=1e9+7;
long long T,N,M,K,Ans;
long long Ksm(long long x,long long y){
	long long Res=1;
	while(y){
		if(y&1)Res=Res*x%Mod;
		x=x*x%Mod,y>>=1;
	}return Res;
}

int main()
{
	scanf("%lld",&T);
	for(int Cnt=1;Cnt<=T;Cnt++){
		scanf("%lld%lld%lld",&N,&M,&K);
		printf("Case #%d: ",Cnt);
		Ans=Ksm(K,N*M%Mod);
		for(int i=2;i<=K;i++)
			Ans=(Ans+Ksm(i-1,N+M-2)*Ksm(K,(N-1)*(M-1))%Mod*N%Mod*M%Mod)%Mod;
		printf("%lld\n",Ans);
	}
}
~~~

