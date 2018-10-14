## BZOJ4318 OSU!

[题目传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=4318)

*Solution：*

假设我们目前结尾有连续$x$个1，现在又有一个1，那么这个1的贡献为$(x+1)^3-x^3\Rightarrow 3x^2+3x+1$，令$f[i]$表示前$i$个数的期望，同时维护$x$和$x^2$的期望即可。

*Code：*

~~~c++
#include <cstdio>
using namespace std;

int N;
double A[100005],X[100005],_X[100005],F[100005];
int main()
{
	scanf("%d",&N);
	for(int i=1;i<=N;i++)scanf("%lf",&A[i]);
	for(int i=1;i<=N;i++){
		X[i]=(X[i-1]+1)*A[i];
		_X[i]=(_X[i-1]+2.0*X[i-1]+1.0)*A[i];//此处之所以不必再+1是因为这1的期望在X[i-1]中已经加了
		F[i]=F[i-1]+(3.0*_X[i-1]+3.0*X[i-1]+1)*A[i];
	}printf("%.1f",F[N]);
}
~~~

