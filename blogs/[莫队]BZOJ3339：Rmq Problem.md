### [莫队]BZOJ3339：Rmq Problem

[题目传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=3339)

莫队板子题。

开一个桶记录当前操作时的状态。

对于+1的操作我们就看看最小值是否和当前修改的点相同，相同就向后找答案。

对于-1的操作我们如果有$ t_x=0 $则取$ min$。

_Code：_

~~~c++
#include <cmath>
#include <cstdio>
#include <algorithm>
using namespace std;
int read(){
    char c;while(c=getchar(),c<'0'||c>'9');
    int x=c-'0';while(c=getchar(),c>='0'&&c<='9')x=x*10+c-'0';
    return x;
}
const int Maxn=200005;
int N,M,A[Maxn],B[Maxn],Lig,t[Maxn],Ans[Maxn];
struct Q{int x,y,i;}Qr[Maxn];
int Cmp(Q x,Q y){return B[x.x]==B[y.x]?x.y<y.y:x.x<y.x;}
void Resv(int x,int v){
    t[x]+=v;
    if(x==Lig&&t[x])while(t[Lig])Lig++;
    if(!t[x])Lig=min(Lig,x);
}
int Size;
int main()
{
    N=read(),M=read(),Size=sqrt(N+0.5);
    for(int i=1;i<=N;i++)A[i]=read();
    for(int i=1;i<=N;i++)B[i]=(i-1)/Size+1;
    for(int i=1;i<=M;i++)Qr[i].x=read(),Qr[i].y=read(),Qr[i].i=i;
    sort(Qr+1,Qr+M+1,Cmp);
    int L=1,R=0;
    for(int i=1;i<=M;i++){
        while(L<Qr[i].x)Resv(A[L++],-1);
        while(L>Qr[i].x)Resv(A[--L],1);
        while(R<Qr[i].y)Resv(A[++R],1);
        while(R>Qr[i].y)Resv(A[R--],-1);
        Ans[Qr[i].i]=Lig;
    }
    for(int i=1;i<=M;i++)printf("%d\n",Ans[i]);
    return 0;
}
~~~

