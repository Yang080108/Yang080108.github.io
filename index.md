## Welcome to GitHub Pages by Yang080108

this is a temporary note page for Yang080108
	
  
  
  
	10.20 堆优化dijkstra
	dijkstra可以解决最短路问题（SP）
		1. 单源最短路（SSSP）
		2. 多元最短路（MSSP）
	
	Dis：
		|0|1|3|3|4|4|
		 1 2 3 4 5 6
		然后进行松弛操作
	Dijkstra 算 法 的 过 程 总 结：
	第一步： 从起始顶点开始
	第二步： 对当前顶点进行标识
	第三步： 对当前顶点的所有相邻顶点依次进行松弛操作
	第四步： 更新列表
	第五步： 从列表的未标识顶点中找到当前最短距离最小的顶点， 作为新的当前顶点
	第六步： 重复第二步至第五步， 直到列表中不存在未标识顶点
	Dijkstra 算法主要做两件事情：
	（ 1 ） 从列表中找最值
	（ 2 ） 更新列表
	
堆优化的dijk：

#include<bits/stdc++.h>
using namespace std;
const int MAXV=1e5+5;
const int MAXE=2e5+5;
struct tNode{
	int v,w,nxt;
}e[MAXE];
struct node{
	int u,dis;
	bool operator<(const node&x)const{
		return dis>x.dis;
	}
};
int n,m,s,dis[MAXV],cnt=0,head[MAXV];
bool vis[MAXV];
priority_queue<node>q;
void add_edge(int u,int v,int w){
	e[++cnt].v=v;
	e[cnt].w=w;
	e[cnt].nxt=head[u];
	head[u]=cnt;
}
void dij(){
	memset(dis,0x3f,sizeof(dis));
	dis[s]=0;
	q.push({s,0});
	while(!(q.empty())){
		int u=q.top().u;
		int disu=q.top().dis;
		q.pop();
		if(vis[u])
			continue;
		vis[u]=true;
		dis[u]=disu;
		for(int i=head[u];i;i=e[i].nxt){
			int v=e[i].v;
			if(dis[v]>dis[u]+e[i].w){
				dis[v]=dis[u]+e[i].w;
				q.push({v,dis[v]});
			}
		}
	}
}
int main(){
	cin>>n>>m>>s;
	for(int i=1;i<=m;i++){
		int u,v,w;
		cin>>u>>v>>w;
		add_edge(u,v,w);
	}
	dij();
	for(int i=1;i<=n;i++){
		cout<<dis[i]<<" ";
	}
	return 0;
} 


10.20 堆优化dijkstra
DSU 并查集
6110196
	
	
4.2 网络流
问题描述
这是一个比较狭窄的问题
一个网络=一张图
两个特殊点:
	起点（源点），终点（汇点）
剩下的都是普通点
边： 可以是有向边， 也可以是无向边
边上权重不是长度， 距离， 而是容量
意思是这点边上通过的流水不能超过给定上限
对于边，要满足容量上限条件
对于普通点， 要满足流量平衡条件
对于u 总流入=总流出
起点和终点不受这个条件限制
找到一种流的布局
使得从起点到终点的流量最大

遇树归纳 遇图贪心
图论就一个动态规划：dijstra，其余都是贪心

网络流中的贪心 -- 朴素想法 -- 能增加就增加

While 能够找到一条能够从起点到终点的道路
Do 为这条路上的每条边计算“潜力”
可以增加的量=最小潜力
按照潜力为路上的每条边加流量

在计算网络流的时候，每条边都是在动态变化的

一条边的“潜力”=给定的容量-当前的流量
			  =可增广量
有潜力=可增广
没有潜力=饱和

上述的贪心，因为缺乏一种“反悔”机制
		 看上去就感觉有反例
		
为了解决这种反例，必须引入“退钱”（反向弧）算法

重新认识什么叫做可增广（或者叫做不饱和）
A ---> B 还有潜力（可增广）
	1. 容量（a,b）> 容量(a,b)
	2. 流量（b,a） >0
对称表示：
	假设我们用f[a,b]表示a--->b的流量
	是否可能f[a,b] = 3, f[b,a] = 8
	回答：不可能
	因为如果真的这样的话，可简化为
	f[a,b] = 0 f[b,a] = 5
	结论是，在一般表示里，f[a,b] ，f[b,a]
	至少有一个是0
	但这样的话，f数组就有一半的数字是0，效率不高
	我们就定义对称表示为：
	f[a,b] ==- f[b,a] 
	用一对数字表达一个概念，那不是自找冗余么？
	就是要冗余
	
	原来：图上a--->b f[a,b]=5, f[b,a]=0;
	现在，f[a,b]=5; f[b,a]=-5;
	
	A ---> B 还有潜力（可增广）
	一般表示
		1. C（a,b）> f(a,b)
		2. f（b,a） >0
	对称表示
		1. C（a,b）> f(a,b);  R[a,b]>0
	
