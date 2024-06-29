+++
draft = false
author = "CPoet"
title = "数据结构基本算法录入"
date = "2018-10-26T23:46:25+08:00"
description = ""
tags = ["数据结构", "算法"]
categories = [
    "coding/c",
]
+++​​
## 说明

本文主要收录《数据结构（C语言版）》第五版的基本算法，算法包括顺序表，栈，队列等。收录的算法是使用C语言实现的，在调用的时候请注意传参的类型。特别是对指针参数的传入。

## 宏&基本算法

### 宏定义（所有算法必须引入）

```c
/*
**数据结构头文件
**宏定义&基本算法及结构
**asorb&201810
*/
#include<stdio.h>
#include<stdlib.h>

//状态码
#define TRUE 1
#define FALSE 0
#define OK 1
#define ERROR 0
#define INFEASIBLE -1
#define OVERFLOW -2
//函数返回状态码类型
typedef int Status;

线性表&顺序表

/*线性表.顺序表&结构&算法*/
typedef int ElemType;

#define LIST_INIT_SIZE 100		//线性表存储空间的初始分配量
#define LISTINCREMENT 10		//线性表存储空间的分配增量
typedef struct {
	ElemType *elem;
	int length;			//当前长度
	int listsize;		//当前分配的存储容量（以sizeof(ElemType)为单位）
}SqList;

Status InitList_Sq(SqList *L){
	L->elem = (ElemType*)malloc(sizeof(ElemType)*LIST_INIT_SIZE);
	if (!L->elem) exit(OVERFLOW);		//空间分配失败
	L->length = 0;
	L->listsize = LIST_INIT_SIZE;
	return OK;
}//构造一个空的线性表L
Status DestroyList_Sq(SqList *L){
	free(L->elem);
	if (L->elem) return ERROR;
	L->length = 0;
	L->listsize = 0;
	return OK;
}//销毁线性表L
Status ClearList_Sq(SqList *L){
	L->length = 0;
	return OK;
}//将L重置为空表
Status ListEmpty_Sq(SqList L){
	if (L.length == 0)return TRUE;
	else return FALSE;
}//若L为空表，则返回TRUE,否则返回FALSE
int ListLength_Sq(SqList L){
	return L.length;
}//返回L中的元素个数
Status GetElem_Sq(SqList L,int i, ElemType *e){
	if (i<1 || i>L.length)return ERROR;
	*e = *(L.elem + i - 1);
	return OK;
}//用e返回L中第i个元素的值
int LocateElem_Sq(SqList L, ElemType e, Status compare(ElemType,ElemType)){
	int i = 0;
	while (i < L.length){
		if (compare(*(L.elem + i), e))
			return i + 1;
		i++;
	}
	return 0;
}//返回L中第一个与e满足关系compare()的数据元素的位序。若这样的元素不存在，则返回值为0
Status PriorElem_Sq(SqList L, ElemType cur_e, ElemType *pre_e){
	for (int i = 1; i < L.length; i++){
		if (*(L.elem + i) == cur_e){
			*pre_e = *(L.elem + i - 1);
			return TRUE;
		}
	}
	return FALSE;
}//用pre_e返回cur_e的前驱
Status NextElem_Sq(SqList L, ElemType cur_e, ElemType *next_e){
	for (int i = 0; i < L.length - 1; i++){
		if (*(L.elem + i) == cur_e){
			*next_e = *(L.elem + i + 1);
			return TRUE;
		}
	}
	return FALSE;
}//用next_e返回cur_e的后继
Status ListInsert_Sq(SqList *L, int i, ElemType e){
	if (i<1 || i>L->length + 1)return ERROR;
	if (L->length >= L->listsize){//空间不足，增加空间
		L->elem = (ElemType*)realloc(L->elem, sizeof(ElemType)*(L->listsize + LISTINCREMENT));
		if (!L->elem)exit(OVERFLOW);			//分配失败
		L->listsize += LISTINCREMENT;
	}
	for (int ii = L->length; ii >= i; ii--){//i位以后开始后移
		*(L->elem + ii) = *(L->elem + ii - 1);
	}
	*(L->elem + i - 1) = e;
	L->length++;
	return OK;
}//在L中的第i个位置前插入e，L的长度加1
Status ListDelete_Sq(SqList *L, int i, ElemType *e){
	if (i < 1 || i>L->length)return ERROR;
	*e = *(L->elem + i - 1);
	for (int ii = i - 1; ii < L->length - 1; ii++){//i位以后前移覆盖
		*(L->elem + ii) = *(L->elem + ii + 1);
	}
	L->length--;
	return OK;
}//删除L的第i的数据元素，并用e返回其值，L的长度减1
Status ListTraverse_Sq(SqList L, Status visit(ElemType*)){
	if (L.length == 0)return ERROR;
	for (int i = 0; i < L.length; i++)
	if (!visit(L.elem + i))return ERROR;
	return OK;
}//依次对L中的每个数据元素调用visit()
```

