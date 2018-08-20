## $Luogu3795$钟氏映射

[题面传送门](https://www.luogu.org/problemnew/show/P3795)

设$f[i]$表示$i$的方案数，考虑放入第$i+1$个数，这个数可以映射到自己，也可以与$f[i-1]$中的数匹配。

$f_i=f_{i-1}+(i-1)*f_{i-2}$

~~~c++
#include <cstdio>
#define Mod 14233333
using namespace std;

int K,a,b,c;
int main()
{
	scanf("%d",&K);
	a=1,b=2;
	for(int i=3;i<=K;i++){
		(c=b+1ll*a*(i-1)%Mod)%=Mod;
		a=b,b=c;
	}
	printf("%d\n",c);
}
~~~

