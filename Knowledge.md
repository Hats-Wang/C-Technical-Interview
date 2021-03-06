# C++ Technical Interview

[TOC]

## 1. C++

### (1) 指针 && 引用

- 指针是变量，是变量的地址，有4Byte或8Byte，但是引用是别名，不占内存空间
- 指针可以为空，可以先声明后初始化，并且初始化之后可以改变；引用必须在声明时初始化且之后不可更改
- 指针可以有多级，引用不可以

### (2)多态 && 实现方式

- 多态是用虚函数和重载实现的，而虚函数主要通过虚函数表（V-Table）实现的
- 虚函数是动态联编，重载是静态的
- 若类中有virtual修饰的成员函数，则该类有一个虚函数表
- 该类定义的对象会有一个虚函数指针指向类的虚函数表，原始基类的对象的虚函数指针指向基类的虚函数表
- 有纯虚函数的类为抽象类，不能被实例化。纯虚函数在基类中没有定义，要求所有派生类必须定义，且必须重新声明。

### (3)虚函数 && 底层实现细节

- 虚函数表（基类和派生类都有）
- 多重继承->多张虚函数表
- 空类大小为1byte，因为需要给这个类分配确定的内存地址，防止两个实例是一个地址
- 如果空类+一个虚函数，则大小为四字节，因为有一个指向虚函数表的指针

### (4)动态绑定

- 静态绑定是绑定对象的静态类型（编译期间），no-virtual函数会在静态时绑定
- 动态绑定是动态类型，利用虚函数的虚函数表来进行 运行时绑定管理

### (5)内存管理、智能指针

- auto_ptr (c++11废弃)，保证指针变量离开作用域时会进行析构

  c++11用unique_str替代了

  ```c++
  auto_ptr<int> p(new int(12));
  ```

- auto_ptr是独占的，避免使用同一个对象初始化两个智能指针

- shared_ptr，计数型智能指针

  可能存在循环引用的问题：两个对象互相使用一个shared_ptr成员变量指向对方。

    解决循环引用方法：

     1. 当只剩下最后一个引用的时候需要手动打破循环引用释放对象。
     2. 当A的生存期超过B的生存期的时候，B改为使用一个普通指针指向A。
     3. 使用weak_ptr打破这种循环引用，因为weak_ptr不会修改计数器的大小，所以就不会产生两个对象互相使用一个shared_ptr成员变量指向对方的问题，从而不会引起引用循环。

- 智能指针的实现方法 

### (6) STL

- STL中map和unordered_map的应用场景区别 

- STL包括容器、迭代器、算法（查找、排序）、函数对象、适配器、空间配置器

- 各种容器：

  * vector：可变大小数组，支持快速随机访问，在尾部之外的位置插入或者删除元素可能很慢

    *对vector的任何操作一旦引起了空间的重新配置，指向原vector的所有迭代器就会都失效*

    size() : 当前有多少元素; capacity() : 可容纳多少元素；

    *vector元素不能是引用，因为vector元素需要有实际地址*

  * deque：双端队列，支持快速随机访问，在头尾插入删除速度很快

    底层是双向开口的连续线性空间

  * list：双向链表，只支持双向顺序访问，在任何位置插入删除速度很快

    底层是双向链表，push_back(), pop_back(), push_front(), pop_front()

  * array：固定大小数组，不能添加删除元素

  * string：专门存储字符，与vector类似

  * priority_queue：优先队列，*底层用堆实现的*

    ```c++
    priority_queue<int, vector<int>, greater<int>> pq; //最小堆
    priority_queue<int, vector<int>, less<int>> pq; //最大堆
    pq.top();//优先级最高（堆顶）
    pq.pop();//删除最高
    ```

  * map，set，multiset，multimap

    底层都是红黑树

    map中mp.count(key) > 0和mp.find(key) != mp.end()都可以表示key存在

    set,multiset会自动排序，set不允许元素重复，multiset可以重复

    map, multimap将key, value组成的pair作为元素，根据key排序

  * unordered_map, unordered_set

    底层是防冗余的哈希表（除留余数法）

    常数级别查找，但是耗内存



### (7) new/delete & malloc/free

##### new/malloc区别

- new从自由存储区分配内存，malloc从堆上分配内存

- new/delete会调用构造析构函数对对象进行初始化与销毁

- operator new/delete可以进行重载

- new返回类型是对象类型的指针，但是malloc返回的是void*需要强制类型转换

- 分配失败时，new抛出异常，malloc返回NULL

- new无需指定内存块大小，malloc需要指定

##### malloc和alloc的区别 

### (8) extern "C"

告诉c++编译器，下面的代码按照c方式进行编译，即不需要对函数进行名字重整

### (9) 继承

##### *a; 单继承*

- class B : public A ：Bpublic方式继承A
- 类中的static静态成员变量，单独存储在程序的.data段
- 若派生类成员变量与基类成员变量同名，则基类隐藏

##### *b: 多继承*

- 按照声明顺序成员变量由低地址向高地址排列，先基类排列，后派生类

##### *c: 虚继承*

- class B : public <font color="red">virtual A</font>

![虚继承](D:\Intern\Tencent\虚继承.png)



- 继承类调用构造函数顺序和析构函数顺序

  构造函数：先父后子

  虚析构函数：先子后父

  非虚析构函数：实例化的对象执行一次

### (10) struct & class

- struct默认继承访问权限为public，默认访问权限为public，初始化方式（能否使用大括号）
- 结构体内存对齐方式 ： x86 gcc默认4字节对齐

### (11) C++11特性 最新到c++17了 

- Lambda表达式，多用于STL

- 智能指针

- 新的多线程库，\<thread> \<mutex> 

- auto关键字

  类似于c#里面的var，正确用法：

  - 用于代替冗长复杂变量使用范围专一的变量声明

    ```c++
    std::vector<std::string>::iterator i = vs.begin(); =>
    auto i = vs.begin();
    ```

    

  - 定义模板函数时，用于声明依赖模板参数的变量类型

    ```c++
    template <typename _Tx,typename _Ty>
    void Multiply(_Tx x, _Ty y)
    {
        auto v = x*y;
        std::cout << v;
    }
    ```

    

  - 注意：auto 变量必须在定义时初始化，这类似于const关键字 

    定义在一个auto序列的变量必须始终推导成同一类型。

    如果初始化表达式是引用，则去除引用语义。 

    如果初始化表达式为const或volatile（或者两者兼有），则除去const/volatile语义。 

    函数或者模板参数不能被声明为auto 

- 函数模板

  ```c++
  template <typename T>
  void swap(T &a, T &n);
  ```

  

### (12) 介绍迭代器失效。push_back会导致迭代器失效吗 