### 线性表&链表

```c
/*线性表.链表&结构&算法*/
typedef struct LNode{
	ElemType data;
	struct LNode *next;
}*Link,Position;
typedef struct{
	Link head, tail;		//分别指向线性链表中的头结点和最后一个结点
	int len;		//指示线性表元素的个数
}LinkList;
Status MakeNode(Link *p, ElemType e){
	(*p) = (Link)malloc(sizeof(Position));
	if (!(*p))return ERROR;
	(*p)->data = e;
	(*p)->next = NULL;
	return OK;
}//分配由p指向的值为e的结点，并返回OK，否则返回ERROR
void FreeNode(Link p){
	free(p);
}//释放p所指的结点

Status InitList_L(LinkList *L){
	L->head = (Link)malloc(sizeof(Position));
	if (!L->head)exit(OVERFLOW);	//空间分配失败
	L->head->next = NULL;
	L->tail = L->head;
	L->len = 0;
	return OK;
}//构造一个空的线性链表L
Status DestroyList_L(LinkList *L){
	L->tail = L->head->next;
	while (L->tail){
		L->head->next = L->tail->next;
		free(L->tail);
		L->tail = L->head->next;
	}
	free(L->head);
	L->len = 0;
	return OK;
}//销毁L，L不在存在
Status ClearList_L(LinkList *L){
	L->tail = L->head->next;
	while (L->tail){
		L->head->next = L->tail->next;
		free(L->tail);
		L->tail = L->head->next;
	}
	L->tail = L->head;
	L->len = 0;
	return OK;
}//重置L为空表，释放原链表的结点空间
Status InsFirst_L(Link h, Link s){
	s->next = h->next;
	h->next = s;
	return OK;
}//已知h指向线性表头结点，将s所指结点插入在第一个结点之前
Status DelFirst_L(Link h, Position *q){
	Link p = h->next;
	if (!p)return ERROR;		//表空
	h->next = p->next;
	*q = *p;
	free(p);
	return OK;
}//已知h指向线性表头结点，删除链表中的第一个结点并以q返回
Status Append_L (LinkList *L, Link s){
	L->tail->next = s;
	while (s){
		L->tail = s;
		L->len++;
		s = s->next;
	}
	return OK;
}//把s所指指针接在L的最后一个结点之后，并改变链表L的尾指针指向新的尾结点
Status Remove_L(LinkList *L, Position *q){
	Link p = L->tail;
	if (L->head == L->tail)return ERROR;		//表空
	*q = *(L->tail);
	L->tail = L->head;
	while (L->tail->next != p)
		L->tail = L->tail->next;
	free(p);
	L->len--;
	return OK;
}//删除线性表L中的尾结点并以返回，改变链表L的尾指针指向新的尾结点
Status InsBefore_L(LinkList *L, Link p, Link s){
	Link q = L->head->next;
	while (q->next != p&&q->next != NULL)
		q = q->next;
	q->next = s;
	s->next = p;
	p = s;
	L->len++;
	return OK;
}//已知p指向线性表L中的一个结点，将s所指结点插入在p所指结点前
Status InsAfter_L(LinkList *L, Link p, Link s){
	Link q = p->next;
	p->next = s;
	s->next = q;
	if (p == L->tail)
		L->tail = s;
	L->len++;
	return OK;
}//已知p指向线性表L中的一个结点，将s所指结点插入在p所指结点后
Status SetCurElem_L(Link p, ElemType e){
	p->data = e;
	return OK;
}//已知p指向线性表中的一个结点，用e更新p所指结点的值
ElemType GetCurElem_L(Link p){
	return p->data;
}//已知p指向线性表中的一个结点，返回p所指结点数据元素的值
Status ListEmpty_L(LinkList L){
	if (L.len == 0)return TRUE;
	else return FALSE;
}//若线性表L为空，则放回TRUE，否则返回FALSE
int ListLength_L(LinkList L){
	return L.len;
}//返回线性表L中的元素个数
Position* GetHead_L(LinkList L){
	return L.head;
}//返回L中头结点位置
Position* GetLast_L(LinkList L){
	return L.tail;
}//返回线性表L中最后一个结点位置
Position* PriorPos_L(LinkList L, Link p){
	Link q = L.head->next;
	while (q->next != p&&q != NULL)
		q = q->next;
	return q;
}//已知p指向L中的一个结点，返回其前驱
Position* NextPos_L(LinkList L, Link p){
	return p->next;
}//已知p指向线性表L中的一个结点，返回p所指结点的直接后继位置
Status LocatePos_L(LinkList L, int i, Link *p){
	if (i<1 || i>L.len)return ERROR;
	*p = L.head;
	for (i; i >= 1; i--){
		*p= (*p)->next;
	}
	return OK;
}//返回p指示线性表L中第i个结点的位置并返回OK，i不合法返回ERROR
Position* LocateElem_L(LinkList L, ElemType e, Status(*compare)(ElemType, ElemType)){
	Link p = L.head;
	while (p){
		if ((*compare)(e, p->data))
			return p;
		p = p->next;
	}
	return NULL;
}//返回线性表中第一个与e满足函数compare()判定关系的元素的位置，没有返回NULL
Status ListTraverse_L(LinkList L, Status(*visit)(ElemType)){
	Link p = L.head;
	while (p){
		if (!(*visit)(p->data))return ERROR;
		p = p->next;
	}
	return OK;
}//依次对L的每个元素调用函数visit()。一旦失败，则操作失败
```

