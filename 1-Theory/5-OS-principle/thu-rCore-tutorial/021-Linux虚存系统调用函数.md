---
url_mmap: https://zhuanlan.zhihu.com/p/477641987
---
## mmap ()

mmap 将一个文件或者其它对象映射进内存。文件被映射到多个页上，如果文件的大小不是所有页的大小之和，最后一个页不被使用的空间将会清零。mmap 在用户空间映射调用系统中作用很大。

头文件 `<sys/mman.h>`

### 函数原型
```
void* mmap (void* start, size_t length, int prot, int flags, int fd, off_t offset);

int munmap (void* start, size_t length);
```
mmap ()必须以 PAGE_SIZE 为单位进行映射，而内存也只能以页为单位进行映射，若要映射非 PAGE_SIZE 整数倍的地址范围，要先进行内存对齐，强行以 PAGE_SIZE 的倍数大小进行映射。  

用法:
---

下面说一下内存映射的步骤:  
用 open 系统调用打开文件, 并返回描述符 fd.  
用 mmap 建立内存映射, 并返回映射首地址指针 start.  
对映射 (文件) 进行各种操作, 显示 (printf), 修改 (sprintf).  
用 munmap (void *start, size_t lenght) 关闭内存映射.  
用 close 系统调用关闭文件 fd.  

主要用途
----

UNIX 网络编程第二卷进程间通信对 mmap 函数进行了说明。该函数主要用途有三个：  
1、将一个普通文件映射到内存中，通常在需要对文件进行频繁读写时使用，这样用内存读写取代 I/O 读写，以获得较高的性能；  
2、将特殊文件进行匿名内存映射，可以为关联进程提供共享内存空间；  
3、为无关联的进程提供共享内存空间，一般也是将一个普通文件映射到内存中。  
函数：void *mmap (void *start, size_t length, int prot, int flags, int fd, off_t offsize);  
参数 start：指向欲映射的内存起始地址，通常设为 NULL，代表让系统自动选定地址，映射成功后返回该地址。  
参数 length：代表将文件中多大的部分映射到内存。  
参数 prot：映射区域的保护方式。可以为以下几种方式的组合：  
PROT_EXEC 映射区域可被执行  
PROT_READ 映射区域可被读取  
PROT_WRITE 映射区域可被写入  
PROT_NONE 映射区域不能存取  
参数 flags：影响映射区域的各种特性。在调用 mmap () 时必须要指定 MAP_SHARED 或 MAP_PRIVATE。  
MAP_FIXED 如果参数 start 所指的地址无法成功建立映射时，则放弃映射，不对地址做修正。通常不鼓励用此旗标。  
MAP_SHARED 对映射区域的写入数据会复制回文件内，而且允许其他映射该文件的进程共享。  
MAP_PRIVATE 对映射区域的写入操作会产生一个映射文件的复制，即私人的 “写入时复制”（copy on write）对此区域作的任何修改都不会写回原来的文件内容。  
MAP_ANONYMOUS 建立匿名映射。此时会忽略参数 fd，不涉及文件，而且映射区域无法和其他进程共享。  
MAP_DENYWRITE 只允许对映射区域的写入操作，其他对文件直接写入的操作将会被拒绝。  
MAP_LOCKED 将映射区域锁定住，这表示该区域不会被置换（swap）。  
参数 fd：要映射到内存中的文件描述符。如果使用匿名内存映射时，即 flags 中设置了 MAP_ANONYMOUS，fd 设为 - 1。有些系统不支持匿名内存映射，则可以使用 fopen 打开 / dev/zero 文件，然后对该文件进行映射，可以同样达到匿名内存映射的效果。  
参数 offset：文件映射的偏移量，通常设置为 0，代表从文件最前方开始对应，offset 必须是分页大小的整数倍。  
返回值：  
若映射成功则返回映射区的内存起始地址，否则返回 MAP_FAILED (－1)，错误原因存于 errno 中。  
错误代码：  
EBADF 参数 fd 不是有效的文件描述词  
EACCES 存取权限有误。如果是 MAP_PRIVATE 情况下文件必须可读，使用 MAP_SHARED 则要有 PROT_WRITE 以及该文件要能写入。  
EINVAL 参数 start、length 或 offset 有一个不合法。  
EAGAIN 文件被锁住，或是有太多内存被锁住。  
ENOMEM 内存不足。  
系统调用 mmap () 用于共享内存的两种方式：  
（1）使用普通文件提供的内存映射：  
适用于任何进程之间。此时，需要打开或创建一个文件，然后再调用 mmap ()  
典型调用代码如下：  
fd=open (name, flag, mode); if (fd<0) ...  
ptr=mmap (NULL, len , PROT_READ|PROT_WRITE, MAP_SHARED , fd , 0);  
通过 mmap () 实现共享内存的通信方式有许多特点和要注意的地方，可以参看 UNIX 网络编程第二卷。  
（2）使用特殊文件提供匿名内存映射：  
适用于具有亲缘关系的进程之间。由于父子进程特殊的亲缘关系，在父进程中先调用 mmap ()，然后调用 fork ()。那么在调用 fork () 之后，子进程继承父进程匿名映射后的地址空间，同样也继承 mmap () 返回的地址，这样，父子进程就可以通过映射区 域进行通信了。注意，这里不是一般的继承关系。一般来说，子进程单独维护从父进程继承下来的一些变量。而 mmap () 返回的地址，却由父子进程共同维护。 对于具有亲缘关系的进程实现共享内存最好的方式应该是采用匿名内存映射的方式。此时，不必指定具体的文件，只要设置相应的标志即可。

  
一、概述
-------

