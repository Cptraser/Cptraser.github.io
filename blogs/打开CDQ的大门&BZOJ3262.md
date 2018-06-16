### 打开CDQ的大门&BZOJ3262

[题目传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=3262)

第一次接触CDQ分治，感谢YZ大佬的教导。

CDQ分治就是一种奇特的分治方法，它用左区间的区间信息来更新右区间。

设CDQ(L,R,l,r)表示递归到区间[L,R]，区间的值为[l,r]。

mid=(l+r)/2。

将L~R区间按<=mid和>mid的两块重新分开。

继续递归区间分治。

CDQ分治用于解决一类偏序问题，题目中所求的为三维偏序。

即计算一类$a_i<b_i,a_j<b_j,a_k<b_k$的贡献。

普通的二维偏序大家都做过，经典题目逆序对。

三维怎么做呢？

第一维依然是SORT，第三位是树状数组。

第二维是CDQ。

第一维SORT后有序，故无需考虑第一维。

第二维CDQ分治时枚举L~R区间，当a[i].y>mid时，计算在权值树状数组上的贡献，否则插入树状数组。

**注意：这道题需要对重复区间判重，否则贡献值不好算。** 

~~~c++
/**************************************************************
    Problem: 3262
    User: yekehe
    Language: C++
    Result: Accepted
    Time:1116 ms
    Memory:10600 kb
****************************************************************/
 
#include <cstdio>
#include <algorithm>
using namespace std;
 
char tc()
{
    static char tr[10000],*A=tr,*B=tr;
    return A==B&&(B=(A=tr)+fread(tr,1,10000,stdin),A==B)?EOF:*A++;
}
 
int read()
{
    char c;while(c=tc(),c<'0'||c>'9');
    int x=c-'0';while(c=tc(),c>='0'&&c<='9')x=x*10+c-'0';
    return x;
}
 
struct node{
    int x,y,z,o;
}a[100005],lx[100005],rx[100005],Ne[100005];
int N,K,cnt;
int at[200005],b[200005],tim,ans[200005],tot[200005];
inline int cmp(node x,node y){return x.x<y.x || x.x==y.x&&x.y<y.y || x.x==y.x&&x.y==y.y&&x.z<y.z;}
 
void add(int x,int y)
{
    for(int i=x;i<=K;i+=i&-i){
        if(b[i]!=tim)at[i]=0,b[i]=tim;
        at[i]+=y;
    }
    return ;
}
 
int get(int x)
{
    int tot=0;
    for(int i=x;i;i-=i&-i)
        if(!(b[i]^tim))tot+=at[i];
    return tot;
}
 
void cdq(int L,int R,int l,int r)
{
    tim++;
    int mid=l+r>>1,lt=0,rt=0;
        for(int i=L;i<=R;i++){
            if(!(l^r)){
                ans[Ne[i].o]+=get(Ne[i].z);
                add(Ne[i].z,tot[Ne[i].o]);
            }
            else{
                if(Ne[i].y<=mid)add(Ne[i].z,tot[Ne[i].o]);
                else ans[Ne[i].o]+=get(Ne[i].z);
            }
        }//统计贡献
        for(int i=L;i<=R;i++){
            if(Ne[i].y<=mid)lx[++lt]=Ne[i];
            else rx[++rt]=Ne[i];
        }
        for(int i=1;i<=lt;i++)Ne[L+i-1]=lx[i];
        for(int i=1;i<=rt;i++)Ne[L+i-1+lt]=rx[i];//类似于归并那样重组
    if(!(l^r))return ;
    cdq(L,L+lt-1,l,mid);
    cdq(L+lt,R,mid+1,r);
    return ;
}
 
int IS(int x,int y){
    return a[x].x==a[y].x&&a[x].y==a[y].y&&a[x].z==a[y].z;
}
 
int res[100005];
 
int main()
{
    N=read(),K=read();
        for(int i=1;i<=N;i++){
            a[i].x=read(),a[i].y=read(),a[i].z=read();
        }
    sort(a+1,a+N+1,cmp);
    a[0].x=a[0].y=a[0].z=-2e9;
        for(int i=1;i<=N;i++){
            if(IS(i,i-1))++tot[cnt];
            else tot[++cnt]=1,Ne[cnt]=a[i],Ne[cnt].o=cnt;
        }//去重
    cdq(1,cnt,0,K);//注意为cnt
        for(int i=1;i<=cnt;i++)res[ans[i]+tot[i]-1]+=tot[i];
        for(int i=0;i<N;i++)printf("%d\n",res[i]);
    return 0;
}
~~~

