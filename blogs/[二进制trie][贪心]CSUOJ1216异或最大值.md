### CSUOJ1216异或最大值

[题目传送门](http://acm.csu.edu.cn/csuoj/problemset/problem?pid=1216)

过了好久，终于重新开始写博客了。。。

这是一道二进制trie树的模板题。

二进制trie树，理解一下就是一颗二叉树，左右儿子为０或１。

然后每插入一个数就进行一次Find操作。

Find：对于一个数x，我们在trie上总是走x在二进制下第i位的相反的那个节点。（当存在此节点时）

##### *Code*

```python
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

const int Maxn=1e5+5;
int N,x,Ans,tr[Maxn][2],Cnt;
void I(int x){
    int Node=0;
    for(int i=31;~i;i--){
        if(!tr[Node][(x>>i)&1])tr[Node][(x>>i)&1]=++Cnt;
        Node=tr[Node][(x>>i)&1];
    }
}
int F(int x){
    int Node=0,Sum=0;
    for(int i=31;~i;i--){
        int l=(x>>i)&1^1,r=(x>>i)&1;
        if(!tr[Node][l])Node=tr[Node][r],Sum+=r<<i;
        else Node=tr[Node][l],Sum+=l<<i;
    }
    return Sum^x;
}
int main()
{
    while(~scanf("%d",&N)){
        memset(tr,0,sizeof tr),Ans=Cnt=0;
        for(int i=1;i<=N;i++){
            scanf("%d",&x);
            I(x);Ans=max(Ans,F(x));
        }
        printf("%d\n",Ans);
    }
    return 0;
}
```
[贪心]: 