### (13) 函数指针和指针函数的区别 

- 指针函数：带指针的函数，返回值类型为指针

  ```c++
  int *func(int x);
  ```

  

- 函数指针：指向函数的指针变量

  ```c++
  int (*func)(int x);
  int k = (*func)(x);
  int k = func(x);//两种方式都可以，推荐第一种
  ```

  

### (14) include 包含文件的时候，尖括号和双引号有什么区别 

##### 如果双引号中是库文件的话，会发生什么（答对一半，面试官解答，双引号是优先在工程文件makefile标识的文件夹中寻找，找不到再去库文件夹中找） 

- 系统自带的头文件用尖括号，会在系统文件目录下查找
- 用户自定义文件用双引号，编译器首先在用户目录下查找，然后到c++安装目录中查找，最后在系统文件查找

### (15) 一个程序从开始运行到结束的完整过程（四个过程）

1、预处理：条件编译，头文件包含，宏替换的处理，生成.i文件。

2、编译：将预处理后的文件转换成汇编语言，生成.s文件

3、汇编：汇编变为目标代码(机器代码)生成.o的文件

4、链接：连接目标代码,生成可执行程序

### (16) const与#define的区别

##### 1.编译器处理方式 

define – 在预处理阶段进行替换 
const – 在编译时确定其值

##### 2.类型检查 

define – 无类型，不进行类型安全检查，可能会产生意想不到的错误 
const – 有数据类型，编译时会进行类型检查

##### 3.内存空间 

define – 不分配内存，给出的是立即数，有多少次使用就进行多少次替换，在内存中会有多个拷贝，消耗内存大 
const – 在静态存储区中分配空间，在程序运行过程中内存中只有一个拷贝

##### 4.其他 

在编译时， 编译器通常不为const常量分配存储空间，而是将它们保存在符号表中，这使得它成为一个编译期间的常量，没有了存储与读内存的操作，使得它的效率也很高。 
宏替换只作替换，不做计算，不做表达式求解。

### (17) 空悬指针&野指针

空悬指针：指针指向的区域被释放之后，指针没有指向NULL

野指针：没有初始化的指针

### (18) volatile关键字

volatile表明这个变量是随时可能变化的，在这里的编译优化就不用做了，每次用到它的时候，都要从内存中重新读取。



## 2. Operator System

### (1) 进程线程协程管程 (fork, 线程模型)

- 有线程系统：进程是资源分配的独立单位，线程是资源调度的独立单位
- 无线程系统：进程是资源分配、调度的独立单位
- 进程是抢占式争夺CPU
- 线程属于进程，共享进程的内存地址空间
- 进程切换需要：1. 切换页目录以使用新的地址空间；2. 切换内核栈；3. 切换硬件上下文
- 线程只需要2.3
- 协程属于线程，协程在线程里面运行，协程基本没有内核切换的开销，协程的调度是用户手动切换的，因此更加灵活；原子操作性，因为是用户调度的，所以无需原子操作锁。
- fork：复制父进程所有的资源，有自己的task_struct和pid，打开的文件以及读写指针都在相同的地方
- vfork：并不将父进程的地址空间完全复制，因为子进程会立即调用exec或exit，父子共享地址空间，父进程会被阻塞直到子进程调用exec或exit，因为有的时候fork只是为了exec执行另一个程序，这时候对于父进程的复制就是浪费的（但是在fork有了写时复制之后意义不打了）
- exit()函数与_exit()函数最大的区别就在于，exit()函数在调用exit系统之前要检查文件的打开情况，把文件缓冲区的内容写回文件。 
- clone有参数
- fork返回后，父子进程都从调用fork函数的下一条语句开始。

### (2) 进程间通信

- 管道

  - 有名管道：允许无亲缘关系进程间通信

  - 无名管道：父子进程中使用，只能单向通信，依赖文件系统

    pipe函数接受一个参数，是包含两个整数的数组，如果调用成功，会通过pipefd[2]传出给用户程序两个文件描述符，需要注意pipefd [0]指向管道的读端, pipefd [1]指向管道的写端，那么此时这个管道对于用户程序就是一个文件，可以通过read(pipefd [0])；或者write(pipefd [1])进行操作。pipe函数调用成功返回0，否则返回-1.. 

- 信号量

  一个计数器，用来控制多个进程对共享资源的访问

  可以同步进程

- 信号：比较复杂的通信方式，通知接收进程某个事件已经发生

- 套接字（Socket）：不同计算机间进程通信

- 消息队列：在内核中，可以实现任意进程间通信，通过系统调用函数实现

- 共享内存

  共享内存的原理：共享内存是最有用的进程间通信方式之一，也是最快的IPC形式。两个不同进程A、B共享内存的意思是，同一块物理内存被映射到进程A、B各自的进程地址空间。进程A可以即时看到进程B对共享内存中数据的更新，反之亦然。 

  共享内存映射到进程的内存映射区

  

##### linux的进程间通信和windows有何不同？是否了解？ 

##### 进程间通信的方法，还有它和socket通信的相同和不同点在哪儿，使用时机是何时？ 

### (3) 进程同步与互斥

- 原子操作（不会被打断）

### (4) 死锁

- 系统资源不足，资源分配不当，进程运行推进顺序不合适
- 产生死锁的必要条件：
  - **互斥条件(Mutual exclusion)**：资源不能被共享，只能由一个进程使用。
  - **请求与保持条件(Hold and wait)**：已经得到资源的进程可以再次申请新的资源。
  - **非抢占条件(No pre-emption)**：已经分配的资源不能从相应的进程中被强制地剥夺。
  - **循环等待条件(Circular wait)**：系统中若干进程组成环路，该环路中每个进程都在等待相邻进程正占用的资源。

### (5) 银行家算法

- 可利用资源向量，最大需求矩阵，分配矩阵，需求矩阵
- 每次试探着允许分配，然后进行安全性检查

### (6) 几种常见调度算法

##### 先来先服务

##### 短作业优先

##### 时间片轮转

##### 优先级

（linux进程调度方法：普通进程基于动态优先级，实时进程：先进先出or基于时间片FIFO）

### (7) 原子操作

### (8) 内核态、用户态，如何进入内核态

- 系统调用，比如printf, open, read, write
- 发生中断，然后用TSS（任务状态段）中的堆栈段ss-和栈指针esp0装载ss和esp，则由用户栈切换到内核栈，同时内核以pusha指令保存所有寄存器值
- 中断结束，执行iret命令，读回所有寄存器值

### (9) 自由存储区和堆的区别

### (10) CPU的执行方式：指令执行？

### (11) read系统调用过程

