# Hidden World Beneath Your Code

## Abstraction

Abstraction is the process of generalizing concrete details, away from the study of object and systems, to foucs attention on details of greater importance

抽象就是当我们思考和操作时，并不关心具体使用的是哪一个（对象或实现）。换言之，抽象是专门化的对立面。

抽象化的计算机结构：

- 问题（Problem）计算机想要解决的问题
- 算法（Algorithm）将问题转换成算法描述语言
- 程序（Program）利用编译器实现算法
- 操作系统（Operating System） 程序如何在OS上实现
- ISA 指令集架构 x86 RAM
- 微架构 （Microachitecture）CPU
- 逻辑电路 （Logic）如何实现CPU设计 
- 电路（Circuits）


![alt text](<src/Pasted image 20250627191345.png>)

CPU 的抽象结构：

- 寄存器（Register）：存储程序所需数据，用于CPU访问
- ALU：计算单元
- 控制单元：控制CPU执行


![alt text](<src/Pasted image 20250627191607.png>)

一般而言，所处体系越高，存储量越小，但访问速度比较快，所处体系越低，存储量也越大，但此时访问速度比较蛮。因此权衡存储和访问速度是一个关键的问题

内核：

- Win：NT
- macOS，iOS:Darwin 
- Linux，Android：Linux

## Operating System 

> 没有什么能比自己写个内核更能加深对操作系统的理解了


Operating System is resource abstractor and resource allocator：OS 管理硬件资源，并将其包裹成抽象的层，对应用程序提供一个接口，也是资源的分配者。

抽象角度的看内核代码：

```C
void processEvent(event){
	switch(event.type)
		case NETWORK_COMMUNICATION:
			NetworkManager.handleEvent(event);
			break;
		case SEGMENTATION_FAULT;
		case INVALID_MODE:
			ProcessManager.handleEvent(event);
			break;
}
```


通俗来说：OS 等待事件（events）的发生，待发生后调用处理事件的代码。

### Traps

在 RISC-V 架构中，有两种特殊的events，成为 Traps，这会让处理器从当前程序跳转到另一个程序（Handler）中处理某些特定的情况。

- Interrupts：external asynchronous events 外部异步中断事件，并不是由当前正在执行的指令直接引发的
- Exceptions：unusual condition occuring at instruction run time 指令有问题，在指令执行过程中遇到某种特殊或非法的情况

![alt text](<src/Pasted image 20250627193512.png>)

当一个 Trap 发生时，控制流跳转 Exception Handler，执行异常处理，然后返回用户程序。这个过程实质上一种控制权从用户空间(User Mode)转移到内核空间(Kernel Mode)的过程。 

![alt text](<src/Pasted image 20250627201638.png>)


为了防止用户程序影响计算机本身，需要对程序的权限做出限制。**系统安全**是一个很重要的事情。通常的做法在 CPU 添加 mode （模式位），mode 会限制调用 CPU 的权限，mode bit 用于表示权限级别，用户程序权限一般为 0，操作系统的权限比较高。DOS 系统中并没有区分用户程序和OS。



### System call

![alt text](<src/Pasted image 20250627200802.png>)


但是有些时候，用户程序还是需要一定的权限进行操作，如访问 CPU、磁盘等，因此系统提供了系统调用 (System call)，用于处于组件与用户之间的互动。用户在进行系统调用时，通过传递一个系统调用编号，来告知内核，它所请求的系统调用，内核通过这个编号进而找到对应的处理系统调用的函数。这个行为很像 API ，实际上一些标准库对 system call 进行了封装处理，然后以 API 形式提供给用户，每一个系统调用对应着封装的 API，但翻过不一定，因为一些 API 将多个系统调用组合使用。常见的如 C 语言中的 `malloc` 函数调用了一定内存空间。

System Calls：When a user program needs to do something privileged, it calls a system call. A system call is a special kind of trap.

```C
void processEvent(event){
		...
		case SYSTEM_CALL:
			SystemCallManager.handleEvent(event);
			break;
		...
}
```

