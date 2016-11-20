---
title: 【排序算法总结】
tags: []
notebook: 0. 精华整理贴
---

1\. 排序算法的总结：
<table style="border-collapse: collapse; margin-left: 0px; table-layout: fixed;width:98.28203292770222%;">

<tr>
<td style="border: 1px solid rgb(211, 211, 211); padding: 10px; margin: 0px;width:32.702112163146396%;">
<div style="text-align: center"><b>时间复杂度</b></div>
</td>
<td style="border: 1px solid rgb(211, 211, 211); padding: 10px; margin: 0px;width:34.304442825928625%;">
<div style="text-align: center"><b>空间复杂度</b></div>
</td>
<td style="border: 1px solid rgb(211, 211, 211); padding: 10px; margin: 0px;width:32.92061179898034%;">
<div style="text-align: center"><b>稳定性</b></div>
</td>
</tr>
<tr>
<td style="border-style:solid;border-width:1px;border-color:rgb(211,211,211);padding:10px;margin:0px;width:33.33%;">
<div>O(n^2):</div>
<div>
<ul>
<li>冒牌排序</li>
<li>插入排序</li>
<li>选择排序</li>
</ul>
<div><br/></div>
<div>O(n*logn):</div>
<ul>
<li><b>归并排序</b></li>
<li><b>快排</b></li>
<li><b>堆排序</b></li>
<li>希尔排序</li>
</ul>
<div><br/></div>
<div>O(n*logn):</div>
</div>
<ul>
<li>计数排序</li>
<li>基数排序</li>
</ul>
</td>
<td style="border-style:solid;border-width:1px;border-color:rgb(211,211,211);padding:10px;margin:0px;width:33.33%;">
<div>
<div>O(1):</div>
<ul>
<li>插入排序</li>
<li>选择排序</li>
<li>冒泡排序</li>
<li>堆排序</li>
<li>希尔排序</li>
</ul>
<div><br/></div>
<div>O(logN) ~ O(N):</div>
<ul>
<li>快速排序</li>
</ul>
<div><br/></div>
<div>O(N):</div>
<ul>
<li>归并排序</li>
</ul>
<div><br/></div>
<div>O(M):M是桶的数量</div>
</div>
<ul>
<li>基数排序</li>
<li>计数排序</li>
</ul>
</td>
<td style="border-style:solid;border-width:1px;border-color:rgb(211,211,211);padding:10px;margin:0px;width:33.33%;">
<div>稳定的排序算法：</div>
<ul>
<li>冒泡排序</li>
<li>插入排序</li>
<li>归并排序</li>
<li>计数排序</li>
<li>基数排序</li>
<li>桶排序</li>
</ul>
<div><br/></div>
<div>不稳定的排序算法：</div>
<ul>
<li>选择排序</li>
<li>快速排序</li>
<li>希尔排序</li>
<li>堆排序</li>
</ul>
</td>
</tr>

</table>


补充说明：

  * 1\. 算法无优劣，要与排序的元素有关。例如，Udine热你的年龄或者身高进行排序，因为范围比较小，考虑计数排序。但是均匀分布的整数，计数排序就不适合了。面试中除了特殊说明，默认的是均匀分布。
  * 2\. 快速排序不代表更快，最好的情况下，与堆排序和归并排序是相同的，只是快排序的常量系数较小。
  * 3\. 工程上的排序都是综合的，数组较小使用插入排序，数组较大选择快拍或者其他O(n*logn)的算法。

  


  


**各个算法的主要思想：**

  


冒泡排序：

插入排序：

选择排序：

归并排序：

快速排序：

堆排序：

希尔排序：

计数排序：比如身高，数目有限，设置M个桶覆盖所有可能的范围，身高依次进桶，正向倒出，就是最后的排序；

基数排序：重复利用桶的思想，比如三位整数排序，设置9个桶，1.按个位入桶，正向倒出；2.按十位入桶，正向倒出；3.按百位入桶，逆序倒出；

  


  


  


  


2\. coding：

  


2.1 归并排序

  


<div markdown="1" style="-en-codeblock: true; box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, "Courier New", monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.14902); background-position: initial initial; background-repeat: initial initial;">

/* memset example */

#include "stdafx.h"

#include <stdio.h>

#include <string.h>

  


int g_nCount = 0;

void mergearray(int a[], int first, int mid, int last, int temp[]) {

int i = first, j = mid + 1;

int m = mid, n = last;

int k = 0;

  


while (i <= m && j <= n) {

if (a[i] < a[j]) temp[k++] = a[i++];

else {

temp[k++] = a[j++];

g_nCount += m - i + 1; // Q1: g_nCount++?

}

}

  


while (i <= m) {

temp[k++] = a[i++];

}

while (j <= n) {

temp[k++] = a[j++];

}

for (int i = 0; i < k; i++) {// Q2: what to do

a[first + i] = temp[i];

}

}

  


void mergesort(int a[], int first, int last, int temp[]) {

if (first < last) {

int mid = first + (last - first) / 2;

mergesort(a, first, mid, temp);

mergesort(a, mid + 1, last, temp);

mergearray(a, first, mid, last, temp);

}

}

  