### 栈

```c
/*栈&结构&算法*/
typedef int SElemType;

#define STACK_INIT_SIZE 100		//存储空间初始分配量
#define STACKINCREMENT 10		//存储空间分配增量

typedef struct{
	SElemType *base;		//在栈构造之前和销毁之后，base的值为NULL
	SElemType *top;			//栈顶指针
	int stacksize;		//当前已分配的存储空间，以元素为单位
}SqStack;

Status InitStack(SqStack *S){
	S->base = (SElemType*)malloc(sizeof(SElemType)*STACK_INIT_SIZE);
	if (!S->base)exit(OVERFLOW);		//分配失败
	S->top = S->base;
	S->stacksize = STACK_INIT_SIZE;
	return OK;
}//构造一个空栈S
Status DestroyStack(SqStack *S){
	free(S->base);
	S->base = NULL; S->top = NULL;
	S->stacksize = 0;
	return OK;
}//销毁栈S，S不再存在
Status ClearStack(SqStack *S){
	S->top = S->base;
	return OK;
}//把栈S置空
Status StackEmpty(SqStack S){
	if (S.base == S.top)return TRUE;
	else return FALSE;
}//栈为空返回TRUE，否则返回FALSE
int StackLength(SqStack S){
	int SElem_num = 0;
	SElem_num = S.top - S.base;
	return SElem_num;
}//放回S的元素个数，即栈的长度
Status GetTop(SqStack S, SElemType *e){
	if (StackEmpty(S))return ERROR;		//栈空
	*e = *(S.top - 1);
	return OK;
}//若栈不空，则用e返回S的栈顶元素，并返回OK，否则返回ERROR
Status Push(SqStack *S, SElemType e){
	if (S->top - S->base >= S->stacksize){
		//栈满，追加空间
		S->base = (SElemType*)realloc(S->base, sizeof(SElemType)*(S->stacksize + STACKINCREMENT));
		if (!S->base)exit(OVERFLOW);		//分配失败
		S->top = S->base + S->stacksize;
		S->stacksize += STACKINCREMENT;
	}
	*S->top ++ = e;
	return OK;
}//插入元素e为新的栈顶元素
Status Pop(SqStack *S, SElemType *e){
	if (StackEmpty(*S))return ERROR;		//栈空
	*e = * -- S->top;
	return OK;
}//若栈不空，则删除S的栈顶元素，用e返回其值，并返回OK；否则返回ERROR
Status StackTraverse(SqStack S, Status(*visit)(SElemType *e)){
	if (StackEmpty(S))return ERROR;		//栈空
	for (int i = 0; S.base + i < S.top; i++)
		if(!(*visit)(S.base + i))return ERROR;
	return OK;
}//从栈低依次对栈中的每个元素调用函数visit()。一旦visit()失败，则操作失败
```

