## [Thu Summer Camp 2016]补退选

[题目传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=4896)

题目大意：维护一个可以支持以下功能的数据结构。

+ 插入字符串S
+ 删除字符串S
+ 询问最早在第几个事件之后，姓名以S为前缀的学生数量超过了(a*|ANS|+b)%c,|ANS|表示上次查询事件的答案的绝对值

前两个是*trie*的基本操作，第三个用*vector*记录即可。

*TIPS：vector最多开到O(Q)*

*Code：*

~~~c++
#include <cmath>
#include <cstdio>
#include <vector>
#include <cstring>
#include <iostream>
#include <algorithm>
#define wide 26
using namespace std;
struct Trie{
	struct Node{vector<int>P;int v;Node *ch[wide];Node(){memset(ch,0,sizeof ch);v=0;}}*head;
	Trie(){head=new Node;}
	void insert(char *A,int tim){
		int len=strlen(A),c;Node *p=head,*q;
		for(int i=0;i<len;i++){
			if(!p->ch[A[i]-'a'])q=new Node,p->ch[A[i]-'a']=q;
			p=p->ch[A[i]-'a'],p->v++;
			if(p->P.size()<p->v)p->P.push_back(tim);
		}
	}
	int find(char *A,int tim){
		int len=strlen(A);Node *p=head;
		for(int i=0;i<len;i++){if(!p->ch[A[i]-'a'])return -1;p=p->ch[A[i]-'a'];}
		if(p->P.size()>tim)return p->P[tim];
		return -1;
	}
	void delet(char *A){
		int len=strlen(A),c;Node *p=head;
		for(int i=0;i<len;i++)p=p->ch[A[i]-'a'],p->v--;
	}
}T;

int N,opt,x,y,z,Ans;
char C[65];
int main()
{
	cin>>N;
	for(int i=1;i<=N;i++){
		cin>>opt>>C;
		if(opt==1)T.insert(C,i);
		if(opt==2)T.delet(C);
		if(opt==3){
			cin>>x>>y>>z;
			Ans=T.find(C,(1ll*abs(Ans)*x+y)%z);
			printf("%d\n",Ans);
		}
	}
}
~~~