- 当调用发生时，库函数在保存 read 系统调用号以及参数后，陷入 0x80 中断。这时库函数工作结束。Read 系统调用在用户空间中的处理也就完成了。 
- 0x80 中断处理程序接管执行后，先检察其系统调用号，然后根据系统调用号查找系统调用表，并从系统调用表中得到处理 read 系统调用的内核函数 sys_read ，最后传递参数并运行 sys_read 函数。 
- 读数据之前，必须先打开文件。处理 open 系统调用的内核函数为 sys_open
  - get_unuesed_fd() ：取回一个未被使用的文件描述符（每次都会选取最小的未被使用的文件描述符）。
  - filp_open() ：调用 open_namei() 函数取出和该文件相关的 dentry 和 inode （因为前提指明了文件已经存在，所以 dentry 和 inode 能够查找到，不用创建），然后调用 dentry_open() 函数创建新的 file 对象，并用 dentry 和 inode 中的信息初始化 file 对象（文件当前的读写位置在 file 对象中保存）。
  - fd_install() ：以文件描述符为索引，关联当前进程描述符和上述的 file 对象，为之后的 read 和 write 等操作作准备。
  - 函数最后返回该文件描述符。
- 根据 fd 指定的索引，从当前进程描述符中取出相应的 file 对象。
  如果没找到指定的 file 对象，则返回错误
  如果找到了指定的 file 对象：
  - 调用 file_pos_read() 函数取出此次读写文件的当前位置。
  - 调用 vfs_read() 执行文件读取操作，而这个函数最终调用 file->f_op.read() 指向的函数，代码如下：
  - 调用 file_pos_write() 更新文件的当前读写位置。
  - 调用 fput_light() 更新文件的引用计数。
  - 最后返回读取数据的字节数。
  - 到此，虚拟文件系统层所做的处理就完成了，控制权交给了 ext2 文件系统层。
- 进入到文件系统层处理
- 进入IO调度层处理请求，进行IO的请求队列，何时处理就要IO调度自己决定了
- 完成IO请求之后，通过中断方式通知CPU，如果成功，上层函数解锁IO操作所涉及的页面。之后函数不断返回。

### (12) 虚拟文件层作用

虚拟文件系统层的作用：屏蔽下层具体文件系统操作的差异，为上层的操作提供一个统一的接口。正是因为有了这个层次，所以可以把设备抽象成文件，使得操作设备就像操作文件一样简单。

### (13) 内存池

- 目的：尽量减少内存碎片，平均效率高于malloc和free
- 向系统中请求一大片连续的内存空间，根据实际需要分配出去

 ### (14) 计算机系统基础

- 分页
- 分段
- 虚拟内存
- 地址转换
- 页面调度



## 3. Data Structure

- 顺序结构：栈，队列，非循环队列，循环队列
- 链式结构：链表（单链表，双向链表，循环链表）
- 哈希表
- 二叉树 ：堆，二叉搜索树，平衡二叉树（AVL数）
- 并查集
- 红黑树
- B树，B+树（文件系统，数据库系统）

### (1) 链表

### (2) 树

- 二叉树（高度）

### (3) 排序 (稳定性排序 )

- 冒泡排序

- 插入排序

- 快速排序（pivot)

  ```c++
  int Partition(vector<int>& nums, int begin, int end) {
  	int key = nums[end];//取最后一个
  	int i = begin - 1;
  	for (int j = begin; j < end; j++)
  	{
  		if (nums[j] <= key)
  		{
  			i++;
  			//i一直代表小于key元素的最后一个索引，当发现有比key小的a[j]时候，i+1 后交换     
  			swap(nums[i], nums[j]);
  		}
  	}
  	swap(nums[i + 1], nums[end]);//将key切换到中间来，左边是小于key的，右边是大于key的值。
  	return i + 1;
  }
  ```

- 归并排序

- 堆排序

- 桶排序

- 基数排序

### (4) N个数求前K大

- 快排pivot
- 计数排序，开辟一个大叔组，记录每个整数出现的次数，从大到小取（bitmap）
- 维护一个大小为k的大根堆

### (5) 求文件A中没有但B中有的单词（字典树）

- 遍历文件A，将文件hash到n个小文件中
- 对B文件同样操作
- 然后对于每一对文件，进行hash操作

### (6) 并查集

### (7) map, hashmap, set

### (8) Dijkstra && Floyd

### (9) 红黑树的特征，介绍 

（1）每个节点或者是黑色，或者是红色。
（2）根节点是黑色。
（3）每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]
（4）如果一个节点是红色的，则它的子节点必须是黑色的。
（5）从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。[这里指到叶子节点的路径]



## 4. 数据库

几种引擎以及各自的特点：

- ISAM ： 读取操作快，不能够容错，不支持事务处理
- MYISAM ： 除了**提供**ISAM里所没有的**索引和字段管理的**功能，MYISAM还使用一种**表格锁定的机制**，来**优化多个并发的读写操作**。 
- HEAD ：HEAP**允许只驻留在内存里的临时表格**。驻留在内存里让HEAP要比ISAM和MYISAM都快，但是它所**管理的数据是不稳定的**，而且如果在关机之前没有进行保存，那么所有的数据都会丢失。 
- INNODB ： INNODB和BDB包括了**对事务处理和外来键的支持** 

### (1) 为什么用B+树作为索引的数据结构

#### 事务管理（ACID）

谈到事务一般都是以下四点

#### 1. 原子性（Atomicity）

原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。

#### 2. 一致性（Consistency）

事务前后数据的完整性必须保持一致。

#### 3. 隔离性（Isolation）

事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。

#### 4. 持久性（Durability）

持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响

### (2) Redis内存数据库的内存指的是共享内存么

NOSQL数据库，可使用各种模型进行数据管理，针对大数据量、低延迟和灵活数据模型的应用程序进行了优化

非常适合大数据、移动和web应用程序

**Redis**是一款基于内存的且支持持久化、高性能的Key-Value NoSQL 数据库，其支持丰富数据类型(string，list，set，sorted set，hash)，常被用作缓存的解决方案。Redis具有以下显著特点：

- 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)；
- 支持丰富数据类型，支持string，list，set，sorted set，hash；
- 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行；
- 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除。

### (3) Redis的持久化方式

因为Redis在内存中，需要配置持久化

- RDB持久化：定时dump到磁盘上
- AOF持久化：将操作日志追加方式写入

### (4) Redis和MySQL有什么区别，用于什么场景。 

- mysql是关系型数据库，主要用于存放持久化数据，将数据存储在硬盘中，读取速度较慢。

  redis是NOSQL，即非关系型数据库，也是缓存数据库，即将数据存储在缓存中，缓存的读取速度快，能够大大的提高运行效率，但是保存时间有限