### 队列

```c
/*队列&结构&算法*/
/*单链队列，队列的链式存储结构*/
typedef int QElemType;

typedef struct QNode{
	QElemType data;
	struct QNode *next;
}QNode,*QueuePtr;
typedef struct{
	QueuePtr front;		//队头指针
	QueuePtr rear;		//队尾指针
}LinkQueue;

Status InitQueue(LinkQueue *Q){
	Q->front = (QueuePtr)malloc(sizeof(QNode));
	if (!Q->front)exit(OVERFLOW);		//空间分配失败
	Q->rear = Q->front;
	return OK;
}//构造一个空队列
Status DestroyQueue(LinkQueue *Q){
	while (Q->front){
		Q->rear = Q->front->next;
		free(Q->front);
		Q->front = Q->rear;
	}
	return OK;
}//销毁队列Q，Q不在存在
Status ClearQueue(LinkQueue *Q){
	QueuePtr p = Q->front;
	while (p){
		Q->rear = p->next;
		free(p);			//逐个释放空间
		p = Q->rear;
	}
	Q->rear = Q->front;		//置空
	return OK;
}//将Q清为空队列
Status QueueEmpty(LinkQueue Q){
	if (Q.rear == Q.front)return TRUE;
	else return FALSE;
}//若队列Q为空队列，则返回TRUE，否则返回FALSE
int QueueLength(LinkQueue Q){
	int QueueL = 0;
	QueueL = Q.rear - Q.front;
	return QueueL;
}//返回Q的元素个数，即为队列的长度
Status GetHead(LinkQueue Q, QElemType *e){
	if (QueueEmpty(Q)) return ERROR;		//队空
	*e = Q.front->next->data;
	return OK;
}//若e不空，用e返回Q队头元素，并返回OK，否则返回ERROR
Status DeQueue(LinkQueue *Q, QElemType *e){
	QueuePtr p = NULL;
	if (QueueEmpty(*Q))return ERROR;
	p = Q->front->next;
	Q->front->next = p->next;
	*e = p->data;
	if (Q->rear == p)Q->rear = Q->front;
	free(p);
	return OK;
}//若e不空，则删除Q的队头元素，用e返回其值，并返回OK，否则ERROR
Status QueueTraverse(LinkQueue Q, Status visit(QElemType*)){
	QueuePtr p;
	if (QueueEmpty(Q))return ERROR;
	p = Q.front->next;
	for (int i = QueueLength(Q); i > 0; i--){
		if (!visit(&(p->data)))return ERROR;
		p = p->next;
	}
	return OK;
}//从队头到队尾，依次对Q的每个元素调用函数visit()，失败则返回ERROR
```

## 结尾
> 参考：数据结构（C语言版）第五版-严蔚敏
