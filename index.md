# 数据结构精讲

## 堆/priority_queue

1. 求 $k$ 小数， 即 $k$ 分位数 

	CSP 普及组有一道这样的题目，虽然当时考场上性价比最高的做法是桶排序，但是堆也可以 [直播获奖](https://www.luogu.com.cn/problem/P7072)

	直播获奖解法：用小根堆维护目前的分数线，大根堆维护剩下的人的分数，如果小根堆需要人就从大根堆中弹出元素到小根堆当中就可以了
	
2. 扫描线

求所有矩形面积（扫描线）

![image](https://user-images.githubusercontent.com/107249565/193509057-2d9bc191-1105-448e-afdf-d4ff1d395dcd.png)

将每一段扫描线先 priority queue 然后把没两段之间的宽度乘以最高的高度再加起来就是答案