- Redis
  基于内存，读写速度快，也可做持久化，但是内存空间有限，当数据量超过内存空间时，需扩充内存，而内存成本较高；

  MySQL
  基于磁盘，读写速度没有Redis快，但是不受空间容量限制，性价比高；

  应用场景
  多数时候是MySQL（主）+Redis（辅），MySQL做为主存储，Redis用于缓存，加快访问速度。需要高性能的地方使用Redis，不需要高性能的地方使用MySQL。

  个人总结：一个优秀的网站不同模块一定是有对性能不同的要求的，性能要求高的模块我们使用redis(拿空间换速度)，其他模块使用性价比更高的mysql储存。

### (5) 基本概念

- 实体(entity)：客观存在并可相互区别的事物
- 属性：实体所具有的某一特性
- 码：唯一标识实体的属性集

### (6) 常用数据模型

- 层次模型
- 网状模型
- 关系模型
  - 关系：一个关系对应通常说的一张表
  - 元组：表中一行即为一个元祖
  - 属性：表中一列即为一个属性
- 面向对象的数据模型
- 对象关系数据模型
- 半结构化数据模型

### (7) 索引

- 顺序索引

- B-树：对于二元组[key, data]，key为键值

  d为大于1的一个正整数，称为B-Tree的度。

  h为一个正整数，称为B-Tree的高度。

  每个非叶子节点由n-1个key和n个指针组成，其中d<=n<=2d。

  每个叶子节点最少包含一个key和两个指针，最多包含2d-1个key和2d个指针，叶节点的指针均为null 。

  所有叶节点具有相同的深度，等于树高h。

  key和指针互相间隔，节点两端是指针。

  一个节点中的key从左到右非递减排列。

  所有节点组成树结构。

  每个指针要么为null，要么指向另外一个节点。

  如果某个指针在节点node最左边且不为null，则其指向节点的所有key小于v(key1)v(key1)，其中v(key1)v(key1)为node的第一个key的值。

  如果某个指针在节点node最右边且不为null，则其指向节点的所有key大于v(keym)v(keym)，其中v(keym)v(keym)为node的最后一个key的值。

  如果某个指针在节点node的左右相邻key分别是keyikeyi和keyi+1keyi+1且不为null，则其指向节点的所有key小于v(keyi+1)v(keyi+1)且大于v(keyi)v(keyi)。

  图2是一个d=2的B-Tree示意图。

- B+树

  - 每个节点的指针上限为2d而不是2d+1
  - 内节点不存储data，只存储key，叶子节点不存储指针

  **数据库索引采用B+树而不是B树的主要原因：**B+树只要遍历叶子节点就可以实现整棵树的遍历，而且在数据库中基于范围的查询是非常频繁的，而B树只能中序遍历所有节点，效率太低。 

- hash索引

### (8) 范式

- 第一范式（1NF）：属性（字段）是最小单位不可再分。（列不可分）
- 第二范式（2NF）：满足 1NF，每个非主属性完全依赖于主键（候选码）（消除 1NF 非主属性对码的部分函数依赖）。
- 第三范式（3NF）：满足 2NF，任何非主属性不依赖于其他非主属性（消除 2NF 非主属性对码的传递函数依赖）。
- 鲍依斯-科得范式（BCNF）：满足 3NF，任何非主属性不能对主键子集依赖（消除 3NF 主属性对码的部分和传递函数依赖）。
- 第四范式（4NF）：满足 3NF，属性之间不能有非平凡且非函数依赖的多值依赖（消除 3NF 非平凡且非函数依赖的多值依赖）。

### (9) 数据库恢复

- 事务：用户定义的一个数据库操作序列
- 事务特性：原子性、一致性、隔离性、持续性



## 5. Linux

### (1) Linux了解么， 

- 查看进程状态ps 
- 查看cpu状态 top 
- 查看占用端口的进程号lsof -i:端口号, netstat -tunlp用于显示tcp udp的端口进程相关情况，netstat -tunlp | grep 端口号 
- free查看内存状态

### (2) 代码中遇到进程阻塞，进程僵死，内存泄漏等情况怎么排查。通过ps查询状态，分析dump文件等方式排查。

场景：xx.perl读取文件并发送邮件

现象：执行脚本的进程僵死(卡住)

排查：ps -ef |grep “perl xx.perl” 

跟踪：strace -p 16634  (跟踪进程执行时的系统调用和所接收的信号（即它跟踪到一个进程产生的系统调用，包括参数、返回值、执行消耗的时间)，卡在read(3,位置 

查看进程文件描述符目录：查看3进行的是socket操作，也就是卡在通信。 （ll命令 )

netstat -anoutp |grep 24432 查看通信的目标是什么：端口ip：25，可以确定是邮件服务器 

可以使用pstack查询程序的栈跟踪

[root@Joe ~]# pstack 11694

### (3) cat | grep 管道处理内存不足问题

- |：管道，command1 | command2, 将command1的输出给command2

### (4)  Linux大文件怎么查某一行的内容 && 搜索。 

cat info.log | grep ‘1711178968'   >> temp.log 

### (5) Linux的cpu 100%怎么排查，top，jstack，日志，gui工具 

- top -c（找到进程）
- top -Hp xxxxx（找到线程）
- jstack 10765 | grep xxxx(在堆栈里线程是16进制的)

### (6) gdb条件断点 

- break main if xxx
- break 180 if xxx
- condition 3 i == 3（加在已存在的断点上）
- watch

### (7) Top

Top命令查看linux当前所有进程的状态，可以粗略理解为windows里的任务管理器，可以看到cpu占用量，内存占用量等，（默认按照cpu占用排序）

如果想要查看线程，可以使用top -H命令

### (8) 一个32位机器上linux进程最大可以申请多少空间？ 

### (9) RTOS与linux的区别 

### (10) daemon mode（守护进程）

是linux**后台执行**的一种服务进程，特点是**独立于控制终端、周期性地执行某种任务或等待处理某些发生事件，**不会随终端关闭而停止，直到接受停止信息才会结束，且一般采用以d结尾的名字。 

