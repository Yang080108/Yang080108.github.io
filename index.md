## Welcome to GitHub Pages by Yang080108

this is a temporary note page for Yang080108
	
  
  
  
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
|	|时间复杂度	|空间复杂度	|备注
| -----------: | -----------: | -----------: | -----------: |
|Dijkstra	|$O(V^2)$	|$O(V^2)$|不能有负边
|Bellman Ford	|$O(VE)$	|$O(E)$	|可以判负环
|Floyd	|$O(V^3)$	|$O(V^2)$	|[传递闭包](https://iai.sh.cn/problem/503)
|堆优化Dijkstra	|$O(ElgV)$||
		
			

