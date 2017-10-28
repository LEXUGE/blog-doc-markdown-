---
layout: post
title:  "USACO Broken Necklace"
tags: [编程,算法,OI,USACO]
date:   2017-08-31 11:14
categories: jekyll update
issueid: 16
---
> USACO Broken Necklace题解分析

1.题目  

- Description  
You have a necklace of N red, white, or blue beads (3<=N<=350) some of which are red, others blue, and others white, arranged at random. Here are two examples for n=29:  
```

                1 2                               1 2  
            r b b r                           b r r b  
          r         b                       b         b  
         r           r                     b           r  
        r             r                   w             r  
       b               r                 w               w  
      b                 b               r                 r  
      b                 b               b                 b  
      b                 b               r                 b  
       r               r                 b               r  
        b             r                   r             r  
         b           r                     r           r  
           r       r                         r       b  
             r b r                             r r w  
            Figure A                         Figure B  
                        r red bead  
                        b blue bead  
                        w white bead  
```
The beads considered first and second in the text that follows have been marked in the picture.  
The configuration in Figure A may be represented as a string of b's and r's, where b represents a blue bead and r represents a red one, as follows: brbrrrbbbrrrrrbrrbbrbbbbrrrrb .  
Suppose you are to break the necklace at some point, lay it out straight, and then collect beads of the same color from one end until you reach a bead of a different color, and do the same for the other end (which might not be of the same color as the beads collected before this).  
Determine the point where the necklace should be broken so that the most number of beads can be collected.  

- Example  
For example, for the necklace in Figure A, 8 beads can be collected, with the breaking point either between bead 9 and bead 10 or else between bead 24 and bead 25.  
In some necklaces, white beads had been included as shown in Figure B above. When collecting beads, a white bead that is encountered may be treated as either red or blue and then painted with the desired color. The string that represents this configuration can include any of the three symbols r, b and w.  
Write a program to determine the largest number of beads that can be collected from a supplied necklace.  

- INPUT FORMAT  
Line 1:	N, the number of beads  
Line 2:	a string of N characters, each of which is r, b, or w  
SAMPLE INPUT (file beads.in)  
```
29
wwwbbrwrbrbrrbrbrwrwwrbwrwrrb
```
- OUTPUT FORMAT  
A single line containing the maximum of number of beads that can be collected from the supplied necklace.  
SAMPLE OUTPUT (file beads.out)
```
11
```
- OUTPUT EXPLANATION  
Consider two copies of the beads (kind of like being able to runaround the ends). The string of 11 is marked.  
```
                Two necklace copies joined here
                             v
wwwbbrwrbrbrrbrbrwrwwrbwrwrrb|wwwbbrwrbrbrrbrbrwrwwrbwrwrrb
                       ******|*****
                       rrrrrb|bbbbb  <-- assignments
                   5xr .....#|#####  6xb

                        5+6 = 11 total
```
<br>
<hr>
2.思路与想法  
题目的OUTPUT EXPLANATION已经给我们了提示，需要将字符串*2形成环状效果。  
本题的解法就是枚举。对所有的切口进行枚举，设切口的左边bead为p，那么从p+n开始往左数，为pre；以p+1开始向右数为post。  
如下，necklace为`wrwwwwrwrbbbw`,设切口在第3个与第四个bead之间，那么p=3。
```
        pre的范围
  切口      v   p+n
   v ~~~~~~~~~~~v
wrw|wwwrwrbbbwwrwwwwrwrbbbw
    ^
    p+1 (post的范围为p+1到pre的范围)
```
为保证最终结果<=n，所以必须确定范围。范围如上图所示。可以证明pre+post<=n（最大为p+n-p-1+1)  
有一个小的点需要注意。就是如果切口处向左或向右为w(white)的情况。  
这种情况下，你需要painted with the desired color，也就是说，在循环的过程中寻找第一个不是w的bead并且将w涂上它的颜色。虽然你不考虑这个，进行一些其他的考虑也能AC，但是算法是错的，AC也没有什么意义。  
<br>
<hr>
C程序实现：
```c
/*
ID: lexugey1
LANG: C
PROG: beads
*/
#include <stdio.h>
#include <string.h>

int n=0;
char s[350],s1[700];

int get_beads(int p)
{
  int pre=0,post=0,i=0,j=0,flag=0;
  i=p+1;j=p+n;
  char c=0;

  c=s1[j];
  while ((flag==0)&&(j-1>p+1))
  {
    if ((c=='w')&&(s1[j]!='w')) c=s1[j]; //涂色处理
    if ((s1[j]==c)||(s1[j]=='w')) pre++;
    else flag=1;
    j--;
  }
  j++;
  c=s1[i];
  flag=0;
  while ((flag==0)&&(i<j))
  {
    if ((c=='w')&&(s1[i]!='w')) c=s1[i]; //涂色处理
    if ((s1[i]==c)||(s1[i]=='w')) post++;
    else flag=1;
    i++;
  }
  return pre+post;
}

int main()
{
  int i=0,max=0;

  FILE *fin  = fopen ("beads.in", "r");
  FILE *fout = fopen ("beads.out", "w");
  // FILE *fin  = stdin;
  // FILE *fout = stdout;

  fscanf(fin,"%d", &n);
  fscanf(fin,"%s", s);
  strcat(s1,s);
  strcat(s1,s);
  for (i=0;i<n;i++)
  {
    if (get_beads(i)>max) max=get_beads(i);
  }
  fprintf(fout,"%d\n", max);
  return 0;
}

```