##### 实现守护进程的编程流程：
  1.  先fork(),然后父进程退出，这样做实现了以下几点：第一，如果该守护进程是作为一个简单的shell命令启动的，那么终端关闭，则该进程就会结束，这样不符合守护进程能在后台不断运行的特点；第二，子进程继承了父进程的进程组id，但具有了一个新的进程id, 这保证了子进程不是一个进程组的组长进程，这个是下面调用setsid()的必要前提条件。

  2.  setsid()创建新会话，使调用进程（a）成为新会话的首进程, (b)成为一个新进程组的组长进程，(c)没有控制终端

  3.  fork(),然后退出父进程，这一步是保证fork()得到的子进程失去 会话首进程 和 组长进程 的身份，但是属于可做可不做的操作

  4.  chdir("/"),改变工作目录为根目录，因为从父进程继承过来的工作目录可能在一个装配文件系统下，因为守护进程通常在系统的再引导下是一直存在的，所以如果守护进程的当前工作在一个装配文件系统中，那么该文件系统就不能被拆卸，这与装配文件系统的意愿不符合。

  5.  unask(0), 将文件模式创建屏蔽字设置为0，由继承得来的文件模式创建屏蔽字可能会拒绝设置某些权限。例如，若守护进程要创建一个组可读可写的文件，而继承来的文件模式创建屏蔽字可能屏蔽了这两种权限，于是守护进程所要求的组可读可写就不起作用

  6.  close(),关闭文件描述符，这使守护进程不在持有从父进程继承来的文件描述符。可以通过getdtablesize()函数来获得全部描述符的个数。

### (11) 僵死进程

如果守护进程fork出自己的子进程，则有可能出现僵死进程。

僵死进程的的特点是：先于父进程结束，而父进程没有wait()子进程的退出码。 因为每个子进程退出之前会留下一些数据结构，如果父进程没有处理的话，这些空间就浪费了

1. 僵尸进程的PID还占据着，意味着海量的子进程会占据满进程表项，会使后来的进程无法fork.
2. 僵尸进程的内核栈无法被释放掉，为啥会留着它的内核栈，因为在栈的最低端，有着thread_info结构，它包含着 struct_task 结构，这里面包含着一些退出信息

##### 解决办法：

1. wait和waitpid函数：父进程调用wait/waitpid等函数等待子进程结束，如果尚无子进程退出wait会导致父进程阻塞。waitpid可以通过传递WNOHANG使父进程不阻塞立即返回。
2. sigaction信号处理函数（交给内核处理）:如果父进程很忙可以用sigaction注册信号处理函数，在信号处理函数调用wait/waitpid等待子进程退出。（sigaction函数类似于signal函数，而且完全可以代替后者，也更稳定）
3. signal忽略SIGCHLD信号（交给内核处理） :通过signal(SIGCHLD, SIG_IGN)通知内核对子进程的结束不关心，由内核回收。如果不想让父进程挂起，可以在父进程中加入一条语句：signal(SIGCHLD,SIG_IGN);表示父进程忽略SIGCHLD信号，该信号是子进程退出的时候向父进程发送的。
4. fork两次：通过两次调用fork。父进程首先调用fork创建一个子进程然后waitpid等待子进程退出，子进程再fork一个孙进程后退出。这样子进程退出后会被父进程等待回收，而对于孙子进程其父进程已经退出所以孙进程成为一个孤儿进程，孤儿进程由init进程接管，孙进程结束后，init会等待回收。

 

第三种方法忽略SIGCHLD信号，这常用于并发服务器的性能的一个技巧因为并发服务器常常fork很多子进程，子进程终结之后需要服务器进程去wait清理资源。如果将此信号的处理方式设为忽略，可让内核把僵尸子进程转交给init进程去处理，省去了大量僵尸进程占用系统资源

### (12) 孤儿进程

一个父进程退出，而它的一个或多个子进程还在运行，那么那些子进程将成为孤儿进程。孤儿进程将被init进程(进程号为1)所收养，并由init进程对它们完成状态收集工作。

### (13) 终端是哪个文件夹下的哪个文件？黑洞文件是哪个文件夹下的哪个命令？

- 终端  /dev/tty
- 黑洞文件  /dev/null

### (14) Linux 中进程有哪几种状态？在 ps 显示出来的信息中，分别用什么符号表示的？

1. 不可中断状态：进程处于睡眠状态，但是此刻进程是不可中断的。不可中断， 指进程不响应异步信号。
2. 暂停状态/跟踪状态：向进程发送一个 SIGSTOP 信号，它就会因响应该信号 而进入 TASK_STOPPED 状态;当进程正在被跟踪时，它处于 TASK_TRACED 这个特殊的状态。正在被跟踪”指的是进程暂停下来，等待跟踪它的进程对它进行操作。
3. 就绪状态：在 run_queue 队列里的状态
4. 运行状态：在 run_queue 队列里的状态
5. 可中断睡眠状态：处于这个状态的进程因为等待某某事件的发生（比如等待 socket 连接、等待信号量），而被挂起
6. zombie 状态（僵尸）：父亲没有通过 wait 系列的系统调用会顺便将子进程的尸体（task_struct）也释放掉
7. 退出状态

### (15) 使用什么命令查看磁盘使用空间？ 空闲空间呢?

df -hl

### (16) 使用什么命令查看网络是否连通? 

**netstat ** ：用于显示各种网络相关信息

**tcpdump** ： 截获本及网络接口的数据，

**ipcs** ： 检查系统上共享内存的分配，报告进程间通信设施状态，包括当前活动消息队列、共享内存段、信号量、远程队列和本地队列标题

**ipcrm** ： 手动解除系统上共享内存的分配

### (17)查看cpu信息

cat /proc/cpuinfo

uname -a 显示系统核心版本号、名称、机器类型等





## 6. Network

### (1) OSI/ISO七层协议栈

| 分层             | 作用                                                  | 协议                                                         |
| ---------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| 物理层           | 通过媒介传输**比特**                                  | IEEE802.3                                                    |
| 数据链路层       | 将网络层的IP数据报封装成帧和点到点的传递（帧：Frame） | PPP、MAC（网桥，交换机）                                     |
| 网络层           | 数据包从源到宿的传递和网际互连（包：Packet）          | IP（网际协议）, ICMP（网际控制报文协议）, ARP（地址解析协议）, |
| 传输层（运输层） | 提供端到端的可靠报文传递和错误恢复（段：segment）     | TCP（传输控制协议）, UDP（用户数据报协议）                   |
| 会话层           |                                                       |                                                              |
| 表示层           |                                                       |                                                              |
| 应用层           |                                                       | DNS, FTP, HTTP,                                              |

- 网络层：路由表包含：网络ID，子网掩码（判断IP所属网络），下一跳地址/接口

### (2) 链路层

- the minimum Ethernet frame size as 64 bytes and the maximum as 1518 bytes 

- HDLC(High-Level Data Link Control)

  - Frame separated by flag byte (01111110)

  -  Bit Stuffing 

     Sending: 0 inserted after every sequence of five 1s in other fields 

     Receiving: after five 1s, if sixth is 0, delete 0; if sixth starts with 10, delimiter

- PPP: (IP)

  also have delimiter(01111110)

