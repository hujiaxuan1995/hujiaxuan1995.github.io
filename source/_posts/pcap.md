title: pcap数据包的解析还原
date: 2015-05-16 21:04:16
tags: tcp
categories: 计算机网络
description: 对pcap文件进行还原为应用层文件
keywords: pcap,wireshake,还原,五元组
---
最近在做pcap数据包还原的项目，这个项目主要目的是针对wireshake抓取的数据包进行解析与还原，最终还原为应用层文件。比如，wireshake将在某一时间段流经网卡的所有数据流抓取下来打包成pacp文件，而我们就可以还原出你访问过的网页，视频，音频等数据。这在信息安全领域是很有用的，比如进行流量监控等。
#关于wireshake抓包的介绍
wireshake主界面如下：
![wireshake](https://raw.githubusercontent.com/hujiaxuan1995/MyImage/master/wireshake.jpg)

可以很清晰得看到主界面包括三个部分。
第一部分是独立的pcap数据包，每一行的信息包括数据包到达的时间，源地址，目的地址，协议，长度和其他信息。
第二部分是每一个pcap包的协议和数据段。
第三部分是每一个pcap包的具体内容，wireshake自动去掉了pcap包头的24个字节。
基于http的pacp按顺序包括如下协议：
- 24字节的pcap头
        sturct pcap_file_header 
        {       DWORD           magic;    
                DWORD           version_major;  
                DWORD           version_minor;     
                DWORD           thiszone;    
                DWORD           sigfigs;       
                DWORD           snaplen;    
                DWORD         linktype; 
         }  
    说明：1、标识位：32位的，这个标识位的值是16进制的 0xa1b2c3d4。   2、主版本号：16位， 默认值为0x2。          3、副版本号：16位，默认值为0x04。        4、区域时间：32位，实际上该值并未使用，因此可以将该位设置为0。  5、精确时间戳：32位，实际上该值并未使用，因此可以将该值设置为0。 
    6、数据包最大长度：32位，该值设置所抓获的数据包的最大长度，如果所有数据包都要抓获，将该值设置为65535；例如：想获取数据包的前64字节，可将该值设置为64。7、链路层类型：32位， 数据包的链路层包头决定了链路层的类型。
- 16字节的数据报头
        struct __pkthdr  
            {  
                _Int32      iTimeSecond;   //分别4B
                _Int32      iTimeSS;  
                _Int32      iPLength;  //当前数据区的长度，由此可以得到下一个数据帧的位置
                _Int32      iLength;  
            };  
    每个pcap包包含一个数据报头。
- 14字节的以太网帧
            typedef struct FramHeader_t 
            { //Pcap捕获的数据帧头 
                u_int8 DstMAC[6]; //目的MAC地址 
                u_int8 SrcMAC[6]; //源MAC地址 
                u_short FrameType;    //帧类型 
            } FramHeader_t; 

- 20字节的IP头
            /*IP头定义，共20个字节*/
            typedef struct _IP_HEADER 
            {
             char m_cVersionAndHeaderLen;     　　//版本信息(前4位)，头长度(后4位)
             char m_cTypeOfService;      　　　　　 // 服务类型8位
             short m_sTotalLenOfPacket;    　　　　//数据包长度
             short m_sPacketID;      　　　　　　　 //数据包标识
             short m_sSliceinfo;      　　　　　　　  //分片使用
             char m_cTTL;        　　　　　　　　　　//存活时间
             char m_cTypeOfProtocol;    　　　　　 //协议类型
             short m_sCheckSum;      　　　　　　 //校验和
             unsigned int m_uiSourIp;     　　　　　//源ip
             unsigned int m_uiDestIp;     　　　　　//目的ip
            } __attribute__((packed))IP_HEADER, *PIP_HEADER ;
- 20字节的TCP头
                //定义TCP报头
            typedef struct _tcphdr
            {
             byte source_port[2]; //发送端端口号,16位
             byte dest_port[2];  //接收端端口号,16位
             byte sequence_no[4]; //32位，标示消息端的数据位于全体数据块的某一字节的数字
             byte ack_no[4];   //32位，确认号,标示接收端对于发送端接收到数据块数值
             byte offset_reser_con[2];//数据偏移4位，预留6位，控制位6为
             byte window[2];   //窗口16位
             byte checksum[2];  //校验码,16位
             byte urgen_pointer[2]; //16位，紧急数据指针
             byte options[3];  //选祥和填充,32位- 数据段

以上是pcap数据包中头部的几层协议，wireshake抓取的pcap文件中有很多个基本的pcap包
pcap包根据各自不同的源ip、目的ip、源端口、目的端口、协议类型加以区分，我们要做的第一项工作就是把属于同一五元组的pcap包筛选出来。筛选的时候并不区分目的ip和源ip。

#算法一：从pcap文件中析取所有TCP会话与UDP会话
* 输入：在网络节点处截获的互联网数据包文件(数据包以pcap模式存储)
* 输出：1. 输入的pcap文件中所包括的所有TCP会话与UDP会话, 每一TCP会话为一pcap文件，TCP会话文件名以TCP[ip1][port1][ip2][port2]_i.pcap格式命名，UDP会话文件名以UDP[ip1][port1] [ip2][port2].pcap格式命名，且作为32比特的整数[ip1]小于整数[ip2]；2. report1.txt输出报告,其列出形如TCP[ip1][port1][ip2][port2]_i.pcap与UDP[ip1][port1] [ip2][port2].pcap的记录。
* 算法：首先，建立一个二维链表，链表的每一行是一个五元组,每一行链表中的子节点是一个pcap包。然后遍历pcap数据包，对每一pcap数据报提取五元组，如果五元组存在，则插入进去，如果不存在则新建一个五元组链表。对pcap数据包遍历一遍之后，就得到了一个二维链表，表中的每一项就是一个pcap包。
     然后还需要对每一个五元组中的pcap包进行排序。排序的规则是根据每一个pcap数据报通过端口的时间进行排序，从而还原出一个完整的_会话_，而可以标识不同数据报到达先后顺序的字段是_序号seq_和_确认号ack_。
* 关键代码实现：
数据结构定义：
                    // C++test1.cpp : 定义控制台应用程序的入口点。
                //
                #include"stdafx.h"
                #include<stdio.h>
                #include<stdlib.h>
                #include <direct.h>
                #include"string.h"

                char * Ch_path(char ch[40]);
                struct Wuyuan * Creat(char name[40]);
                struct Wuyuan * ComparePcap(char name[40]);
                char * Itoa_name();
                char * Ch_path(char ch[40]);
                void AnalyPcap();
                void TCP(struct Wuyuan *p);
                void Butten1();
                char *Get_name(char p1[300]);
                void Butten2();
                void Butten3();
                void Butten4();

                typedef int _Int16;
                typedef unsigned long  _Int32;

                FILE * pfile;
                char ch_path[260] = "";
                long Filelen = 0, flen = 0, n_pcap = 0;

                struct TCPhead
                {
                    //数据包在总文件中位置
                    _Int32 f_size;
                    //抓包长度
                    _Int32 cap_len;
                    //tcp  ip头长度
                    _Int16 iphdr_len;
                    _Int16 tcphdr_len;
                    _Int16 s_port;
                    _Int16 d_port;
                    //tcp序号
                    _Int32 tcpseq;
                    //tcp确认号
                    _Int32 tcpack;
                    //控制字段CTL
                    _Int16 CTL;
                    struct TCPhead  *next;
                };
                struct Wuyuan
                {
                    //包个数
                    _Int32 num;
                    //协议
                    _Int16 protocol;
                    _Int32 caplen;
                    _Int16 s_port;
                    _Int16 d_port;
                    char wu_name[40];
                    char path[300];
                    struct TCPhead *TCPhead;
                    struct TCPhead *tcp_head;
                    struct Wuyuan *next;
                }*wu_head = NULL, *wu_p = NULL;

    button1(),实现算法一：

                    void Butten1()
                    {
                        FILE *fp, *fp1, *fp2;
                        _Int32 i = 0;
                        _Int16   q = 0;
                        struct TCPhead *tp;
                        char txt_path[300] = "0";
                        strcpy(txt_path, ch_path);
                        strcat(txt_path, "result1.txt");
                        fp1 = fopen(txt_path, "w");
                        wu_p = wu_head;
                        while (wu_p != 0)
                        {
                            if (wu_p->protocol == 17)
                            {
                                fprintf(fp1, "%s.pcap\r\n", wu_p->wu_name);
                                fp = fopen(wu_p->path, "wb");
                                fseek(pfile, flen, 0);
                                for (i = 0; i < wu_p->caplen + 16; i++)
                                {
                                    fread(&q, 1, 1, pfile);
                                    fwrite(&q, 1, 1, fp);
                                }
                                fclose(fp);
                            }
                            if (wu_p->protocol == 6)
                            {
                                fprintf(fp1, "%s_0.pcap\r\n", wu_p->wu_name);
                                fp = fopen(wu_p->path, "wb");
                                tp = wu_p->tcp_head;
                                while (tp != NULL)
                                {
                                    fseek(pfile, tp->f_size, 0);
                                    for (i = 0; i < tp->cap_len + 16; i++)
                                    {
                                        fread(&q, 1, 1, pfile);
                                        fwrite(&q, 1, 1, fp);
                                    }
                                    tp = tp->next;
                                }
                                fclose(fp);
                            }
                            wu_p = wu_p->next;
                        }
                        fclose(fp1);
                    }



#算法二：
    




注意:malloc创建一个结构体后要用memset进行初始化操作。否则就可能造成内存访问冲突的错误。
fopen创建的文件名不能包含某些特殊字符如？/等
