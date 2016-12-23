---
layout: post
title:  "单源点/多源点最短路径算法"
date:   2016-01-15 15:27:41 +0800
categories: 算法
excerpt:
---

最短路径算法
&emsp;假设图G(V,E), 有n个顶点，e条边，w(u,v)为两点间边的权值，这里分别介绍两种单源点和两种多源点最短路径算法  

### 单源点最短路径算法

#### Dijkstra 算法
&emsp;Dijkstra是典型的最短路径算法，不允许有负权边存在。算法采用的是贪婪算法，普通方法实现的话，时间复杂度为O(n^2),但可以用最小优先队列或者堆实现，可以将时间复杂度降为O(e + nlog(n)) , 这样的算法速度比bellman算法要快。  
首先假设几个变量并初始化：  
* X={s},X为已计算出最短距离的点集合，初始化时包含源点  
* A[s]=0, A[]为数组，记录点s到各点的最短距离，初始化A[s]=0
* B={},逐渐加入的连接成最短路径的边，初始化为空  

算法步骤：  

1. 对于所有边e(v,w),其中v in X, u not in X,遍历这些边，选择使A[v] + w(v,u) 最小的边  
2. 将点 u 加入点集合 X 中  
3. 设置 A[u] = A[v] + w(v,u)  
4. 设置 B(u) = B(v) U e(v,u)
5. 循环执行1-4，直至所有点都在 X 中， 则所有点到源点 s 的最短距离计算完成

#### Bellman-Ford算法

&emsp;该算法可以有效快速的求解单源点最短路径问题适用范围：  

1. 单源点最短路径算法，即计算图G=(V,E)中某一源点s到任意某一点v的距离  
2. 有向图或者无向图  
3. 边权可正可负  

首先假设几个变量：  

* d(x)表示原点到点 x 的距离  
* w(u,v)表示 u，v两点之间的距离  

关于松弛计算概念：  
&emsp; 对某一条边e(u,v)，如果 d(u) > d(u) + w(u,v)，则 d(v) = d(u) + w(u,v)  

算法步骤:  

1. 初始化所有点到源点的距离。即初始化源点到源点 d(s) = 0, 其他任意点 d(v) = 正无穷    
2. 计算最短路径。进行循环， 循环下标从 1到n-1， 每个循环内则遍历所有边， 对每条边进行松弛计算  
3. 检测是否有负权边成环。判断是否存在  d(v) > d(u) + w(u,v), 若存在， 则证明有负权边成环， 返回false， 该图无法求单源点最短路径  

由上述算法可以发现，这个算法的时间复杂度是O(ne), 而且源点到各点的距离要等到最后循环完成之后才能确定。  

### 多源点最短路径

计算有向图中任意两点之间的距离,可以认为是对单源最短路径的推广

#### Floyd-warshall 算法

&emsp;该算法理解起来比较简单， 就是通过检查每条边来确定这条边是不是更短路径的一部分，适用范围：  

1. 多源点最短路径  
2. 有向图  
3. 可以有负权边  

算法伪码步骤：  

1. 初始化：  

		dist[i][j] = 0, if i=j;		
				   = w[i][j], if i!=j and e(i,j) in E;	
				   = infinite , if i!=j and e(i,j) not in E;	
				
2. 计算最短路径：

		for  k=1 to n	
			for i=1 to n	
				for j=1 to n	
					if( dist[i][k] + dist[k][j] ) < dist[i][j]	
						dist[i][j] = dist[i][k] + dist[k][j]	


以上可知，该算法也是贪婪算法，时间复杂度为 O(n^3)，所以效率比较低  

#### Johnson算法  

&emsp;适用范围：  

1. 多源点有向图  
2. 边权可负无负环  
3. 在有负权边时，该算法效率最高  

&emsp;该算法想法比较奇妙，考虑利用bellmanford算法和dijkstra算法，因此需要先加入了一个点到原图。还采用了reweighting的思想，由于Dijkstra算法不允许负权边存在，需要reweighting所有权边，如果G有负权边， reweighting之后所有的边都是非负权边  
算法步骤:  

1. 加入新节点 s 到原图 G 中构成图 G’，而且所有点到点 s 的距离都设为 0 。  
2. 运行Bellman-Ford算法，计算 G‘中源点 s 到其他所有点的最短距离，检测到有负环时退出  
3. 假设对点 v in V, P(v) 表示点 s 到点 v 的最短距离, e(u,v)表示u,v两点间边的权值，
   则reweighting 所有的权边，e(u,v)' = e(u,v) + P(u) -P(v)    
4. 利用reweighting之后的新权边e'，对 G 中的每一个点v分别作为源点运行Dijkstra算法，即可得到d(u,v)'  
5. 对每一对u.v in G, 计算 d(u,v) = d(u,v)' - P(u) + p(v)

这个算法看起来比较复杂，但是其实效率比较高，步骤1的时间复杂度是O(n),步骤2的是O(ne),步骤3的是O(e),步骤4的则是O(enlogn)，步骤5的是O(n^2)，则最终这个算法的时间复杂度是O(enlogn)，比floyd的效率要高。