OS 需要一直保持对 CPU 的控制权，程序不能陷入无限循环并锁死整个计算机，也不能占用不公平的计算资源；内核重新获得控制权的一种方式是通过中断的发生； 

例如，为了防止出现死循环，在 CPU 中内部装一个定时器（Timer）：定时发送时间中断，确保操作系统获得计算机的使用权。

## Process 

进程调度：时间中断切换程序，当时间切换足够快时，可以视作两个程序同时跑（单核CPU）

A process is a program in execution, a unit of resource allocation an protection

![alt text](<src/Pasted image 20250627205313.png>)


当一个程序运行时，操作系统会为它分配**独立的地址空间**，包括代码、数据、栈和堆等内存区域，这些构成了进程的完整运行状态。

进程在内存中的布局结构：

- 代码：Text section, initially stored on disk。也就是存储程序的机器指令，从磁盘加载，只读。
- 数据段
	- BSS：Uninitialized data 未初始化的数据如 `int a`
	- Data：Initialized data 初始化后的数据如 `int a =5`
- 栈：Function frames, local variables 存放函数调用过程中的局部变量、返回地址、参数等。**向下增长**（地址从高到低），如图中箭头所示。每一次函数调用，都会新建一个栈帧，记录该次调用的信息
- 堆：Dynamic memory allocation 用与动态内存分配，向上增长，需要手动申请和释放

一般而言栈和堆不会撞车。

一个进程还包含其运行时的“上下文”，这是让操作系统能随时挂起/恢复进程的关键：

上下文（Context）

- Program Counter：指向下一条即将执行的指令
- Content of the processor's registers 

下面是一段程序执行过程中内存分配情况。


![alt text](<src/Pasted image 20250628083828.png>)

### Process Scheduling

当有多个进程处于就绪状态时，操作系统需要一个策略来选择其中一个进程运行，这个选择过程就叫做进程调度（Process Scheduling）。

特点：

- Non-preemptive：The process must finish its execution before the OS can schedule another process. 一般而言，上一个进程结束执行后 OS 才会调用下一个进程
- Preemptive：The OS can interrupt a process to schedule another one OS也能打断一个进程来调度另一个进程，这种情况是让某些进程优先执行（如需要的时间比较短的进程）
	- Round Robin
	- Priority-based 


**Also decide which process to run on which core** （Control by Core Affinity）



### Inter-Process Communication 
每个进程都有自己独立的系统资源，彼此隔离，为了实现不同进程之间的资源访问和协调工作，需要使用进程间通信（Inter-Process Communication）。

Message Passing 是一种进程间通信机制，两个进程通过操作系统提供的接口，发送和接受结构化的消息，实现数据交换和协作，常见的机制有：

- Message Queue：系统维护一个消息链表，进程将消息发送到队列。
- Socket：本地或网络通信同一接口

IPC 虽然功能强大，但缺点是其开销大，实现复杂。

- Due to isolation, processes do not share address space：由于进程间**相互隔离**，每个进程有自己独立的内存空间，所以它们**不能直接访问对方的数据**，这就导致通信变得复杂
- For message passing：
	- Need to maintain a message queue for each process：操作系统必须**为每个进程维护消息队列**，用于临时保存消息。这不仅增加了内存开销，还增加了内核管理负担。
	- Data need to be copied between address spaces：消息从一个进程发出后，**需要从发送者的地址空间复制到内核，然后再从内核复制到接收者地址空间**，这两个拷贝会带来明显的性能损耗（称为「双拷贝开销」）。
- For shared memory：
	- Synchronization is needed：多个进程同时访问共享内存时可能会产生**竞争条件**（Race Condition），所以必须引入**同步机制**（比如互斥锁、信号量）来确保读写的正确性
	- It’s complex for OS to manage
- Must flush cache when switching between processes ：**每个进程有自己的虚拟地址空间**，但是 CPU 的缓存是基于物理地址或虚拟地址实现的。如果两个进程使用相同的虚拟地址（但映射到不同的物理内存），而缓存没被清空，会导致**缓存别名问题（cache aliasing）** ，出现错误的读写结果。因此，在切换进程时，系统可能必须**清空缓存以保证内存访问正确性**，这带来了严重的性能开销。