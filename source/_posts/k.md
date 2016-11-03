title: 【oj算法题】第K极值
date: 2015-05-15 22:26:12
tags: OJ 数据结构
categories: OJ
---
#描述
给定一个长度为N(0<n<=10000)的序列，保证每一个序列中的数字a[i]是小于maxlongint的非负整数 ，编程要求求出整个序列中第k大的数字减去第k小的数字的值m，并判断m是否为质数。(0<k<=n)
#输入格式
输入格式：
第一行为2个数n，k（含义如上题）
第二行为n个数，表示这个序列
#输出格式
输出格式：
如果m为质数则
第一行为'YES'(没有引号）
第二行为这个数m
否则 
第一行为'NO'
第二行为这个数m
#备注
备注
对于第K大的详细解释:
如果一个序列为1 2 2 2 2 3
第1大 为3
第2大 为2
第3大 为2
第4大 为2
第5大 为1
第K小与上例相反
另外需要注意的是
最小的质数是2,如果小于2的话,请直接输出NO
#思路
拿到这道题最直接的思路可能就是通过快速排序或者冒泡排序对输入的这串数进行排序，然后直接输出a[k-1]或a[n-k]。时间复杂度为O(n^2)。这种方法显然可以解决问题。但并不是我们所追求的。因为这道题只需要输出数据中的第K大的数和第K小的数。因此有一部分的比较是多余的。
经过测试时间和空间的开销为：1152(ms) 496(kib)

    #include <stdio.h>
    #include <stdlib.h>
    void exchange(int * a, int * b)
    {
        int temp = 0;
        temp = *a;
        *a = *b;
        *b = temp;
    }
    int bubble_sort(int a[], int n)
    {
        int i = 0, j = 0;
        for (i = 0; i<n; i++)
        {
            for (j = 0; j< n - i - 1; j++)
            {
                if (a[j]>a[j + 1])
                    exchange(&a[j],&a[j+1]);
            }
        }
    }
    int judge(int m)
    {
        int i;
        for (i = 2; i <= m; i++)
            if (m%i == 0)
                break;
        if (i == m)
            printf("YES\n%d", m);
        else
            printf("NO\n%d", m);

    }
    int main(void)
    {
        int n, i, k,m;
        long * data;
        long temp;
        temp = 0;
        m = 0;
        scanf("%d %d", &n, &k);
        data = (long *)calloc(n, sizeof(long));
        for (i = 0; i<n; i++)
        {
            scanf("%d", &temp);
            data[i] = temp;
        }
        bubble_sort(data, n);
        m = data[n-k]-data[k - 1];
        judge(m);
        return 0;
    }

#使用堆的数据结构
因为题目只要求找出第K大和第K小的数。所以我们完全可以采用堆这种数据结构。构造一个容量为K的最大堆和最小堆就可以得出两个值。时间复杂度为O(n*k)。
经过验证它的时间和空间的开销为：(561ms) (500kib)。可见，当数据量偏大的时候，这种方法还是非常节省时间的。

    #include <stdio.h>
    #include <stdlib.h>
    void exchange(int * a, int * b)
    {
        int temp = 0;
        temp = *a;
        *a = *b;
        *b = temp;
    }
    void small_heap_adjust(int data[],int index,int length)//构建最小堆
    {
        int child;
        int temp = data[index];
        if (2 * index +1>= length)
        {
            return;
        }
        child = 2 * index +1;
        if (child < length - 1 && data[child + 1] < data[child])
        {
            ++child;
        }
        if (temp>data[child])
        {
            data[index] = data[child];
        }
        else
            return;
        data[child] = temp;
        small_heap_adjust(data, child, length);
    }
    void big_heap_adjust(int data[], int index, int length)//构建最大堆
    {
        int child;
        int temp = data[index];
        if (2 * index + 1 >= length)
        {
            return;
        }
        child = 2 * index + 1;
        if (child < length - 1 && data[child + 1] > data[child])
        {
            ++child;
        }
        if (temp<data[child])
        {
            data[index] = data[child];
        }
        else
            return;
        data[child] = temp;
        big_heap_adjust(data, child, length);
    }
    int getBigkey(int data[], int n, int k)
    {
        int i = 0;
        for (i = k / 2 - 1; i >= 0; i--)
        {
            small_heap_adjust(data, i, k);
        }
        for (i = k; i < n; i++)
        {
            if (data[i]>data[0])
            {
                exchange(&data[i], &data[0]);
                small_heap_adjust(data, 0, k);
            }
        }
        return data[0];
    }
    int getSmallkey(int data[], int n, int k)
    {
        int i = 0;
        for (i = k / 2 - 1; i >= 0; i--)
        {
            big_heap_adjust(data, i, k);
        }
        for (i = k; i < n; i++)
        {
            if (data[i]<data[0])
            {
                exchange(&data[i], &data[0]);
                big_heap_adjust(data, 0, k);
            }
        }
        return data[0];
    }
    int judge(int m)
    {
        int i;
        for (i = 2; i <= m; i++)
        if (m%i == 0)
            break;
        if (i == m)
            printf("YES\n%d", m);
        else
            printf("NO\n%d", m);
    }
    int main(void)
    {
        int n, i, k, m;
        long * data;
        long temp;
        temp = 0;
        m = 0;
        scanf("%d %d", &n, &k);
        data = (long *)calloc(n, sizeof(long));
        for (i = 0; i<n; i++)
        {
            scanf("%d", &temp);
            data[i] = temp;
        }
        m = getBigkey(data,n,k) - getSmallkey(data, n, k);
        judge(m);
        return 0;
    }
