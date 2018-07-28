## 关于区间Mex问题的算法整理

> 给你一个长度为n的数列，元素编号1到n，第i个元素值为Ai。现在有m个形如(L,R)的提问，你需要回答出区间[L,R]的mex值。即求出区间[L,R]中没有出现过的最小的非负整数。

该问题被称为区间Mex问题，这个问题的解法多种多样，下面给出3种不同解法。

* ### 离线+线段树维护

由于没有修改操作，我们可以考虑离线。

对于一个固定的左端点，我们可以在$O(N)$的复杂度内求出每个右端点的答案。

首先对于那些值大于等于$N$的肯定对答案没用。

然后开一个数组（桶）记录一下小于$N$的每个值有没有出现，然后用一个指针不停的向上爬就好了。

由于指针***只增不降***，所以均摊复杂度$O(N)$。

然后考虑对于一个左端点$i$对于**下一个左端点**$i+1$有什么影响。

只需要**消除**$A_i$对于之后那些$Mex_j$的**影响**就好了，$A_i$会对哪些$Mex_j$有影响呢？

$A_i$只会对下一个$A_j=A_i$的$[i,j)$产生影响。

只需要有一个数据结构支持区间修改（将区间内大于等于$x$的数修改为$y$）即可。**线段树**。

~~~c++
//离线+线段树解法ByCptraser
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

char tc(){
    static char tr[10000],*A=tr,*B=tr;
    return A==B&&(B=(A=tr)+fread(tr,1,10000,stdin),A==B)?EOF:*A++;
}
void read(int &x){
    char c;int y=1;for(;(c=tc())<48||c>57;)if(c=='-')y=-1;
    for(x=c-'0';(c=tc())>47&&c<58;x=(x<<1)+(x<<3)+c-'0');
}

const int Maxn=4e5+5;

int N,M,A[Maxn],T[Maxn],Top,Ans[Maxn];
int f[Maxn],to[Maxn];
void Addd(int x,int y){to[x]=y;}

struct Query{
    int x,y,id;
    bool operator < (const Query &v)const{
        return v.x>x;
    }
}Qr[Maxn];

int tr[Maxn<<2],Add[Maxn<<2];
void Up(int Node){tr[Node]=min(tr[Node<<1],tr[Node<<1|1]);}
void PushDown(int Node){
    if(~Add[Node]){
        tr[Node<<1]=min(tr[Node<<1],Add[Node]);
        tr[Node<<1|1]=min(tr[Node<<1|1],Add[Node]);
        if(~Add[Node<<1])Add[Node<<1]=min(Add[Node<<1],Add[Node]);else Add[Node<<1]=Add[Node];
        if(~Add[Node<<1|1])Add[Node<<1|1]=min(Add[Node<<1|1],Add[Node]);else Add[Node<<1|1]=Add[Node];
        Add[Node]=-1;
    }
}
void Build(int l,int r,int Node){
    if(l==r)tr[Node]=f[l];
    else{
        int Mid=l+r>>1;
        Build(l,Mid,Node<<1);
        Build(Mid+1,r,Node<<1|1);
        Up(Node);
    }
}
int Query(int l,int r,int Node,int P){
    PushDown(Node);
    if(l>=r)return tr[Node];
    int Mid=l+r>>1;
    if(P<=Mid)return Query(l,Mid,Node<<1,P);
    else return Query(Mid+1,r,Node<<1|1,P);
}
void Change(int l,int r,int Node,int Cl,int Cr,int v){
    PushDown(Node);
    if(l>=Cl&&r<=Cr){
        tr[Node]=min(tr[Node],v);
        Add[Node]=v;
        return ;
    }
    int Mid=l+r>>1;
    if(Mid>=Cl)Change(l,Mid,Node<<1,Cl,Cr,v);
    if(Mid<Cr) Change(Mid+1,r,Node<<1|1,Cl,Cr,v);
    Up(Node);
}

int main()
{
    read(N),read(M);
    memset(to,-1,sizeof to);
    memset(Add,-1,sizeof Add);
    for(int i=1;i<=N;read(A[i++]));
    for(int i=1;i<=N;i++){
        if(A[i]>=N){f[i]=f[i-1];continue;}
        for(T[A[i]]++;T[Top];Top++);
        f[i]=Top;
    }
    memset(T,0,sizeof T);
    for(int i=1;i<=N;i++){
        if(A[i]>=N)continue;
        if(T[A[i]])Addd(T[A[i]],i-1);
        T[A[i]]=i;
    }
    for(int i=0;i<N;i++)if(T[i])Addd(T[i],N);
    for(int i=1;i<=M;i++)
        read(Qr[i].x),read(Qr[i].y),Qr[i].id=i;
    sort(Qr+1,Qr+M+1);
    Build(1,N,1);
    int L=1,i=1;
    for(;;){
        for(;L<Qr[i].x;L++)if(~to[L]&&A[L]<N)Change(1,N,1,L+1,to[L],A[L]);
        for(;i<=M;i++)if(Qr[i].x==L)Ans[Qr[i].id]=Query(1,N,1,Qr[i].y);else break;
        if(i>M)break;
    }
    for(int i=1;i<=M;i++)printf("%d\n",Ans[i]);
    return 0;
}
~~~

