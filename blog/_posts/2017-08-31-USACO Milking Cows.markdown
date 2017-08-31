---
layout: default
title:  "USACO Milking Cows"
tags: [编程,算法,OI,USACO]
date:   2017-08-31 10:26
categories: jekyll update
---
> USACO Milking Cows题解分析

1. 题目：
- DESCRIPTION  
Three farmers rise at 5 am each morning and head for the barn to milk three cows. The first farmer begins milking his cow at time 300 (measured in seconds after 5 am) and ends at time 1000. The second farmer begins at time 700 and ends at time 1200. The third farmer begins at time 1500 and ends at time 2100. The longest continuous time during which at least one farmer was milking a cow was 900 seconds (from 300 to 1200). The longest time no milking was done, between the beginning and the ending of all milking, was 300 seconds (1500 minus 1200).  
Your job is to write a program that will examine a list of beginning and ending times for N (1 <= N <= 5000) farmers milking N cows and compute (in seconds):  
The longest time interval at least one cow was milked.  
The longest time interval (after milking starts) during which no cows were being milked.  
- INPUT FORMAT  
Line 1:	The single integer, N  
Lines 2..N+1:	Two non-negative integers less than 1,000,000, respectively the starting and ending time in seconds after 0500  
SAMPLE INPUT (file milk2.in)  
```
3  
300 1000  
700 1200  
1500 2100  
```
- OUTPUT FORMAT  
A single line with two integers that represent the longest continuous time of milking and the longest idle time.  
SAMPLE OUTPUT (file milk2.out)  
```
900 300
```

2. 思路与想法  
可以把Milking Time想象为线段，你就可以直观感受到这到题目的大概思路：  
对输入的线段与现有线段进行判断，能否合并，如果可以就合并并继续，如果不可以就进行两个答案存取并继续  
如果只是单纯的实现这个解法，你就会在这个测试数据面前WA：  
```
------- test 1 [length 10 bytes] ----
1
100 200
```
当然解决方案也很简单，就只需要在最后循环结束时再进行一次答案存取即可。  
但实际上这个想法还有有缺陷，我们来看测试数据：  
```
------- test 3 [length 54 bytes] ----
10
2 3
4 5
6 7
8 9
10 11
12 13
14 15
16 17
18 19
1 20
```
前面的数据全都没问题，但是最后的```1 20```就会使得no cows were being milked的时间段变为0。但显然之前的解法会输出```19 1```。怎么办呢？  
我前后进行过多种方法的变换。但实际上，只需要按照开始时间排序即可。  
为什么排序就可以解决这个问题？因为排序之后你会按照顺序处理，如果你的线段无法合并，那后面一定不会出现数据再来覆盖这一段，也就保证了这样的致命问题不会出现。  
当然，这道题还有一个坑，就是你必须使用快速排序及以上的高速排序算法。因为最后的测试数据很大。  
<br>
<hr>
C程序实现：（这段代码是没有fclose，在竞赛时切勿这样，我是参照USACO的写法，所以没有fclose）
```c
/*
ID: lexugey1
LANG: C
PROG: milk2
*/
#include <stdio.h>

struct xy {
  int x;
  int y;
};
struct xy a[5001];

int swap(struct xy *a,struct xy *b)
{
  struct xy t;
  t=*a;
  *a=*b;
  *b=t;
  return 0;
}

int Partition(int first, int end)
{
  int i=first,j=end;
  while (i<j)
  {
    while (i<j&&a[i].x<=a[j].x) j--;
    if (i<j)
    {
      swap(&a[i],&a[j]);
      i++;
    }
    while (i<j&&a[i].x<=a[j].x) i++;
    if (i<j)
    {
      swap(&a[i],&a[j]);
      j--;
    }
  }
  return i;
}

void QuickSort(int first, int end)
{
  if (first<end)
    {
      int pivot=Partition(first,end);
      QuickSort(first,pivot-1);
      QuickSort(pivot+1,end);
    }
}


int min(int a,int b)
{
  if (a<b) return a;
  else return b;
}

int max(int a,int b)
{
  if (a>b) return a;
  else return b;
}

int main()
{
  int i=0,n=0,max1=0,max2=0,p=0,q=0;

  FILE *fin  = fopen ("milk2.in", "r");
  FILE *fout = fopen ("milk2.out", "w");
  // FILE *fin  = stdin;
  // FILE *fout = stdout;

  fscanf(fin, "%d", &n);
  for (i=1;i<=n;i++)
    fscanf(fin,"%d %d",&a[i].x,&a[i].y);

  QuickSort(1,n);

  p=a[1].x;q=a[1].y;
  for (i=2;i<=n;i++)
  {
    if ((a[i].x<=q)&&(a[i].y>=p))
    {
      p=min(a[i].x,p);
      q=max(a[i].y,q);
    }
    else
    {
      if (q-p>max1) max1=q-p;
      if (a[i].x-q>max2) max2=a[i].x-q;
      p=a[i].x;q=a[i].y;
    }
  }
  if (q-p>max1) max1=q-p;
  if (a[n].x-q>max2) max2=a[n].x-q;
  fprintf(fout,"%d %d\n", max1,max2);
  return 0;
}
```