- Token Ring (LAN,  IEEE 802.5)

- ##### 数据链路层协议可能提供的服务？

  成帧、链路访问、透明传输、可靠交付、流量控制、差错检测、差错纠正、半双工和全双工。最重要的是帧定界（成帧）、透明传输以及差错检测。

- 链路层网络层区别：

  


### (2) 网络层

- 抓包工具，有没有使用过tcpdump 

- 如何构建路由表

  

- Two functions: (1) Switch/Routing (2) Forwarding

![virtual circuit VS Datagram](D:\Intern\virtual circuit VS Datagram.png)

- IP:

  - TTL：Time to Live
  - 在router分段(fragment)，在host重组(re-assemble)

- ARP (Address resolution Protocol)

  - convert an IP address to physical(MAC) address
  - Using broadcasts : ARP Request, ARP Reply
  - Only works on LAN
  - Between Layer 2 and Layer 3(Network layer and Data Link Layer)

- ICMP(Internet Control Message Protocol)

  - transafer <font color="red">error and control msgs</font>

  - encapsulated in IP datagram, not reliable

    ![ICMP header](D:\Intern\ICMP header.png)

  - Typical Example : ping

  - Traceroute: measure the number of hops, and calculate the RTT

- DHCP( Dynamic Host Configuration Protocol)

  - Assign dynamic IP addresses to hosts on a network, typical for dial-up and LAN users 
  - DHCP-Discover(multicast)
  - DHCP-Offer(tell client the IP address)
  - DHCP-Request(multicast) : need to tell other servers they were rejected
  - DHCP-ACK

### (3) 传输层

#### TCP

- TCP：面向连接的、可靠的、基于**字节流**的传输层通信协议，传输的单位是报文段

- 特征：面向连接，只能一对一，可靠交互，全双工通信（发送的同时也可以接收数据）

##### 如何保证可靠性：

  - 确认和超时重传
  - 数据合理分片和排序
  - 流量控制
  - 拥塞控制
  - 数据校验
  - 对于可靠性，TCP通过以下方式进行保证：
    - 数据包校验：目的是检测数据在传输过程中的任何变化，若校验出包有错，则丢弃报文段并且不给出响应，这时TCP发送数据端超时后会重发数据；
    - 对失序数据包重排序：既然TCP报文段作为IP数据报来传输，而IP数据报的到达可能会失序，因此TCP报文段的到达也可能会失序。TCP将对失序数据进行重新排序，然后才交给应用层；
    - 丢弃重复数据：对于重复数据，能够丢弃重复数据；
    - 应答机制：当TCP收到发自TCP连接另一端的数据，它将发送一个确认。这个确认不是立即发送，通常将推迟几分之一秒；
    - 超时重发：当TCP发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段；
    - 流量控制：TCP连接的每一方都有固定大小的缓冲空间。TCP的接收端只允许另一端发送接收端缓冲区所能接纳的数据，这可以防止较快主机致使较慢主机的缓冲区溢出，这就是流量控制。TCP使用的流量控制协议是可变大小的滑动窗口协议。

##### TCP拥塞控制：

  - 慢开始（slow-start）：CWND（拥塞窗口）初始为1，逐步增大，先指数，到达门限时线性
  - 拥塞避免：慢开始门限设置为出现拥塞时发送端窗口大小的一半，但不能小于2，然后把 CWND重新置为1，执行慢开始算法
  - 快重传：收到三个同样的确认报文就应当立即重传
  - 快恢复

##### TCP三次握手

  - 客户端发送SYN给服务器，请求建立连接
  - 服务端收到SYN，并回复SYN+ACK给客户端（同意建立连接）
  - 客户端回复ACK给服务端（收到了同意报文）
  - 服务端收到客户端ACK，连接已建立，可以数据传输

- 为什么要进行三次握手

  - 双方都需要确认对方收到了自己发送的序列号
  - 为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误

##### TCP四次挥手

  - 客户端发送FIN给服务器（请求释放连接）

  - 服务器收到FIN并回复ACK给客户端

  - 客户端收到ACK，释放客户端到服务器的连接（还可以接收服务器的数据）

  - 服务器发送完数据

  - 服务器发送FIN+ACK给客户端

  - 客户端收到并回复ACK给服务端

  - 服务端收到ACK后，释放连接（超时也会释放）

  - 为什么客户端释放最后需要 TIME-WAIT 等待 2MSL 呢？ 

    1. 为了保证客户端发送的最后一个 ACK 报文能够到达服务端。若未成功到达，则服务端超时重传 FIN+ACK 报文段，客户端再重传 ACK，并重新计时。
    2. 防止已失效的连接请求报文段出现在本连接中。TIME-WAIT 持续 2*MSL(Max SegmentLife Time) 可使本连接持续的时间内所产生的所有报文段都从网络中消失，这样可使下次连接中不会出现旧的连接报文段。

  - 四次挥手客户端的状态机转换：

    

#### UDP

- UDP：无连接的传输层协议，提供面向事务的简单不可靠信息传送服务，单位是用户**数据报**
- 特征：无连接，尽最大努力交付，面向报文，没有拥塞控制，支持一对一、一对多、多对一、多对多的交互通信，首部开销小

##### udp调用connect

  - UDP中可以使用connect系统调用
  - UDP中connect操作与TCP中connect操作有着本质区别.
    - TCP中调用connect会引起三次握手,client与server建立连结.UDP中调用connect内核仅仅把对端ip&port记录下来.
  - UDP中可以多次调用connect,TCP只能调用一次connect.UDP多次调用connect.
  - 有两种用途:
    1.  指定一个新的ip&port连结.
    2. 断开和之前的ip&port的连结.指定新连结,直接设置connect第二个参数即可.断开连结,需要将connect第二个参数中的sin_family设置成 AF_UNSPEC即可. 
  - UDP中使用connect可以提高效率.原因如下:
    - 普通的UDP发送两个报文内核做了如下:#1:建立连结#2:发送报文#3:断开连结#4:建立连结#5:发送报文#6:断开连结, 
    - 采用connect方式的UDP发送两个报文内核如下处理:#1:建立连结#2:发送报文#

##### UDP中使用connect的好处:

  - 会提升效率.前面已经描述了
  - 高并发服务中会增加系统稳定性.原因:假设client A 通过非connect的UDP与serverB,C通信.B,C提供相同服务.为了负载均衡,我们让A与B,C交替通信.A 与 B通信IPa:PORTa\<----> IPb:PORTbA 与 C通信IPa:PORTa'\<---->IPc:PORTc 
- 假设PORTa 与 PORTa'相同了(在大并发情况下会发生这种情况),那么就有可能出现A等待B的报文,却收到了C的报文.导致收报错误.解决方法内就是采用connect的UDP通信方式.在A中创建两个udp,然后分别connect到B,C.



