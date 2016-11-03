title: 【OJ算法题】谁拿了最多奖学金 
date: 2015-04-14 23:13:57
tags: 数据结构 算法
categories: OJ
---
#描述
某校的惯例是在每学期的期末考试之后发放奖学金。发放的奖学金共有五种，获取的条件各自不同：
1)     院士奖学金，每人8000元，期末平均成绩高于80分（>80），并且在本学期内发表1篇或1篇以上论文的学生均可获得；

2)     五四奖学金，每人4000元，期末平均成绩高于85分（>85），并且班级评议成绩高于80分（>80）的学生均可获得；

3)     成绩优秀奖，每人2000元，期末平均成绩高于90分（>90）的学生均可获得；

4)     西部奖学金，每人1000元，期末平均成绩高于85分（>85）的西部省份学生均可获得；

5)     班级贡献奖，每人850元，班级评议成绩高于80分（>80）的学生干部均可获得；

只要符合条件就可以得奖，每项奖学金的获奖人数没有限制，每名学生也可以同时获得多项奖学金。例如姚林的期末平均成绩
是87分，班级评议成绩82分，同时他还是一位学生干部，那么他可以同时获得五四奖学金和班级贡献奖，奖金总数是4850元。

现在给出若干学生的相关数据，请计算哪些同学获得的奖金总数最高（假设总有同学能满足获得奖学金的条件）。

#输入格式
输入的第一行是一个整数N（1 <= N <= 100），表示学生的总数。接下来的N行每行是一位学生的数据，从左向右依次是姓名，
期末平均成绩，班级评议成绩，是否是学生干部，是否是西部省份学生，以及发表的论文数。姓名是由大小写英文字母组成的
长度不超过20的字符串（不含空格）；期末平均成绩和班级评议成绩都是0到100之间的整数（包括0和100）；是否是学生干部
和是否是西部省份学生分别用一个字符表示，Y表示是，N表示不是；发表的论文数是0到10的整数（包括0和10）。每两个相邻
数据项之间用一个空格分隔。

#测试样例1
##输入
4
YaoLin 87 82 Y N 0
ChenRuiyi 88 78 N Y 1
LiXin 92 88 N N 0
ZhangQin 83 87 Y N 1 

##输出
ChenRuiyi
9000
28700 


#代码：


				#include <stdio.h>
				#include <stdlib.h>
				#include <windows.h>

				typedef struct Person{
					char name[20];
					int score_avg;
					int score_class;
					char student_union;
					char student_west;
					int paper_num;
					long totle;
					struct Person *next;
				}Person;

				int main(void)
				{
					int num,num1;
					int money=0;
					int max=0;
					char name2[15];
					struct Person *head=(struct Person *)malloc(sizeof(Person));
					struct Person *p1,*p2;
					struct Person *p;
					scanf("%d",&num);
					num1=num;
					p2=head;
					p=head;
					while(num){
					p1=(struct Person *)malloc(sizeof(Person));
					p1->totle=0;
					scanf("%s %d %d %c %c %d",&p1->name,&p1->score_avg,&p1->score_class
						,&p1->student_union,&p1->student_west,&p1->paper_num);
					if(p1->score_avg>80&&p1->paper_num>0)
						p1->totle+=8000;
					if(p1->score_avg>85&&p1->score_class>80)
						p1->totle+=4000;
					if(p1->score_avg>90)
						p1->totle+=2000;
					if(p1->score_avg>85&&p1->student_west=='Y')
						p1->totle+=1000;
					if(p1->score_class>80&&p1->student_union=='Y')
						p1->totle+=850;
					if(max<p1->totle)
					{
						max=p1->totle;
						strcpy(name2,p1->name);
					}
						
					money+=p1->totle;
					p2->next=p1;
					p2=p2->next;
					p2->next=NULL;
					num--;
					}
					printf("%s\n%d\n%d",name2,max,money);
					system("pause");
				}