内存映射，简而言之就是将用户空间的一段内存区域映射到内核空间，映射成功后，用户对这段内存区域的修改可以直接反映到内核空间，同样，内核空间对这段区域的修改也直接反映用户空间。那么对于内核空间 <----> 用户空间两者之间需要大量数据传输等操作的话效率是非常高的。  
以下是一个把普遍文件映射到用户空间的内存区域的示意图。  
图一：

![](https://pic2.zhimg.com/v2-3facbaa6a672f24f158606dc72101315_r.jpg)

  
二、基本函数
---------

  
mmap 函数是 unix/linux 下的系统调用，详细内容可参考《Unix Netword programming》卷二 12.2 节。  
mmap 系统调用并不是完全为了用于共享内存而设计的。它本身提供了不同于一般对普通文件的访问方式，进程可以像读写内存一样对普通文件的操作。而 Posix 或系统 V 的共享内存 IPC 则纯粹用于共享目的，当然 mmap () 实现共享内存也是其主要应用之一。  
mmap 系统调用使得进程之间通过映射同一个普通文件实现共享内存。普通文件被映射到进程地址空间后，进程可以像访问普通内存一样对文件进行访问，不必再调用 read ()，write（）等操作。mmap 并不分配空间, 只是将文件映射到调用进程的地址空间里（但是会占掉你的 virutal memory）, 然后你就可以用 memcpy 等操作写文件, 而不用 write () 了. 写完后，内存中的内容并不会立即更新到文件中，而是有一段时间的延迟，你可以调用 msync () 来显式同步一下, 这样你所写的内容就能立即保存到文件里了. 这点应该和驱动相关。 不过通过 mmap 来写文件这种方式没办法增加文件的长度, 因为要映射的长度在调用 mmap () 的时候就决定了. 如果想取消内存映射，可以调用 munmap () 来取消内存映射

```
void * mmap(void *start, size_t length, int prot , int flags, int fd, off_t offset)
```

mmap 用于把文件映射到内存空间中，简单说 mmap 就是把一个文件的内容在内存里面做一个映像。映射成功后，用户对这段内存区域的修改可以直接反映到内核空间，同样，内核空间对这段区域的修改也直接反映用户空间。那么对于内核空间 <----> 用户空间两者之间需要大量数据传输等操作的话效率是非常高的。  
start：要映射到的内存区域的起始地址，通常都是用 NULL（NULL 即为 0）。NULL 表示由内核来指定该内存地址

length：要映射的内存区域的大小  
prot：期望的内存保护标志，不能与文件的打开模式冲突。是以下的某个值，可以通过 or 运算合理地组合在一起  
PROT_EXEC // 页内容可以被执行  
PROT_READ // 页内容可以被读取  
PROT_WRITE // 页可以被写入  
PROT_NONE // 页不可访问  
flags：指定映射对象的类型，映射选项和映射页是否可以共享。它的值可以是一个或者多个以下位的组合体  
MAP_FIXED ：使用指定的映射起始地址，如果由 start 和 len 参数指定的内存区重叠于现存的映射空间，重叠部分将会被丢弃。如果指定的起始地址不可用，操作将会失败。并且起始地址必须落在页的边界上。  
MAP_SHARED ：对映射区域的写入数据会复制回文件内, 而且允许其他映射该文件的进程共享。  
MAP_PRIVATE ：建立一个写入时拷贝的私有映射。内存区域的写入不会影响到原文件。这个标志和以上标志是互斥的，只能使用其中一个。  
MAP_DENYWRITE ：这个标志被忽略。  
MAP_EXECUTABLE ：同上  
MAP_NORESERVE ：不要为这个映射保留交换空间。当交换空间被保留，对映射区修改的可能会得到保证。当交换空间不被保留，同时内存不足，对映射区的修改会引起段违例信号。  
MAP_LOCKED ：锁定映射区的页面，从而防止页面被交换出内存。  
MAP_GROWSDOWN ：用于堆栈，告诉内核 VM 系统，映射区可以向下扩展。  
MAP_ANONYMOUS ：匿名映射，映射区不与任何文件关联。  
MAP_ANON ：MAP_ANONYMOUS 的别称，不再被使用。  
MAP_FILE ：兼容标志，被忽略。  
MAP_32BIT ：将映射区放在进程地址空间的低 2GB，MAP_FIXED 指定时会被忽略。当前这个标志只在 x86-64 平台上得到支持。  
MAP_POPULATE ：为文件映射通过预读的方式准备好页表。随后对映射区的访问不会被页违例阻塞。  
MAP_NONBLOCK ：仅和 MAP_POPULATE 一起使用时才有意义。不执行预读，只为已存在于内存中的页面建立页表入口。

fd：文件描述符（由 open 函数返回）

offset：表示被映射对象（即文件）从那里开始对映，通常都是用 0。 该值应该为大小为 PAGE_SIZE 的整数倍

返回说明  
成功执行时，mmap () 返回被映射区的指针，munmap () 返回 0。失败时，mmap () 返回 MAP_FAILED[其值为 (void *)-1]，munmap 返回 - 1。errno 被设为以下的某个值  
EACCES：访问出错  
EAGAIN：文件已被锁定，或者太多的内存已被锁定  
EBADF：fd 不是有效的文件描述词  
EINVAL：一个或者多个参数无效  
ENFILE：已达到系统对打开文件的限制  
ENODEV：指定文件所在的文件系统不支持内存映射  
ENOMEM：内存不足，或者进程已超出最大内存映射数量  
EPERM：权能不足，操作不允许  
ETXTBSY：已写的方式打开文件，同时指定 MAP_DENYWRITE 标志  
SIGSEGV：试着向只读区写入  
SIGBUS：试着访问不属于进程的内存区

```
int munmap(void *start, size_t length)
```

start：要取消映射的内存区域的起始地址  
length：要取消映射的内存区域的大小。  
返回说明  
成功执行时 munmap () 返回 0。失败时 munmap 返回 - 1.  
int msync (const void *start, size_t length, int flags);

对映射内存的内容的更改并不会立即更新到文件中，而是有一段时间的延迟，你可以调用 msync () 来显式同步一下, 这样你内存的更新就能立即保存到文件里  
start：要进行同步的映射的内存区域的起始地址。  
length：要同步的内存区域的大小  
flag: flags 可以为以下三个值之一：  
MS_ASYNC : 请 Kernel 快将资料写入。  
MS_SYNC : 在 msync 结束返回前，将资料写入。  
MS_INVALIDATE : 让核心自行决定是否写入，仅在特殊状况下使用

三、用户空间和驱动程序的内存映射
----------------

### 3.1、基本过程

首先，驱动程序先分配好一段内存，接着用户进程通过库函数 mmap () 来告诉内核要将多大的内存映射到内核空间，内核经过一系列函数调用后调用对应的驱动程序的 file_operation 中指定的 mmap 函数，在该函数中调用 remap_pfn_range () 来建立映射关系。

### 3.2、映射的实现

首先在驱动程序分配一页大小的内存，然后用户进程通过 mmap () 将用户空间中大小也为一页的内存映射到内核空间这页内存上。映射完成后，驱动程序往这段内存写 10 个字节数据，用户进程将这些数据显示出来。

驱动程序：

```
#include <linux/miscdevice.h> 
#include <linux/delay.h> 
#include <linux/kernel.h> 
#include <linux/module.h> 
#include <linux/init.h> 
#include <linux/mm.h> 
#include <linux/fs.h> 
#include <linux/types.h> 
#include <linux/delay.h> 
#include <linux/moduleparam.h> 
#include <linux/slab.h> 
#include <linux/errno.h> 
#include <linux/ioctl.h> 
#include <linux/cdev.h> 
#include <linux/string.h> 
#include <linux/list.h> 
#include <linux/pci.h> 
#include <linux/gpio.h> 
 
 
#define DEVICE_NAME "mymap" 
 
 
static unsigned char array[10]={0,1,2,3,4,5,6,7,8,9}; 
static unsigned char *buffer; 
 
 
static int my_open(struct inode *inode, struct file *file) 
{ 
return 0; 
} 
 
 
static int my_map(struct file *filp, struct vm_area_struct *vma) 
{ 
unsigned long page; 
unsigned char i; 
unsigned long start = (unsigned long)vma->vm_start; 
//unsigned long end = (unsigned long)vma->vm_end; 
unsigned long size = (unsigned long)(vma->vm_end - vma->vm_start); 
 
//得到物理地址 
page = virt_to_phys(buffer); 
//将用户空间的一个vma虚拟内存区映射到以page开始的一段连续物理页面上 
if(remap_pfn_range(vma,start,page>>PAGE_SHIFT,size,PAGE_SHARED))//第三个参数是页帧号，由物理地址右移PAGE_SHIFT得到 
return -1; 
 
//往该内存写10字节数据 
for(i=0;i<10;i++) 
buffer[i] = array[i]; 
 
return 0; 
} 
 
 
static struct file_operations dev_fops = { 
.owner = THIS_MODULE, 
.open = my_open, 
.mmap = my_map, 
}; 
 
static struct miscdevice misc = { 
.minor = MISC_DYNAMIC_MINOR, 
.name = DEVICE_NAME, 
.fops = &dev_fops, 
}; 
 
 
static int __init dev_init(void) 
{ 
int ret; 
 
//注册混杂设备 
ret = misc_register(&misc); 
//内存分配 
buffer = (unsigned char *)kmalloc(PAGE_SIZE,GFP_KERNEL); 
//将该段内存设置为保留 
SetPageReserved(virt_to_page(buffer)); 
 
return ret; 
} 
 
 
static void __exit dev_exit(void) 
{ 
//注销设备 
misc_deregister(&misc); 
//清除保留 
ClearPageReserved(virt_to_page(buffer)); 
//释放内存 
kfree(buffer); 
} 
 
 
module_init(dev_init); 
module_exit(dev_exit); 
MODULE_LICENSE("GPL"); 
MODULE_AUTHOR("LKN@SCUT");
```

应用程序

```
#include <unistd.h> 
#include <stdio.h> 
#include <stdlib.h> 
#include <string.h> 
#include <fcntl.h> 
#include <linux/fb.h> 
#include <sys/mman.h> 
#include <sys/ioctl.h> 
 
#define PAGE_SIZE 4096 
 
 
int main(int argc , char *argv[]) 
{ 
int fd; 
int i; 
unsigned char *p_map; 
 
//打开设备 
fd = open("/dev/mymap",O_RDWR); 
if(fd < 0) 
{ 
printf("open fail\n"); 
exit(1); 
} 
 
//内存映射 
p_map = (unsigned char *)mmap(0, PAGE_SIZE, PROT_READ | PROT_WRITE, MAP_SHARED,fd, 0); 
if(p_map == MAP_FAILED) 
{ 
printf("mmap fail\n"); 
goto here; 
} 
 
//打印映射后的内存中的前10个字节内容 
for(i=0;i<10;i++) 
printf("%d\n",p_map[i]); 
 
 
here: 
munmap(p_map, PAGE_SIZE); 
return 0; 
}
```

先加载驱动后执行应用程序，用户空间打印如下：

![](https://pic1.zhimg.com/v2-af517862589a6989e733ac47a5ed30e4_r.jpg)
## sbrk ()

## shm ()