#### TCP 与 UDP 的区别

1. TCP 面向连接，UDP 是无连接的；
2. TCP 提供可靠的服务，也就是说，通过 TCP 连接传送的数据，无差错，不丢失，不重复，且按序到达；UDP 尽最大努力交付，即不保证可靠交付
3. TCP 的逻辑通信信道是全双工的可靠信道；UDP 则是不可靠信道
4. 每一条 TCP 连接只能是点到点的；UDP 支持一对一，一对多，多对一和多对多的交互通信
5. TCP 面向字节流（可能出现黏包问题），实际上是 TCP 把数据看成一连串无结构的字节流；UDP 是面向报文的（不会出现黏包问题）
6. UDP 没有拥塞控制，因此网络出现拥塞不会使源主机的发送速率降低（对实时应用很有用，如 IP 电话，实时视频会议等）
7. TCP 首部开销20字节；UDP 的首部开销小，只有 8 个字节

- 路由器转发规则，一些常见的概念。tcp的三次握手四次挥手的过程，tcp和udp的区别 

- tcp三次握手漏洞：收到一个恶意攻击的ip一直请求连接，然后服务器会发送ack确认，但是永远等不到回复，就会导致服务器的NIC，内存，cpu占用率超载，这种攻击方式叫做基于TCP半开回话的洪水攻击 

- TCP黏包问题：因为TCP是基于字节流的，所以可能两个包没有边界；

  解决办法：包头加上包体长度，数据包之间设置边界

- TCP流量控制：可变窗口

  发送端窗口大小不能超过接收端窗口大小的值

  

### (4) 应用层

- 应用层的常见协议，http协议，长连接短连接，常见端口号 

- DNS：域名系统，将域名与IP地址相互映射

- FTP：文件传输协议，使用TCP数据报

- WWW：万维网，由许多相互链接的超文本组成的系统

- HTTP：用于分布式、协作式和超媒体信息系统的应用层协议

- 以上是Client/Server模型应用，P2P：Skype

  ![Skype](D:\Intern\Skype.png)

##### http协议和https协议的不同和其大概原理 :

|                        | HTTP                        | HTTPS                                                        |
| ---------------------- | --------------------------- | ------------------------------------------------------------ |
| Full Name              | HyperText Transfer Protocol | HyperText Transfer Protocol Secure                           |
| Informaiton Encryption | No                          | Yes **SSL (secure sockets layer) certificate** <br> TLS (Transport Layer Security) protocol  传输层也会提供防篡改保护 |
| 端口                   | 80                          | 443                                                          |

  

##### 对称加密与非对称加密

​	对称密钥加密是指加密和解密使用同一个密钥的方式，这种方式存在的最大问题就是密钥发送问题，即如何安全地将密钥发给对方；而非对称加密是指使用一对非对称密钥，即公钥和私钥，公钥可以随意发布，但私钥只有自己知道。发送密文的一方使用对方的公钥进行加密处理，对方接收到加密信息后，使用自己的私钥进行解密。

​	由于非对称加密的方式不需要发送用来解密的私钥，所以可以保证安全性；但是和对称加密比起来，它非常的慢，所以我们还是要用对称加密来传送消息，但对称加密所使用的密钥我们可以通过非对称加密的方式发送出去。



##### 从输入网址到获得页面的过程

1. 浏览器查询 DNS，获取域名对应的IP地址:具体过程包括浏览器搜索自身的DNS缓存、搜索操作系统的DNS缓存、读取本地的Host文件和向本地DNS服务器进行查询等。对于向本地DNS服务器进行查询，如果要查询的域名包含在本地配置区域资源中，则返回解析结果给客户机，完成域名解析(此解析具有权威性)；如果要查询的域名不由本地DNS服务器区域解析，但该服务器已缓存了此网址映射关系，则调用这个IP地址映射，完成域名解析（此解析不具有权威性）。如果本地域名服务器并未缓存该网址映射关系，那么将根据其设置发起递归查询或者迭代查询；
2. 浏览器获得域名对应的IP地址以后，浏览器向服务器请求建立链接，发起三次握手；
3. TCP/IP链接建立起来后，浏览器向服务器发送HTTP请求；
4.  服务器接收到这个请求，并根据路径参数映射到特定的请求处理器进行处理，并将处理结果及相应的视图返回给浏览器；
5. 浏览器解析并渲染视图，若遇到对js文件、css文件及图片等静态资源的引用，则重复上述步骤并向服务器请求这些资源；
6. 浏览器根据其请求到的资源、数据渲染页面，最终向用户呈现一个完整的页面。

   

##### SQL注入攻击实例

​	比如，在一个登录界面，要求输入用户名和密码，可以这样输入实现免帐号登录：

  ```
  用户名： ‘or 1 = 1 --
  密 码：12
  ```

​	用户一旦点击登录，如若没有做特殊处理，那么这个非法用户就很得意的登陆进去了。这是为什么呢?下面我们分析一下：从理论上说，后台认证程序中会有如下的SQL语句：String sql = “select * from user_table where username=’ “+userName+” ’ and password=’ “+password+” ‘”; 因此，当输入了上面的用户名和密码，上面的SQL语句变成：SELECT * FROM user_table WHERE username=’’or 1 = 1 – and password=’’。分析上述SQL语句我们知道， username=‘ or 1=1 这个语句一定会成功；然后后面加两个-，这意味着注释，它将后面的语句注释，让他们不起作用。这样，上述语句永远都能正确执行，用户轻易骗过系统，获取合法身份。

- 应对方法

1. 参数绑定

   使用预编译手段，绑定参数是最好的防SQL注入的方法。目前许多的ORM框架及JDBC等都实现了SQL预编译和参数绑定功能，攻击者的恶意SQL会被当做SQL的参数而不是SQL命令被执行。在mybatis的mapper文件中，对于传递的参数我们一般是使用#和  \$来获取参数值。当使用#时，变量是占位符，就是一般我们使用javajdbc的PrepareStatement时的占位符，所有可以防止sql注入；当使用$时，变量就是直接追加在sql中，一般会有sql注入问题。

2. 使用正则表达式过滤传入的参数

  

##### HTTP的长连接和短连接?

HTTP的长连接和短连接本质上是**TCP长连接和短连接**。HTTP属于应用层协议.

*短连接:浏览器和服务器每进行一次HTTP操作，就建立一次连接，但任务结束就中断连接。*