-------------------------------------------
新认识：就算原图没有说明x--->y有边
	我们也要设 容量（x,y）=0;
	对称表示的引入，刻画了反向弧及反向弧的增广潜力
	
While 能够找到一条能够从s到t的道路
Do 为这条路上的每条边计算“潜力”
可以增加的量=最小潜力
按照潜力为路上的每条边加流量

上述贪心算法的证明非常重要
1/ 证明需要一个结论，最大流=最小割
	这个结论，很多题目要用
	（实用主义）
2/ 思想优美，有启发

P2891 [USACO07OPEN]Dining G - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)

4/9 最大流=最小割

定义“割”：
跨过割的边
从s 所在的集合出发
能到t 所在集合的所有边
这些边的容量之和就是割的大小

在如图所示的网络中
该割的大小为10+20
注意不用减5
显然的是， 所有的割> = 所有的流
所以，最大流=最小割

从某个流出发，构造一个割。
发现这个流的大小=构造出的割大小
于是这个流就是最大流
	这个割就是最小割
	
从任意一个不可再增广的流出发
不可在增广的流=s到t没有增广路

定义一个割

对于每个网络上的点u
如果s到u有增广路
u属于s这边
如果没有增广路，
u属于t这边

考虑这个割的大小
任意一条边(u,v)
如果u属于s，v属于t
c[u][v] = f[u][v]


有向无环图=DAG


4/17 数论

整个初等数论围绕的关键点就是要解

 ax=b(mod)m

7x=2
	2
x=------
	7
7x=2(mod 11)
	 (-1)
x=2*7   (mod11)

Inv7 * 7 * x =x

7*8=56=55+1=1 (mod 11)

Inv 7就是7的逆元

当然注意，逆元必须要在一个固定的模数下才能确定
m=11, inv7=8
m=13,  inv7=2

说白了，这是在为 mod 运算构造一个数系

数系 = 一些元素 + 元素之间有加减乘除运算且基本封闭

有理数，实数，复数

Mod 11=11 {0，1，2，3， ，10}

1 - 7 = -6 = 5 （mod 11）
6  * 8 = 4
6 / 8 = 6 * inv8

费马说：因为11是个素数，所以
	        10
	(1~10)     =1 （mod 11）
			9
	所以 8 * 8   =  1（mod 11）
	所以 inv8 = 8^9
	
	
Thm fermat  只要p是素数，对于所有的A
  p-1
A        =1  (mod p)

Proof: 取p=11

	1,2,3,4,…,10
	1A  ,2A  ,3A  ,4A  ,…,10A  

发现 1A  ,2A  ,3A  ,4A  ,…,10A  
只不过是将 1,2,3,4,…,10 的重排

1*2*3*4*…..*10=1A *2A *3A *4A  ,…,*10A 

1=A*A*A*A*A*A*A*A*A*A

  10
A      =1 

大学学抽象代数（群论）


某道题目，如果结果是一个小数（概率）

命题人有3种选择
	1. 要求出小数--> 做题人可以用蒙特卡洛来骗分
		蒙特卡洛 = 实验法， 实验100万次
						记录拿到汉堡的次数
						作为答案输出
						
	2. 为了对抗蒙特卡洛，命题人可以通过提高小数精度
	导致的结果就是计算误差
	
	3. 直接输出精确值 a/b
	==> 数位增长非常快，没法搞太大的数
	
	4. 输出分数在某个模下的结果 
		   a
	输出 ------- (mod 10007)
		1.    b

	a*b/c (mod p)
       = a*b*invc

X          a
---  +  ----   (mod p)
Y          b

所以 
数论可以辅助我们解决在有理数上运算过多， 精度爆炸的问题

4/27 线段树


struct Segment_Tree{
	int l,r;
	long long val;
}t[MAXN<<2]; 
int n,m,a[MAXN];

void build(int pos,int left,int right){
	t[pos].l=left;
	t[pos].r=right;
	if(left==right){
		t[pos].val=a[left];
		return;
	}
	int mid=(left+right)/2;
	build(pos*2,left,mid);
	build(pos*2+1,mid+1,right);
	t[pos].val=t[pos*2].val+t[pos*2+1].val;
}

void modify_point(int pos,int x,int delta){
	if(t[pos].l==t[pos].r){
		t[pos].val+=delta;
		return;
	}
	int mid=(t[pos].l+t[pos].r)/2;
	if(x<=mid)
		modify_point(pos*2,x,delta);
	else
		modify_point(pos*2+1,x,delta);
	t[pos].val=t[pos*2].val+t[pos*2+1].val;
}

