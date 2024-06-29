+++
draft = false
author = "CPoet"
title = "数据结构串实现字符串加密"
date = "2018-11-14T00:05:55+08:00"
description = "大学数据结构课程作业"
tags = []
categories = [
    "coding/c",
]
+++
## 说明
> 具体说明可以参照《数据结构串实现单词统计》中的说明。

## 问题描述

一个文本串可用事先给定的字母映射表进行加密。例如，设字母映射表为：

a b c d e f g h i j k l m n o p q r s t u v w x y z

n g z q t c o b m u h e l k p d a w x f y I v r s j

则字符串“encrypt”被加密为“tkzwsdf”。

基本要求：

① 编写一个算法将输入的文本串进行加密后输出

② 编写一个算法，将输入的已加密的文本串解密后输出

③ 编写主函数进行测试

## 实现算法

### 头文件
```c
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

### 主函数
```c
/**
**实验3 002
**ASorb time:201811
**/
#include"DataH.h"
#define MAX_CHARS 100
Status key_op(HString S,char m_chars[],char j_chars[]) {
	char m_key[] = { "abcdefghijklmnopqrstuvwxyz" };		//明文
	char j_key[] = { "ngzqtcobmuhelkpdawxfyIvrsj" };		//暗文
	for (int i = 0; i < S.length; i++) {
		for (int ii = 0; ii < 26; ii++)
			if (S.ch[i] == m_key[ii]) {
				j_chars[i] = j_key[ii];
				break;
			}
			else
				j_chars[i] = S.ch[i];
		for (int ii = 0; ii < 26; ii++)
			if (S.ch[i] == j_key[ii]) {
				m_chars[i] = m_key[ii];
				break;
			}
			else
				m_chars[i] = S.ch[i];
	}
	return OK;
}
int main() {
	HString S; S.ch = NULL;
	char chars[MAX_CHARS], m_chars[MAX_CHARS] = { '\0' }, j_chars[MAX_CHARS] = { '\0' };
	printf("输入需要转换的密码：");
	gets_s(chars);
	StrAssign(&S, chars);
	key_op(S, m_chars, j_chars);
	ClearString(&S);
	printf("你输入的key：%s\n",chars);
	printf("明文key：%s\n", m_chars);
	printf("暗文key：%s\n", j_chars);
	getchar();
	return 0;
}
```
