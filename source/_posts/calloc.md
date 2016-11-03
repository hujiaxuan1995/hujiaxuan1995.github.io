title: calloc  memset realloc memcpy 原理与实现
date: 2015-04-15 23:51:45
tags: C语言
categories: C语言
description: calloc malloc memset 
keywords: calloc malloc memset
---
#realloc
* 功能：动态内存分配
* 参数：extern void *realloc(void *mem_address, unsigned int newsize);
  mem_address:原内存地址。 newsize: 新的内存空间
* 返回值：如果重新分配成功则返回指向被分配内存的指针，否则返回空指针NULL。
* 原理：先判断当前的指针是否有足够的连续空间，如果有，扩大mem_address指向的地址，并且将mem_address返回，如果空间不够，先按照newsize指定的大小分配空间，将原有数据从头到尾拷贝到新分配的内存区域，而后释放原来mem_address所指内存区域（注意：原来指针是自动释放，不需要使用free），同时返回新分配的内存区域的首地址。即重新分配存储器块的地址。也就是说，如果空间足够，只需要在原先内存的基础上进行扩展，如果内存不足，则需要重新在堆上分配内存空间，将原来内存中的值复制过去。
* 简单例子：
                #include<stdio.h>
                #include<stdlib.h>
                int main()
                {
                    int i;
                    int *pn= (int*) malloc(5*sizeof(int));
                    printf("malloc%p\n",pn);
                    for(i=0;i<5;i++)
                        pn[i]=i;
                    pn = (int*)realloc(pn,10*sizeof(int));
                    printf("realloc%p\n",pn);
                    for(i=5;i<10;i++)
                        pn[i]=i;
                    for(i=0;i<10;i++)
                    printf("%3d",pn[i]);
                    free(pn);
                    return 0;
                }
* C语言重写：

#malloc
* C语言实现
                typedef long Align; /* for alignment to long boundary */
                union header { /* block header */
                struct {
                union header *ptr; /* next block if on free list */
                unsigned size; /* size of this block */
                } s;
                Align x; /* force alignment of blocks */
                };
                typedef union header Header;

                static Header base; /* empty list to get started */
                static Header *freep = NULL; /* start of free list */
                /* malloc: general-purpose storage allocator */
                void *malloc(unsigned nbytes)
                {
                   Header *p, *prevp;
                   Header *morecore(unsigned);
                   unsigned nunits;
                   nunits = (nbytes+sizeof(Header)-1)/sizeof(header) + 1;
                   if ((prevp = freep) == NULL) { /* no free list yet */
                      base.s.ptr = freeptr = prevptr = &base;
                      base.s.size = 0;
                   }
                   for (p = prevp->s.ptr; ; prevp = p, p = p->s.ptr) {
                      if (p->s.size >= nunits) { /* big enough */
                        if (p->s.size == nunits) /* exactly */
                           prevp->s.ptr = p->s.ptr;
                        else { /* allocate tail end */
                           p->s.size -= nunits;
                           p += p->s.size;
                           p->s.size = nunits
                             }
                        freep = prevp;
                        return (void *)(p+1);
                      }
                      if (p == freep) /* wrapped around free list */
                         if ((p = morecore(nunits)) == NULL)
                             return NULL; /* none left */
                      }
                }

#memcpy
* C语言实现：
                //C++ program to demonstrate implementation of memcpy()
                #include<stdio.h>
                #include<string.h>

                // A function to copy block of 'n' bytes from source
                // address 'src' to destination address 'dest'.
                void myMemCpy(void *dest, void *src, size_t n)
                {
                   // Typecast src and dest addresses to (char *)
                   char *csrc = (char *)src;
                   char *cdest = (char *)dest;

                   // Create a temporary array to hold data of src
                   char *temp = new char[n];

                   // Copy data from csrc[] to temp[]
                   for (int i=0; i<n; i++)
                       temp[i] = csrc[i];

                   // Copy data from temp[] to cdest[]
                   for (int i=0; i<n; i++)
                       cdest[i] = temp[i];
                }

                // Driver program
                int main()
                {
                   char csrc[] = "GeeksforGeeks";
                   char cdest[100];
                   myMemCpy(cdest, csrc, strlen(csrc)+1);
                   printf("Copied string is %s", cdest);

                   int isrc[] = {10, 20, 30, 40, 50};
                   int n = sizeof(isrc)/sizeof(isrc[0]);
                   int idest[n], i;
                   myMemCpy(idest, isrc,  sizeof(isrc));
                   printf("\nCopied array is ");
                   for (i=0; i<n; i++)
                     printf("%d ", idest[i]);
                   return 0;
                }

#calloc
* C语言实现：调用malloc。
