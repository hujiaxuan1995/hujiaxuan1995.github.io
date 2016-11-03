title: 【OJ算法题】P1038忠诚 
date: 2015-04-18 23:13:50
tags: 算法 数据结构 快速排序
categories: OJ 
---
#描述
老管家是一个聪明能干的人。
他为财主工作了整整10年，财主为了让自已账目更加清楚。
要求管家每天记k次账，由于管家聪明能干，因而管家总是让财主十分满意。
但是由于一些人的挑拨，财主还是对管家产生了怀疑。
于是他决定用一种特别的方法来判断管家的忠诚，他把每次的账目按1，2，3…编号，然后不定时的问管家问题，
问题是这样的：在a到b号账中最少的一笔是多少？
为了让管家没时间作假他总是一次问多个问题。
#输入格式
输入中第一行有两个数m,n表示有m(m<=100000)笔账,n表示有n个问题，n<=100000。
第二行为m个数,分别是账目的钱数
后面n行分别是n个问题,每行有2个数字说明开始结束的账目编号。
##输入
10 3
1 2 3 4 5 6 7 8 9 10
2 7
3 9
1 10
##输出
2 3 1
	 
#程序：
				#include <stdio.h>
				#include <stdlib.h>
				#include <windows.h>
				int main(void){
					int m,n=0;
					int i=0,j=0,tmp;
					int  *arraylist;
					long *arraylist2;
					int *arraylist3;
					scanf("%d %d",&m,&n);
					i=m;
					arraylist=(int *)calloc(m,sizeof(int));
					arraylist2 = (long *)calloc(n*2,sizeof(long));
					arraylist3 = (int *)calloc(n,sizeof(int));
					for(i=0;i<m;i++)
					{
					   scanf("%d",&tmp);
					   arraylist[i]=tmp;	
					}	
					for(i=0;i<n*2;i=i+2)
					{
						scanf("%d %d",&arraylist2[i],&arraylist2[i+1]);
					}
					for(i=0;i<n;i++)
					{
						int tmp=arraylist2[2*i]-1;
						arraylist3[i]=arraylist[tmp];
						for(j=arraylist2[2*i]-1;j<arraylist2[2*i+1];j++)
						{
							if(arraylist3[i]>arraylist[j])
							{
								arraylist3[i]=arraylist[j];
							}
						}
					}
					for(i=0;i<n;i++)
					{
						printf("%d ",arraylist3[i]);
					}
					system("pause");
				}
	
这是一个典型的RMQ问题。
处理RMQ问题的主要方法有：
1.朴素算法（即搜索），算法复杂度为O(n)。
2.线段树 O(n)-O(qlog n)
3.ST(动态规划) O(nlogn -1)

程序虽然运行结果正确，但是当m=100000时，朴素算法仍然超时了。超时的原因在于代码中
从一行数据中选择一个最小值的操作时间复杂度是O(n)。这样的数据规模，即使是快速排序
或堆排序也无法在规定时间内完成。

线段树可以很好地解决这个问题。关于线段树的详细介绍可以戳下面的链接：
[线段树的详解](http://blog.csdn.net/metalseed/article/details/8039326)

线段树方法：
		#include<iostream>
		#include<cstdio>
		#include <windows.h>
		using namespace std;
		int n,q,a[100001];

		struct data{
		   int l,r,mn;
		}tr[400001];

		void build(int k,int s,int t)
		{
			tr[k].l=s;tr[k].r=t;
			if(s==t){tr[k].mn=a[s];return;}
			int mid=(s+t)>>1;
			build(k<<1,s,mid);
			build(k<<1|1,mid+1,t);
			tr[k].mn=min(tr[k<<1].mn,tr[k<<1|1].mn);
		}
		int ask(int k,int s,int t)
		{
			int l=tr[k].l,r=tr[k].r;
			if(s==l&&t==r)return tr[k].mn;
			int mid=(l+r)>>1;
			if(t<=mid)return ask(k<<1,s,t);
			if(s>mid)return ask(k<<1|1,s,t);
			return min(ask(k<<1,s,mid),ask(k<<1|1,mid+1,t));
		}
		int main()
		{
			int *arraylist;

			scanf("%d%d",&n,&q);
			arraylist = (int *)calloc(q,sizeof(int));
			for(int i=1;i<=n;i++)
			   scanf("%d",&a[i]);
			build(1,1,n);
			for(int i=1;i<=q;i++)
			{
				int x,y;
				scanf("%d%d",&x,&y);
				arraylist[i-1]=ask(1,x,y);
			}
			for(int i=0;i<3;i++)
			{
				printf("%d ",arraylist[i]);
			}
			system("pause");
		}

		线段树是比较重要的一种数据结构，应该掌握。