long long query_interval(int pos,int left,int right){
	long long ans=0;
	if(t[pos].l>=left && t[pos].r<=right)
		return t[pos].val;
	int mid=(t[pos].l+t[pos].r)/2;
	if(left<=mid)
		ans+=query_interval(pos*2,left,right);
	if(right>mid)
		ans+=query_interval(pos*2+1,left,right);
	return ans;
}

void spread_down(int pos){
	t[pos*2].tag+=t[pos].tag;
	t[pos*2+1].tag+=t[pos].tag;
	t[pos*2].val+=(t[pos*2].r-t[pos*2].l+1)*t[pos].tag;
	t[pos*2+1].val+=(t[pos*2+1].r-t[pos*2+1].l+1)*t[pos].tag;
	t[pos].tag=0; 
}

void modify_interval(int pos,int left,int right,int delta){
	if(t[pos].l>=left && t[pos].r<=right){
		t[pos].val+=(t[pos].r-t[pos].l+1)*delta;
		t[pos].tag+=delta;
		return;
	}
	if(t[pos].tag) spread_down(pos);
	int mid=(t[pos].l+t[pos].r)/2;
	if(left<=mid) modify_interval(pos*2,left,right,delta);
	if(right>mid) modify_interval(pos*2+1,left,right,delta);
	t[pos].val=t[pos*2].val+t[pos*2+1].val;
}


7/18 P7077 [CSP-S2020] 函数调用 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)

Type 1
Type 2
Type 3

type2+type3
	批量乘法 + 嵌套调用
bottom-up 地计算每个函数的" 等价效应"
相对于儿子孙子来说， 一个乘法因子就可以代替儿子孙子的嵌套操作

type1+type3
	单点加法+ 嵌套调用
从顶部出发， 计算每个操操作被重复调用的次数，push_down
					从父亲出发，将信息传给儿子，如此一直向下传
Top-down
然后用乘法提速加法

type1+type2
	如果没有嵌套调用，只有+ *
离线，全读进来之后综合处理
后缀
----------------------------------------

P4647






	
		
		
		
		
8/24 动态 dp

浅谈动态dp - 知乎 (zhihu.com)    ——赵鹏宏的笔记

IAI神奇宝贝

9/10 2-SAT问题
	
	P4782 【模板】2-SAT 问题 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)
2-SAT 问题应用

初赛复习

求一个串所有的子串的个数
(n+1)∗n/2+1
计算星期几
w=[y+y/4+c/4−2∗c+(26∗(m+1)/10)+d−1](mod 7)
1582年10月4日及以前用w=[y+（y/4）+c/4-2*c+13*(m+1)/5+d+2]（mod7）来计算。
y为年份的后两位数
c为年份的前两位数
m为月数（取值范围为3-14，每年的1月和2月视为13和14）
d为日序数。
需要指出的是，当年份前或者后两位数、月份值应用上式除以相应数值时，要向下取整，确保商与除数之积不大于被除数。最后w取值如果为0，则代表星期日；值为1，代表星期一，就此类推。

10/1 树上问题
树dp：
	求树的直径
		1. 2次dfs 
			void dfs(int x,int father)
			{
				for(int i=h[x];i!=-1;i=ne[i])//用链式前向星存树
				{
					int j=e[i];
					if(j==father) continue;//树是一种有向无环图，只要搜索过程中不返回父亲节点即可保证不会重复遍历同一个点。
					d[j]=d[x]+w[i];//更新每个点到起始点的距离（在树中任意两点的距离是唯一的）
					dfs(j,x);//继续搜索下一个节点
				}
			}
			
		2. Dp
			i. f(i) 从i出发向i子树内走最远
			需要保存最远和次远，以u为根的树的直径为u这个节点的最远和次远
			ii. 只需要保存最远
			void dfs(int u,int fa){
				for(int i=head[u];i;i=e[i].nxt){
					int v=e[i].v;
					if(v==fa)	continue;
					dfs(v);
					ans=max(ans,dis[u]+dis[v]+e[i].w);
					dis[u]=max(dis[u],dis[v]+e[i].w);
				}
			} 
	求树的重心
		
	独立集问题
		
10/2 图论问题

	时间复杂度	空间复杂度	备注
Dijkstra	O(V^2)	O(V^2)	不能有负边
Bellman Ford	O(VE)	O(E)	可以判负环
Floyd	O(V^3)	O(V^2)	传递闭包上海市计算机学会竞赛平台 | YACS (iai.sh.cn)
堆优化Dijkstra	O(ElgV)		
SPFA			
![image](https://user-images.githubusercontent.com/107249565/193442895-363e1e5f-83b4-4e96-9c79-807c4ad25a40.png)
