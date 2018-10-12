## 重拾WQS二分

#### 前置SKILL：·初等函数 ·二分

对于一个单调的函数，我们可以二分一个斜率，使其$x$增大的同时限制$y$来求得我们所求的答案。



[*BZOJ2654* *tree*](https://www.lydsy.com/JudgeOnline/problem.php?id=2654)

我们二分边权增量$k$，令每条白边加上该增量，最后答案减去$need*k$即可。

该想法可以视为将白边被最小生成树选中的概率减小。

~~~c++
#include <cstdio>
#include <algorithm>
using namespace std;

int read()
{
	char c;while(c=getchar(),(c<'0'||c>'9')&&c!='-');
	int x=0,y=1;c=='-'?y=-1:x=c-'0';
	while(c=getchar(),c>='0'&&c<='9')x=x*10+c-'0';
	return x*y;
}

const int Maxm=1e5+5,Maxn=5*1e4+5;
int N,M,K,Tot,Num,Fa[Maxn];
struct Edge{
	int x,y,c,col;
}A[Maxm],Ne[Maxm];
int Cmp(Edge x,Edge y){return (x.c<y.c)||(x.c==y.c&&x.col<y.col);}      

int Getf(int x){return x==Fa[x]?x:Fa[x]=Getf(Fa[x]);}
int Check(int Wx)
{
	Tot=Num=0;
		for(int i=0;i<=N;i++)Fa[i]=i;
		for(int i=1;i<=M;i++){
			Ne[i]=A[i];
			if(!A[i].col)Ne[i].c+=Wx;
		}
	sort(Ne+1,Ne+M+1,Cmp);
		for(int i=1;i<=M;i++){
			int Fx=Getf(Ne[i].x),Fy=Getf(Ne[i].y),Val=Ne[i].c;
			if(Fx^Fy){
				Fa[Fx]=Fy;
				Tot+=Val;
				if(!Ne[i].col)Num++;
			}
		}
	return Num>=K;
}

int main()
{
	N=read(),M=read();
	K=read();
		for(int i=1;i<=M;i++)
			A[i].x=read(),A[i].y=read(),A[i].c=read(),A[i].col=read();
	int L=-100,R=100,Ans;
		while(L<R){
			int Mid=L+R>>1;
			if(Check(Mid))Ans=Tot-K*Mid,L=Mid+1;
			else R=Mid;
		}
	printf("%d",Ans);
	return 0;
}
~~~



[*BZOJ1150* *数据备份*](https://www.lydsy.com/JudgeOnline/problem.php?id=1150)

考虑DP，可以很简单地写出*O(N^2)*的DP方程，但时空复杂度皆不够，可以用*WQS二分*降维打击。

二分增量$k$表示选取区间所需要的费用。

~~~c++
#include <cstdio>
#include <cctype>
#include <algorithm>
#define V 100005
#define Mid (L+R>>1)
using namespace std;

char tc(){static char tr[10000],*A=tr,*B=tr;return A==B&&(B=(A=tr)+fread(tr,1,10000,stdin),A==B)?EOF:*A++;}
void read(long long &x){char c;for(;!isdigit(c=tc()););for(x=c-48;isdigit(c=tc());x=(x<<1)+(x<<3)+c-48);}

long long N,K,A[V],L=-1e9,R,f[V],S[V],Ans;
int main()
{
	read(N),read(K);for(int i=1;i<=N;i++)read(A[i]);
	for(sort(A+1,A+N+1);L<=R;){
		for(int i=2;i<=N;i++){
			f[i]=f[i-1],S[i]=S[i-1];
			if(f[i-2]+A[i]-A[i-1]+Mid<=f[i])f[i]=f[i-2]+A[i]-A[i-1]+Mid,S[i]=S[i-2]+1;
		}if(S[N]>=K)Ans=f[N]-Mid*K,L=Mid+1;else R=Mid-1;
	}printf("%lld\n",Ans);
}
~~~

CF双倍经验：[*Codeforces958E2* *GuardDuty(Medium)*](http://codeforces.com/problemset/problem/958/E2)



[*Codeforces739E* *GoshaIsHunting*](http://codeforces.com/problemset/problem/958/E2)

也是一眼可以写出*O(N^3)*DP的题，然后*WQS二分*套*WQS二分*削去两维即可。

~~~c++
#include <cstdio>
#include <cctype>
#include <algorithm>
#define V 2005
using namespace std;

const double eps=1e-10;

int N,a,b,Ca,Cb;
double A[V],B[V],Ans,CA,CB;
int Work(double Cost){
	CB=Cost;Ca=Cb=0;Ans=0;
	for(int i=1;i<=N;i++){
		double v=0;int opt=0;
		if(A[i]-CA>v)opt=1,v=A[i]-CA;
		if(B[i]-CB>v)opt=2,v=B[i]-CB;
		if(A[i]+B[i]-A[i]*B[i]-CA-CB>v)opt=3,v=A[i]+B[i]-A[i]*B[i]-CA-CB;
		if(opt==1||opt==3)Ca++;
		if(opt==2||opt==3)Cb++;
		Ans+=v;
	}return Cb;
}
int Check(double Cost){
	double L=0,R=1.0;CA=Cost;
	while(R-L>eps){
		double Mid=(L+R)/2;
		if(Work(Mid)<=b)R=Mid;
		else L=Mid;
	}return Work(CB=R),Ca;
}
int main()
{
	scanf("%d%d%d",&N,&a,&b);
	for(int i=1;i<=N;i++)scanf("%lf",&A[i]);
	for(int i=1;i<=N;i++)scanf("%lf",&B[i]);
	double L=0,R=1.0;
	while(R-L>eps){
		double Mid=(L+R)/2;
		if(Check(Mid)<=a)R=Mid;
		else L=Mid;
	}
	Check(R),Ans+=CA*a+CB*b;
	printf("%.12lf",Ans);
	return 0;
}
~~~