-  长连接:当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的 TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接。Keep-Alive不会永久保持连接，它有一个保持时间，可以在不同的服务器软件（如Apache）中设定这个时间。实现长连接要客户端和服务端都支持长连接。
- TCP短连接: client向server发起连接请求，server接到请求，然后双方建立连接。client向server发送消息，server回应client，然后一次读写就完成了，这时候双方任何一个都可以发起close操作，不过一般都是client先发起 close操作.短连接一般只会在 client/server间传递一次读写操作
- TCP长连接: client向server发起连接，server接受client连接，双方建立连接。Client与server完成一次读写之后，它们之间的连接并不会主动关闭，后续的读写操作会继续使用这个连接。



### (5) 对于socket编程，accept方法是干什么的

三次握手：

- 服务器调用listen进行监听
- 客户端调用connect来发送syn报文
- 服务器协议栈负责三次握手的交互过程。连接建立后，往listen队列中添加一个成功的连接，直到队列的最大长度。
- 服务器调用accept从listen队列中取出一条成功的tcp连接，listen队列中的连接个数就少一个，然后程序就可以看到自己与用户的通信

### (6) select, epoll（IO多路复用）

- 阻塞IO模型

  　　最传统的一种IO模型，即在读写数据过程中会发生阻塞现象。

  　　当用户线程发出IO请求之后，内核会去查看数据是否就绪，如果没有就绪就会等待数据就绪，而用户线程就会处于阻塞状态，用户线程交出CPU。当数据就绪之后，内核会将数据拷贝到用户线程，并返回结果给用户线程，用户线程才解除block状态。

- select：同时监听多个socket，从用户空间将fd_set拷贝到内核，对所有的fd进行一次poll操作，即把当前进程挂载到fd上，检查是否有事件触发，无则休眠，再恢复做poll，直到有设备有事件触发，返回用户空间，对相关fd进行读或者写操作

- epoll：epoll不需要通过遍历的方式，而是在内核中建立了file节点，并且通过注册响应事件的方式，当有响应事件发生时采取相应的措施，并把准备就绪的事件放入链表中，从而epoll只关心链表中是否有数据即可。 

  **epoll的触发模式**

  - 水平触发：就是与select和poll类似，当被监控的文件描述符上有可读写事件发生时，epoll_wait()会通知处理程序去读写。如果这次没有把数据一次性全部读写完(如读写缓冲区太小)，那么下次调用 epoll_wait()时，它还会通知你在上次没读写完的文件描述符上继续读写 
  - 边缘触发：只告诉进程哪些文件描述符刚刚变为就绪状态，它只说一遍，如果我们没有采取行动，那么它将不会再次告知，这种方式称为边缘触发，理论上边缘触发的性能要更高一些，但是代码实现相当复杂。

  

### (7) socket中recv函数的返回值及意义

1. recv先等待socket的发送缓冲中的数据被协议传送完毕，如果协议在传送socket的发送缓冲中的数据时出现网络错误，那 么recv函数返回SOCKET_ERROR。
2. 如果socket的发送缓冲中没有数据或者数据被协议成功发送完毕后，recv先检查套接字socket的接收缓冲区，如果接收缓冲区中没有数据或者协议正在接收数据，那么recv就一直等待，直到协议把数据接收完毕。当协议把数据接收完毕，rec函数就把socket的接收缓冲中的数据copy到buffer中（注意协议接收到的数据可能大于buf的长度，所以 在这种情况下要调用几次recv函数才能把socket的接收缓冲中的数据copy完。recv函数仅仅是copy数据，真正的接收数据是协议来完成的），rec函数返回其实际copy的字节数。如果recv在copy时出错，那么它返回SOCKET_ERROR；如果recv函数在等待协议接收数据时网络中断了，那么它返回0。



## 7. 设计模式

### (1) 单例模式。准备了一下C++下写单例模式的代码，还有单例模式线程安全的问题，怎么防范。 

- 仅允许类的一个实例在应用中被创建：将类的构造方法设为private，提供一个方法返回类的实例，只可以通过这个方法来访问实例

  * 懒汉方法

  ```c++
  class Singleton
  {
  public:
      static Singleton* GetInstance();
  private:
      Singleton() {}
      static Singleton *m_pInstance;
  };
  //Singleton.cpp
  Singleton* Singleton::m_pInstance = NULL;
  Singleton* Singleton::GetInstance()
  {
      if (m_Instance == NULL)
      {
          //两次判断可以避免多次加锁解锁
          Lock();
          if (m_Instance == NULL)
          {
              m_Instance = new Singleton();
          }
          UnLock(); 
      }
      return m_pInstance;
  }
  ```

  * 饿汉式

  ```c++
    class Singleton {  
        private static Singleton instance = new Singleton();  
        private Singleton (){}  
        public static Singleton getInstance() {  
        	return instance;  
        }  
    }
  ```

    

- 使用场景：整个项目中需要一个共享访问点或共享数据，创建一个对象需要消耗的资源过多，需要定义大量的静态常量和静态方法

### (2) 大数据

hash和bitmap

bitmap：每个位为0或1代表其对应元素不存在或存在

### (3) OOP设计（面向对象设计）

原则是找名词，一个名词一个类

### (4) 观察者模式

当对象间存在一对多关系，使用观察者模式。比如一个对象被修改时，通知它的依赖对象

在抽象类里有一个 ArrayList 存放观察者们。 

### (5) ctrl+z，ctrl+y，用什么数据结构好 



## 8. 特殊问题

### 1.海量数据排序问题

- hash到小文件中，然后在小文件排序。然后合并的时候，用堆，每个小文件取一个，然后最小的拿走再加入对应文件的数字，直到结束。

- 如果要求文件IO次数尽量少，比如：

  一个文件中20亿个int，有重复但不超过10000个，排序后输出到另一个文件（要求减少文件IO）

### 2. 海量数据出现次数最多数据

- hash
- 但是内存有限制的话，可以先分段到小文件中，然后再hash
- 直接hash到小文件，再在小文件hash，避免集中在一个区间造成退化
- 如果数据给的是40亿都是一个数字，那这个数字的index计数就会爆掉（int最大21亿左右），可以把初始值设置为-21亿
- 如果增加到80亿呢？一边遍历一边判断，如果超过40亿，也不可能有比它更多的了，直接返回

### 3. 40亿整数，再给一个新数字，判断是否在40亿当中

- 分布式机器，多台机器一起查找并合并
- bitmap，注意不是40亿个数字就要40亿个位，而是都是整数，所以覆盖所有整数范围就是2\^32大概42亿，所以需要申请2\^32个位，一个int8个位，所以需要2^29个int，占用500M内存

### 4. 外排序

### 5. 5000个数的set，如何实现增添查找删除

### 6. 设计方法对一篇文章进行敏感词检查，敏感词库量级为1000万