bool MergeSort(int a[], int n) {

int *p = new int[n];

if (p == nullptr) return false;

mergesort(a, 0, n - 1, p);

return true;

}

  


int main()

{

printf(" start");

  


const int MAXN = 8;

int a[MAXN] = { 1,7,2,9,6,4,5,3 };

  


g_nCount = 0;

MergeSort(a, MAXN);

printf("逆序数对为： %d\n", g_nCount);

return 0;

}

</div>

  


  


2.2 快速排序

  


版本一：

<div markdown="1" style="-en-codeblock: true; box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, "Courier New", monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.14902); background-position: initial initial; background-repeat: initial initial;">

#include<iostream>

using namespace std;

class QuickSort {

public:

int partition(int *A,int low,int high) {

int pivot = A[low];

while(low < high) {

while(low < high && A[high] >= pivot) high--;

swap(A[low], A[high]);

while(low<high && A[low]<=pivot) low++;

swap(A[low], A[high]);

}

return low;

}

  


void QSort(int *A,int start,int end) {

if(start<end) {

int index=partition(A,start,end);

QSort(A,start,index-1);

QSort(A,index+1,end);

}

}

  


int* quickSort(int* A, int n) {

QSort(A,0,n-1);

return A;

}

};

</div>

  


版本二（百度百科）（迭代法 ）：第一个为pivot，先从后找到第一个小于pivot的last，将last的值替换first的值，然后对first找第一个大于pivot的值，覆盖last的值，循环；遍历完数组后，将first填上pivot的值，完成partition。

<div markdown="1" style="-en-codeblock: true; box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, "Courier New", monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.14902); background-position: initial initial; background-repeat: initial initial;">

#include <iostream>

using namespace std;

  


void Qsort(int a[], int low, int high) {

if(low >= high) return;

int first = low;

int last = high;

int key = a[first]; /*用字表的第一个记录作为枢轴*/

  


while(first < last) {

while(first < last && a[last] >= key) --last;

a[first] = a[last];/*将比第一个小的移到低端*/

while(first < last && a[first] <= key) ++first;

a[last] = a[first];

}

a[first] = key;/*枢轴记录到位*/

Qsort(a, low, first-1);

Qsort(a, first+1, high);

}

  


int main() {

int a[] = {57, 68, 59, 52, 72, 28, 96, 33, 24};

Qsort(a, 0, sizeof(a) / sizeof(a[0]) - 1);/*这里原文第三个参数要减1否则内存越界*/

for(int i = 0; i < sizeof(a) / sizeof(a[0]); i++) cout << a[i] << "";

return 0;

} /*参考数据结构p274(清华大学出版社，严蔚敏)*/

</div>

  


版本三（wiki）（迭代法 ） ：最后一个为pivot，

<div markdown="1" style="-en-codeblock: true; box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, "Courier New", monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.14902); background-position: initial initial; background-repeat: initial initial;">

struct Range {  
int start, end;

Range(int s = 0, int e = 0) {start = s, end = e;}};template<typename T> //整數或浮點數皆可使用,若要使用物件(class)時必須設定"小於"(<)、"大於"(>)、"不小於"(>=)的運算子功能

void quick_sort(T arr[], const int len) {

if (len <= 0) return; //避免len等於負值時宣告堆疊陣列當機  
//r[]模擬堆疊,p為數量,r[p++]為push,r[--p]為pop且取得元素  
Range r[len]; int p = 0;  
r[p++] = Range(0, len - 1);  
while (p) {  
Range range = r[--p];  
if(range.start >= range.end) continue;  
T mid = arr[range.end];  
int left = range.start, right = range.end - 1;  
while (left < right) {  
while (arr[left] < mid && left < right) left++;  
while (arr[right] >= mid && left < right) right--;  
std::swap(arr[left], arr[right]);  
}  
if (arr[left] >= arr[range.end])  
std::swap(arr[left], arr[range.end]);  
else  
left++;  
r[p++] = Range(range.start, left - 1);  
r[p++] = Range(left + 1, range.end);  
}}

</div>

  


版本四（wiki）：递归法

<div markdown="1" style="-en-codeblock: true; box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, "Courier New", monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.14902); background-position: initial initial; background-repeat: initial initial;">

template<typename T>

void quick_sort_recursive(T arr[], int start, int end) {

if (start >= end) return;  
T mid = arr[end];  
int left = start, right = end - 1;

while (left < right) {

while (arr[left] < mid && left < right) left++;//左找到第一个>=pivot

while (arr[right] >= mid && left < right) right—;//找到第一个<pivot

std::swap(arr[left], arr[right]);  
}  
if (arr[left] >= arr[end])  
std::swap(arr[left], arr[end]);  
else  
left++;

quick_sort_recursive(arr, start, left - 1);

quick_sort_recursive(arr, left + 1, end);

}

  


void quick_sort(T arr[], int len) {

quick_sort_recursive(arr, 0, len - 1);

}

</div>

  


  


  


  


  


  


  


  


  


  


  


  


  


  


  

