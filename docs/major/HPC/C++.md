# C++ 并行编程
!!! info 
    这一节内容主要是关于 C++ 编程，但是我懂 C++ 的部分所以只记了并行编程部分的东西.

    部分资料参考 [C++ 并发编程实战](https://book.douban.com/subject/35653912/) ，注意是第二版翻译会好点（读原本当我没说）

    
## Thread 
[std::thread - cppreference.cn - C++参考手册](https://cppreference.cn/w/cpp/thread/thread)

线程创建：

```Cpp
template<class F,class ... Args>
	explicit thread(F&& f,Args&&... args);
thread(thread&& other) noexcept;
```

- other：利用其他线程创建新线程
- f：新线程中的可调用执行对象
- args：新函数的参数



C++ interprets ambiguous syntax as function declarations.也就是说 C++ 编译器在遇到语法歧义时，会优先将它解释成函数声明。比如说

```cpp
struct background_task{
	void operator()(){
		//do something
	}
};

thread my_thread(background_task());
```

C++ 编译并不会认为你创建了一个线程，反而是创建了一个函数声明，声明了一个返回 `thread` 的函数。这是因为C++倾向于静态类型和声明优先，当语法含糊不清的时候，编译器会优先认为是声明而非表达式，这被称为 "The Most Vexing parse" 。解决方法一般是采用列表初始化，或者采用 Lambda 表达式。

一般而言，需要对一个未销毁的 `thread` 对象使用 `join()` 或 `detach()` ，否则 `thread` 的析构函数会调用 `std::terminate` 终止程序。

- `join()` Wait for thread completion before continuing 等待线程完成其执行
- `detach()` Run thread independently without waiting 允许线程独立于线程句柄执行

`join()` 一般需要同 RAII 原则（以栈管堆）一起使用，即保证资源的合理调用与释放

### Passing Arguments to a Thread Function 
线程具有内部存储空间，参数会按照默认方式先复制到该处，新创建的执行线程才能直接访问它们。 然后，这些副本被当成临时变量，以右值形式传给新线程上的函数或可调用对象。

**Key Principle：Thread constructor copies arguments as rvalues**。这是为了防止发生“悬空引用”和数据竞争，线程库需要复制这些参数的值以保证子线程的运行。

当将含参的函数传给 `thread` 时，默认实参会被忽略，引用类型也会被忽略（需要使用 `std::ref`）。

### Transferring Ownership of a Thread 

假设读者要编写函数，功能是创建线程，并置于后台运行，但该函数本身不等待线程完结，而是将其归属权向上移交给函数的调用者；或相反地，读者想创建线程，遂将其归属权传入某个函数，由它负责等待该线程结束。两种操作都需要转移线程的归属权.

`thread` 是 move-only 类型，不能拷贝，只能通过移动(move)转移所有权。对于一个具体的执行线程，其归属可以在几个 `thread` 实例间转移，从而实现对多个线程的管理。

移动语义使我们能够将动态对象（如 `std::unique_ptr`）显式移动到其他线程中。


```cpp
void process_big_object(std::unique_ptr<big_object>);

std::unique_ptr<big_object> p(new big_object);
p->prepare_data(42);
std::thread t(process_big_object,std::move(p));
```

可以将 `thread` 对象存放到 `std::vector` 等容器中，用引用形式调用。
### Choosing the Number of Threads at Runtime

`std::thread::hardware_concurrency` 能够返回程序在运行中可真正并发的线程数量。

```cpp
unsigned int num_threads = thread::hardware_concurrency(); 

if (num_threads == 0) { 
	num_threads = 2; // Fallback if not available 
} 
cout << "Using " << num_threads << " threads" << endl; 

vector<thread> threads; 
for (unsigned int i = 0; i < num_threads; ++i) { 
	threads.emplace_back(worker, i); 
} 

for (auto& t : threads) {
	t.join()
}
```


并行版的 `accumulate` 的实现

首先定义对每个线程定义工作函数：

```cpp
template<typename Iterator, typename T>
void accumulate_block(Iterator first,Iterator last,T& result){
	reslut = accmulate(first,last,result)
}
```


Function Purpose: 

- Each thread processes a block of data
- Computes sum of elements from first to last
- Stores result in reference parameter 
- Uses standard library last accumulate function

然后计算最优的线程和工作分布

```cpp
template<typename Iterator, typename T>
T parallel_accumulate(Iterator first, Iterator last, T init) { 
	unsigned long const length = distance(first, last);
	if (!length) return init; 
	
	// Configuration parameters 
	unsigned long const min_per_thread = 25; 
	unsigned long const max_threads = 
		(length + min_per_thread- 1) / min_per_thread;
		
	unsigned long const hardware_threads = 
		thread::hardware_concurrency(); 
	
	unsigned long const num_threads = 
		min(hardware_threads != 0 ? hardware_threads : 2, max_threads);
	
	unsigned long const block_size = length / num_threads; 
	// ... 
}
```

最后创建线程和分布工作

```cpp
// Storage for results and threads 
vector<T> results(num_threads); 
vector<thread> threads(num_threads- 1); 
// Create worker threads (all but last) 
Iterator block_start = first; 
for (unsigned long i = 0; i < (num_threads- 1); ++i) { 
	Iterator block_end = block_start; 
	advance(block_end, block_size); 
	threads[i] = thread(accumulate_block<Iterator,T>, block_start, block_end, ref(results[i])); 
	block_start = block_end; 
} 
// Main thread handles the last block 
accumulate_block(block_start, last, results[num_threads- 1]); 
// Wait for all threads and combine results 
for (auto& entry : threads) entry.join(); 
return accumulate(results.begin(), results.end(), init);
```

### Identifying Threads 

线程 ID 所属型别是 `std::thread::id` ，主要有两种获得方式

Key Functions:

- 调用 `std::this_thread::get_id()`  可获得当前线程的 ID
- `std::thread` 对象上的成员函数`get_id()` ，如果对象没有关联任何执行线程，则会得到一个按默认构造方式成的对象，表示“线程不存在”

线程 ID 的特点：

- Unique：每一个执行线程都有一个独一无二的 ID
- Comparable：可以按序比较
- Hashable：可以作为容器中的键值
- Default-constructible：默认 ID 表示无线程

### Summary 

![alt text](<src/Pasted image 20250703235352.png>)

## mutex 
多线程并行计算面临的一个问题是线程之间共享数据的处理，不正确地使用共享数据会引起严重的错误。

mutex: 访问一个数据结构前，先锁住与数据相关的互斥；访问结束后，再解锁互斥。C++线程库保证了，一旦有线程锁住了某个互斥，若其他线程试图再给它加锁，则须等待，直至最初成功加锁的线程把该互斥解锁。这确保了全部线程所见到的共享数据是自洽的（self-consistent），不变量没有被破坏

C++ 标准库提供了类模板：`std::lock_guard()` ，针对互斥类融合实现了 RAII 手法：在构造时给互斥加锁，在析构时解锁，从而保证互斥总被正确解决

```cpp
std::mutex counter_mutex; 
int counter = 0; 
void increment_safe(int iterations) { 
	for(int i = 0; i < iterations; i++) { 
	std::lock_guard lock(counter_mutex); 
	counter++; // Now thread-safe! 
	// Automatic unlock when lock goes out of scope 
	}
}
```

RAII Benefits: 

- Exception safety 
- Automatic cleanup 
- Nomanual lock/unlock

### Deadlock Prevention 

```cpp
std::mutex mutex1, mutex2; 
void thread1() { 
	std::lock_guard lock1(mutex1); 
	// ... some work ... 
	std::lock_guard lock2(mutex2); // Waits forever 
} 
void thread2() {
	std::lock_guard lock2(mutex2); 
	// ... some work ... 
	std::lock_guard lock1(mutex1); // Waits forever 
}
```

死锁情形：两个线程都需要同时锁住两个互斥才能进行某些操作，但它们分别都只锁住了一个互斥，等着给另一个互斥加锁，于是双方毫无进展，因为它们同在等对方解锁互斥。

- 顺序加锁：
```cpp
std::lock_guard<std::mutex> lock1(mutex1);
std::lock_guard<std::mutex> lock2(mutex2);
```
- Atomic Multi-lock:
```cpp
std::unique_lock<std::mutex> lock1(mutex1,std::defer_lock)
std::unique_lock<std::mutex> lock2(mutex2,std::defer_lock)
std::lock(lock1, lock2);
```
- Address-based Ordering 
```cpp
if (&mutex1 < &mutex2){
	std::lock_guard<std::mutex> lock1(mutex1);
	std::lock_guard<std::mutex> lock2(mutex2);
}else{
	std::lock_guard<std::mutex> lock2(mutex2);
	std::lock_guard<std::mutex> lock1(mutex1);
}
```

## condition_variable 
### Waiting for an Event or Other Condition 
如果线程需要等待线程完成任务，可以采取几种不同方式。

- 调用 `std::this_thread::sleep_for()` ，这会在各次查验之间短期休眠。

```cpp
bool data_ready = false; 
std::mutex data_mutex; 

void consumer() {
	while (true) { 
		{ 
			std::lock_guard lock(data_mutex); 
			if (data_ready) { /* Process data */ 
			data_ready = false; break; 
			} 
		} 
		std::this_thread::sleep_for(std::chrono::milliseconds(100)); // Wastes CPU cycles and introduces latency! 
	}
 }
```

线程休眠会使得处理时间不被浪费，但是休眠期的长度难以预知：太短会虚耗处理时间；太长会导致过度休眠。

- Wait for notification instead of polling，也就是等待某一事件的发生，这样的好处就是提高等待的效率，能够立即做出反应，没有 CPU 的浪费。

### Waiting for a Condition with Condition Variables 

C++ 提够了两种条件变量的实现：`std::condition_variable` 和 `std::condition_variable_aby` 。两者都需要与互斥配合使用。`std::condition_variable` 仅限与 `std::mutex` 一起使用。而 `std::_condition_variable_any` 的使用更为通用，它只需要某一类型达到互斥的最低标准，但可能产生额外的开销。

```cpp
std::mutex m; 
std::condition_variable cv; 
bool condition = false;

void waiter() { 
	std::unique_lock lock(m); 
	cv.wait(lock, []{return condition;});
	// Condition is now true 
} 

void notifier() { 
	{
	std::lock_guard lock(m); 
	condition = true; 
	} 
	cv.notify_one(); // or notify_all() 
}
```

`std::condition_variable::wait()` 函数用于线程间同步的阻塞等待机制，让线程等待某个条件成立时继续，lambda 函数用于表达需要等待的成立条件。我们一般释放锁后再调用 `notify_one()` ，这是用来唤醒一个等待的线程，如果用了 `notify_all()` 则唤醒所有等待的线程。

Two versions of `wait()`

```cpp
//Version 1: Basic wait
void wait(std::unique_lock<std::mutex>& lock);

//Version 2:Wait with predicate(recommend)
template<class Predicate>
void wait(std::unique_lock<std::mutex>& lock, Predicate pred);
```

Predicate version equivalent to: 
```cpp
while(!pred()) wait(lock);
```

How `wait()` Wokrs Internally 
1. Atomically releases the mutex 
2. Blocks current thread 
3. Waits for notification 
4. Re-acquires mutex when awakened 
5. Checks predicate (if provided

- `lock_guard` : RAII-only, cannot be manually unlocked 
- `unique_lock`：Flexible, supports deferred/conditional locking 

`lock_guard` 和 `unique_lock` 都能实现锁住互斥。但是`unique_lock` 可以实现灵活性：在等待期间解锁互斥，结束等待后重新加锁，也就是灵活的加锁解锁，但是 `lock_guard` 没有这种灵活性。

- `notify_one()`：When only one thread should process the event
- `notify_all()`：When multiple threads need to check the condition 

**Best Practice**: Always modify shared state before notifying

### Building a Thread-Safe Queue with Condition Variables 

如果我们打算构建一个线性安全队列，需要考虑如下需求：

- Multiple producers can push safely 线程可以同时调用 `push()` ，使用 `std::mutex` 保证每次 `push` 操作的原子性。
- Multiple consumers can pop safely 线程可以调用 `pop()` 同时也需要用互斥锁保护。
- Consumers wait when queue is empty 使用 `wait` 实现等待新的数据的盗用，这是为了减少 CPU 的消耗。
- No busy waiting or polling 应采用条件变量等待，减少 CPU 的浪费。

核心组成

```cpp
template class ThreadSafeQueue { 
private: 
	std::queue queue_; 
	mutable std::mutex mutex_; 
	std::condition_variable condition_; 
public: 
	void push(const T& item); 
	T pop(); bool empty() const; 
	size_t size() const; 
};
```

### Summary 
Essential Guidelines: 

1. Always use predicates: Protects against spurious wakeups 
2. Use unique_lock: Required for condition variable flexibility 
3. Modify state before notify: Ensure consistency 
4. Keep critical sections short: Minimize lock contention 
5. Choose notify method wisely: `notify_one()` vs` notify_all()`


Common Patterns: 

- Producer-Consumer queues 
- Event signaling 
- Worker thread pools 
- Barrier synchronization
 
Remember: Condition variables are more efficient than polling for event-driven synchronization!

## Parallel Alogrithms 
### Parallelizing the Standard Library Algorithms 
C++ 17 向标准库中加入了并行算法函数，且引入了多个函数重载，例如：

```cpp
#include <execution>
std::vector<int> my_data = {3,1,4,1,5,9,2,6};
std::sort(my_data.begin(), my_data.end());
std::sort(std::execution::par,my_data.begin(),my_data.end())
```

并行函数与原来的串行函数相比增加了一个新的参数 `std::execution::par` ，用于向标准库示意，准许其调用多线程按并行算法的形式执行，不过这只是一种许可，并不是强制命令。

并行执行方式会改变算法对复杂度的要求，比普通串行算法函数对复杂度的要求略微宽松。

### Execution Policies 
C++ 提供了四种执行策略：

```cpp
// 1. Sequential (same as no policy) 
std::execution::sequenced_policy // std::execution::seq 
// 2. Parallel 
std::execution::parallel_policy // std::execution::par 
// 3. Parallel + Vectorized 
std::execution::parallel_unsequenced_policy // std::execution::par_unseq 
// 4. Unsequenced (C++20) 
std::execution::unsequenced_policy // std::execution::unseq
```
std::execution::sequenced_policy: 

- Same as traditional serial execution 
- Single thread, sequential order 
 
std::execution::parallel_policy:  

- Multiple threads allowed 
- Operations may be interleaved 
- No vectorization guarantees

std::execution::parallel_unsequenced_policy: 

- Multiple threads + vectorization 
- Operations may execute in any order 
- Best performance potential  
- Requires thread-safe operations