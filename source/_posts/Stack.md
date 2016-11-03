title: 栈
date: 2015-04-11 21:22:43
tags: 数据结构
categories:	数据结构
---
  今天稍微复习了一下栈的用法。栈是种比较重要的线性结构，栈的特点
  在于后进先出(LIFO结构)。
  栈的实现有两种存储表示方法：
	1.顺序栈，即利用数组来进行存储，利用一组地址连续的存储单元依次
	  存放自栈底到栈顶的数据元素，用top来指示栈顶元素在顺序栈中的位
	  置。
	  但是，这样的存储方式有个弊端，由于栈在使用过程中所需最大空间的
	  大小很难控制，所以，一般先为栈分配一个基本容量，在存储过程中，
	  当栈的空间不够用时，在动态地为栈增加内存空间。因此，需要给栈定
	  义两个常量STACK_INIT_SIZE(初始分配量)和STACK_INCREMENT(存储空间
	  分配增量)。 
		#define STACK_INIT_SIZE 100;
		#define STACK_INCREMENT 10;
		typedef struct {
			SElemType *base;
			SElemType *top;
			int stacksize;
		}SqStack;
		
		Status InitStack(SqStack &S){ //栈的初始化
			S.base = (SElemType *)malloc(STACK_INIT_SIZE*sizeof(SqlStack));
			if(!S.base)
				return Error;
			S.top = S.base;
			S.stacksize = STACK_INIT_SIZE;
			return OK;
		}
		Status GetTop(SqlStack S,SElemType &e){
			if(S.top == S.base)
				return Error;
			e = *(S.top-1);
			return OK;
		}
		Status Push(SqlStack S,SElemType &e){
			if((S.top - S.base)>S.stacksize){
				
			}
		}
		
		