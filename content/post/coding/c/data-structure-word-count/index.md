+++
draft = false
author = "CPoet"
title = "数据结构串实现单词统计"
date = "2018-11-14T00:09:17+08:00"
description = ""
tags = []
categories = [
    "coding/c",
]
+++
## 说明
前期发得数据结构的算法都是把书上的基本算法和实际问题的算法分离的，现在会把基本算法的头一起发在同一篇博文中，以便查找。（往期的基本算法日志不在更新）。

## 问题描述
输入一个由若干单词组成的文本行（最多200个字符），每个单词之间用若干个空格隔开，统计此文本行中单词的个数，并在其中查找任意输入的单词。

## 算法

### 头文件（DataH.h）

> 注意：这里头文件中的基本算法包含“数据结构C语言版--严蔚敏”书籍中串的“抽象数据类型ADT”中的基本算法，在实际问题中有的基本算法可能不会被用到。

```C++
#include<stdio.h>
#include<stdlib.h>
/*
**数据结构串头文件
**ASorb time:201811
*/

//状态码
#define TRUE 1
#define FALSE 0
#define OK 1
#define ERROR 0
#define INFEASIBLE -1
#define OVERFLOW -2
//函数返回状态码类型

typedef int Status;
/**串的堆分配存储表示**/
typedef struct {
    char *ch;
    int length;
}HString;

//生成一个其值等于串常量chars的串T
Status StrAssign(HString *T,char *chars){
    int len=0;
    if(T->ch) free(T->ch);
    for(char *c=chars;*c;c++,len++);
    if(!len){
        T->ch = NULL;
        T->length = 0;
    }
    else{
        if(!(T->ch=(char*)malloc(sizeof(char)*len)))
            exit(OVERFLOW);
        for(int i=0;i<len;i++)
            T->ch[i] = chars[i];
        T->length = len;
    }
    return OK;
}
//返回S的元素个数，称为串的长度
int StrLength(HString S){
    return S.length;
}
//串比较
int StrCompare(HString S,HString T){
    for(int i=0;i<S.length && i<T.length;i++)
        if(S.ch[i] != T.ch[i])
            return S.ch[i] - T.ch[i];
    return S.length - T.length;
}
//将S清为空串，并释放S的空间
Status ClearString(HString *S){
    if(S->ch){
        free(S->ch);
        S->ch = NULL;
    }
    S->length = 0;
    return OK;
}
//用T返回有S1和S2连接而成的新串
Status Concat(HString *T,HString S1,HString S2){
    if(T->ch)free(T->ch);
    if(!(T->ch=(char*)malloc(sizeof(char)*(S1.length+S2.length))))
        exit(OVERFLOW);
    for(int i=0;i<S1.length;i++)
        T->ch[i] = S1.ch[i];
    for(int i=0;i<S2.length;i++)
        T->ch[i+S1.length]=S2.ch[i];
    T->length = S1.length + S2.length;
    return OK;
}
//返回串S的第pos个字符起长度为len的子串
Status SubString(HString *Sub,HString S,int pos,int len){
    if(pos<1 || pos>S.length || len<0 || len>S.length-pos+1)
        return ERROR;
    if(Sub->ch){
        free(Sub->ch);
        Sub->length = 0;
    }
    if(!len){
        Sub->ch = NULL;
        Sub->length =0;
    }
    else{
        if(!(Sub->ch=(char*)malloc(sizeof(char)*len)))
            exit(OVERFLOW);
        for(int i=0;i<len;i++)
            Sub->ch[i] = S.ch[pos+i-1];
        Sub->length = len;
    }
    return OK;
}
```

### 主函数（main.cpp）
```C++
/**
**实验3 001
**ASorb time:201811
**/
#include"DataH.h"
#define MAX_CHARS 200
//返回单词数量
int word_num(HString S) {
	int num = 0;
	int is_word = 0;
	if (S.length == 0)return num;
	for (int i = 0; i < S.length; i++) {
		if (S.ch[i] != ' ')
			is_word = 1;
		if ((S.ch[i] == ' '|| i==S.length-1) && is_word) {
			num++;
			is_word = 0;
		}
	}
	return num;
}
//统计查找单词的数量并记录位置
Status find_word_num(HString S,char chars[],int position[]) {
	int num = 1;
	HString T; T.ch = NULL;
	HString Sub; Sub.ch = NULL;
	char chars_find[MAX_CHARS];
	StrAssign(&T,chars);
	for (int i = 1; i <= S.length - T.length+1; i++) {
		SubString(&Sub, S, i, T.length);
		if (StrCompare(Sub, T) == 0)
			if ((i == 1 || S.ch[i - 2] == ' ') && (i==S.length-T.length+1||S.ch[i+T.length-1]==' ')) {
				position[0]++;
				position[num++] = i;
			}
	}
	ClearString(&Sub);
	ClearString(&T);
	return OK;
}
int main() {
	HString S;S.ch = NULL;
	char chars[MAX_CHARS], chars_find[MAX_CHARS];
	int position[MAX_CHARS + 1] = { 0 };		//0位置存储数量，其余存储位置
	printf("输入文本（200字符内）：");
	gets_s(chars);
	StrAssign(&S, chars);
	printf("需要查找的单词为：");
	gets_s(chars_find);
	printf("文段单词数量为：%d\n", word_num(S));
	find_word_num(S, chars_find, position);
	printf("查找单词的数量为:%d\n", position[0]);
	if (position[0]) {
		printf("位置记录为：\n");
		for (int i = 1;position[i]; i++)
			printf("%4d", position[i]);
	}
	ClearString(&S);
	getchar();
	return 0;
}
```

## END
