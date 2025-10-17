#Go #Golang #并发

# 1. GoRoutines

个人认为，go能够成为主流编程语言，同时在并发性能上这么好，就是`GoRoutines`的功劳。它能够在占用较少的资源下，依然能够稳定的处理海量的并发，这个就是王炸。

> [!danger]
> 传统的线程模型对比
> 1. 线程创建销毁成本高，每个线程可能需要1-8M左右的栈空间
> 2. 上下文切换开销大，需要在内核态进行
> 3. 调度器相对简单，主要依赖OS的抢占式调度

首先，在go语言中，每个并发的执行单元叫做`goroutine`。在一个程序启动的时候，main函数可以认为在`goroutine`中执行，我们称之为『main goroutine』。 新的goroutine会用go语句来创建。
语法：
```go
go 方法/函数的调用
```
这个就类似Java中的tread的start，会起一个线程去执行；只不过在go中不是线程，而是更小的执行单元。在main函数执行完毕返回后，所有的goroutine都会被打断，这个跟Java也有点像，主程序退出后，线程也随着销毁。

## 1.1 goroutine用法

```go
func main() {
    // 启动goroutine
    go sayHello("Alice")
    go sayHello("Bob")
    
    // 等待一段时间让goroutine执行完
    time.Sleep(time.Second)
}

func sayHello(name string) {
    fmt.Printf("Hello, %s!\n", name)
}
```

## 1.2 goroutine vs 传统线程

| 特性     | 传统线程<img width="300">    | Goroutine <img width="300"> |
| ------ | ------------------------ | --------------------------- |
| 初始化栈大小 | 2kb                      | 1- 8M                       |
| 创建销毁开销 | 极小                       | 较大                          |
| 调度方式   | 协作式 + 抢占式                | 抢占式                         |
| 数量限制   | 百万级                      | 千级                          |
| 上下文切换  | 用户态，在运行时go自行管理，避免昂贵的系统调用 | 内核态                         |

==核心的原理== （GMP模型）
- G - goroutine   代表封装了需要执行的函数、参数和栈信息
- M-Machine   代表一个操作系统的线程，实际干活的人，由操作系统内核进行调度，所有的Go代码最终肯定在M上执行
- P-Processor  代表一个逻辑处理器，可以把它理解为一个go的调度器

==工作流程==：
1. **G的创建**：通过`go`启动一个新的任务，Go运行时创建一个G，并将它放入一个全局的Goroutine队列或者本地Goroutine队列中
2. **P的作用**：Go运行时会为每个可用的CPU核心创建一个P。P负责管理一个Goroutine的本地队列，并为该队列中的Goroutine提供执行环境
3. **M的执行**：一个M会从一个P中获取Goroutine，然后执行其代码
4. **调度的循环**
	1. M从其关联的P的本地队列中去除一个G来执行
	2. 如果本地队列唯恐，M会尝试从全局队列或其他M的本地队列中『窃取』Goroutine来执行，大大提升了CPU的利用率
	3. 当一个Goroutine因为某种原因阻塞了，go运行时会暂停这个goroutine，将其从M上移除，并让M去执行下一个Goroutine

## 1.3 Goroutine生命周期

```go
func main() {
    var wg sync.WaitGroup
    
    for i := 0; i < 5; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            fmt.Printf("Goroutine %d is working\n", id)
            time.Sleep(time.Millisecond * 100)
        }(i)
    }
    
    wg.Wait()
    fmt.Println("All goroutines completed")
}
```


# 2. Go并发模型

>   CSP模型： 不要通过共享内存来通信，而是通过通信来共享内存

Java中就是通过共享内存来进行通信的，多个线程通常会访问同一块内存，这样实现『共享内存来通信』，带来的问题就是数据竞争，其实就是锁的竞争，复杂度可能会高
而Go中是通过通信来共享内存的，工作方式就是通过channel来传递数据，这样就不会有锁的问题，在传递的过程中天然的就完成了数据的共享

```go
// ❗️❗️❗️传统的共享内存方式（不推荐）
var counter int
var mutex sync.Mutex

func increment() {
    mutex.Lock()
    counter++
    mutex.Unlock()
}

// ✅ ✅ ✅Go推荐的通道方式
func worker(ch chan int) {
    for n := range ch {
        fmt.Printf("Processing: %d\n", n)
    }
}
```

# 3. Channel ： Goroutine间通信

在goroutine中间进行通信的方式就是**channel**
使用内置函数`make`创建一个channel
```go
ch := make(chan int)  // 创建一个类型为chan int的channel
```

上面创建的是一个没有缓存的channel，它的特点是发送和接收都是阻塞的，直到另一端就绪

```go
func main() {
    ch := make(chan string)
    go func() {
        time.Sleep(time.Second)
        ch <- "Hello from goroutine"    // 同样也会阻塞，知道接收数据的就绪
    }()
    msg := <-ch  // 阻塞直到接收到数据
    fmt.Println(msg)
}
```

- 发送者在`ch <- data`处会阻塞，知道有接收者准备`<-ch`
- 接受者在`<-ch`处阻塞，直到有发送者准备好`ch <- data`
- 主要的特点：
	- 同步。 无缓冲就像一个『碰头点』。用于确保两个Goroutine在特定时刻都已经就绪

## 3.1 有缓冲的channel

有缓冲的channel的容量大于0. 他们就像一个队列，只有满了才会阻塞

```go
func main() {
    ch := make(chan int, 3)  // 缓冲区大小为3
    
    // 发送数据（不会阻塞直到缓冲区满）
    ch <- 1
    ch <- 2
    ch <- 3
    
    fmt.Println(<-ch)  // 1
    fmt.Println(<-ch)  // 2
    fmt.Println(<-ch)  // 3
}
```

- 只要channel中还有容量，就不会发生阻塞
- 发送者只有在channel已经满时才会阻塞
- 接收者只有在channel为空时才会阻塞

## 3.2 channel的方向性
在函数或者方法的参数中，可以指定channel的方向性，不止使用`chan int` 双向的channel，因为有些方法可能需要发送消息就行了或者接收信息就行了

```go
// 只发送通道
func sender(ch chan<- int) {  // 专门发送的通道
    ch <- 42
}

// 只接收通道
func receiver(ch <-chan int) {  // 专门接收的通道
    value := <-ch
    fmt.Println(value)
}

func main() {
    ch := make(chan int)
    go sender(ch)
    go receiver(ch)
    time.Sleep(time.Second)
}
```

<span class='r'>需要注意：</span>
在函数的外部传递的时候，只能用双向channel来创建和传递；只有作为函数的参数时候，才可以限定是发送还是接收

## 3.3 channel关闭和range

```go
func producer(ch chan<- int) {
    defer close(ch)
    for i := 1; i <= 5; i++ {
        ch <- i
        time.Sleep(time.Millisecond * 500)
    }
}
func consumer(ch <-chan int) {
    // range会自动检测通道是否关闭
    for value := range ch {
        fmt.Printf("Received: %d\n", value)
    }
}
func main() {
    ch := make(chan int)
    go producer(ch)
    go consumer(ch)
    time.Sleep(time.Second * 5)
}
```

- 关闭是一个不可逆、永久性的操作，一旦channel被关闭，将不能重新开启
- channel的关闭动作一般由发送者来操作
- 关闭后的channel禁止再发送消息，如果发送的话，会导致程序panic
- 也可以通过 `if v,ok := range ch; ok==false` 这种方式继续处理

# 4. select：多路复用

`select`是go语言中的一个控制结构，专门用于处理**多个channel的并发操作**。它允许我们在多个发送或者接收操作中进行**非阻塞**选择

==**原理**：==
- select会同时监控所有`case`语句中channel。当其中任意一个channel准备好通信是，`select`就会执行对应的`case`代码块
- 如果多个`case`都准备好了，`select`会随机选择一个执行

==**它解决了什么问题？**==
- 阻塞等待： 如果你用`v := <-ch` 接收消息，那么当这个程序阻塞这边。后面的`ch2`先有数据了，你却无法处理，这就造成程序阻塞，从而影响效率
- 复杂逻辑： 如果写很多`if-else`或者循环判断哪个channel可用，这不仅麻烦，同时容易出错

`select`解决的这些问题，能够让Go：
1. 高效地同时监听多个channel
2. 优雅的处理超时： 通过结合`time.After`实现
3. 实现非阻塞操作：通过`default`关键字来处理没有任何channel准备好的情况

## 4.1 select基本用法

```go hl:15-22
func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)
    
    go func() {
        time.Sleep(time.Second)
        ch1 <- "from ch1"
    }()
    
    go func() {
        time.Sleep(time.Second * 2)
        ch2 <- "from ch2"
    }()
    
    select {
    case msg1 := <-ch1:
        fmt.Println(msg1)
    case msg2 := <-ch2:
        fmt.Println(msg2)
    case <-time.After(time.Second * 3):  // 这个超时过后处理
        fmt.Println("timeout")
    }
}
```


## 4.2 非阻塞操作

在`select`语句中，使用`default`去实现

```go
func main() {
    ch := make(chan int, 1)
    
    select {
    case ch <- 42:
        fmt.Println("Sent successfully")
    default:
        fmt.Println("Channel is full")
    }
    
    select {
    case value := <-ch:
        fmt.Printf("Received: %d\n", value)
    default:
        fmt.Println("No data available")
    }
}
```

## 4.3 扇形模式

```go
// 扇出：一个输入分发到多个输出
func fanOut(input <-chan int, workers int) []<-chan int {
    outputs := make([]<-chan int, workers)
    
    for i := 0; i < workers; i++ {
        output := make(chan int)
        outputs[i] = output
        
        go func(out chan<- int) {
            defer close(out)
            for data := range input {
                out <- process(data)
            }
        }(output)
    }
    
    return outputs
}

// 扇入：多个输入合并到一个输出
func fanIn(inputs ...<-chan int) <-chan int {
    output := make(chan int)
    var wg sync.WaitGroup
    
    for _, input := range inputs {
        wg.Add(1)
        go func(in <-chan int) {
            defer wg.Done()
            for data := range in {
                output <- data
            }
        }(input)
    }
    
    go func() {
        wg.Wait()
        close(output)
    }()
    
    return output
}

func process(data int) int {
    time.Sleep(time.Millisecond * 100)
    return data * 2
}
```

# 5. 同步锁

## 5.1 mutex 互斥锁

```go
type SafeCounter struct {
    mu    sync.Mutex
    count int
}

func (c *SafeCounter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count++
}

func (c *SafeCounter) Value() int {
    c.mu.Lock()
    defer c.mu.Unlock()  // 释放锁一般都是通过defer
    return c.count
}
```

其实跟Java有点类似，就是一个公共的变量，作为锁的逻辑

## 5.2 RWMutex 读写锁

```go
type SafeMap struct {
    mu   sync.RWMutex
    data map[string]int
}

func (sm *SafeMap) Get(key string) int {
    sm.mu.RLock()  // 读锁
    defer sm.mu.RUnlock()
    return sm.data[key]
}

func (sm *SafeMap) Set(key string, value int) {
    sm.mu.Lock()  // 写锁
    defer sm.mu.Unlock()
    sm.data[key] = value
}
```

## 5.3 Once  一次性执行

```go
var once sync.Once
var instance *Singleton

func GetInstance() *Singleton {
    once.Do(func() {
        instance = &Singleton{}
        // 初始化代码只执行一次
    })
    return instance
}
```

## 5.4 Cond  条件变量

```go
type Queue struct {
    mu    sync.Mutex
    cond  *sync.Cond
    items []interface{}
}

func NewQueue() *Queue {
    q := &Queue{}
    q.cond = sync.NewCond(&q.mu)
    return q
}

func (q *Queue) Put(item interface{}) {
    q.mu.Lock()
    defer q.mu.Unlock()
    
    q.items = append(q.items, item)
    q.cond.Signal()  // 通知等待的goroutine
}

func (q *Queue) Get() interface{} {
    q.mu.Lock()
    defer q.mu.Unlock()
    
    for len(q.items) == 0 {
        q.cond.Wait()  // 等待条件满足
    }
    
    item := q.items[0]
    q.items = q.items[1:]
    return item
}
```

# 6. 并发模式

## 6.1 Worker Pool模式

```go
func workerPool(jobs <-chan Job, results chan<- Result, numWorkers int) {
    var wg sync.WaitGroup
    
    for i := 0; i < numWorkers; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for job := range jobs {
                result := processJob(job)
                results <- result
            }
        }()
    }
    
    go func() {
        wg.Wait()
        close(results)
    }()
}

func main() {
    jobs := make(chan Job, 100)
    results := make(chan Result, 100)
    
    // 启动worker pool
    workerPool(jobs, results, 5)
    
    // 发送任务
    go func() {
        defer close(jobs)
        for i := 0; i < 100; i++ {
            jobs <- Job{ID: i}
        }
    }()
    
    // 收集结果
    for result := range results {
        fmt.Printf("Result: %+v\n", result)
    }
}
```

> 类似Java中的CompleteFuture

## 6.2 Pipline模式

多个chennel串联整个流程

```go
func pipeline() <-chan int {
    // 阶段1：生成数据
    numbers := make(chan int)
    go func() {
        defer close(numbers)
        for i := 1; i <= 10; i++ {
            numbers <- i
        }
    }()
    
    // 阶段2：处理数据
    squared := make(chan int)
    go func() {
        defer close(squared)
        for n := range numbers {
            squared <- n * n
        }
    }()
    
    // 阶段3：过滤数据
    filtered := make(chan int)
    go func() {
        defer close(filtered)
        for n := range squared {
            if n%2 == 0 {
                filtered <- n
            }
        }
    }()
    
    return filtered
}
```

## 6.3 超时和取消模式

```go
func doWorkWithTimeout(ctx context.Context, timeout time.Duration) error {
    ctx, cancel := context.WithTimeout(ctx, timeout)
    defer cancel()
    
    done := make(chan error, 1)
    
    go func() {
        // 模拟耗时工作
        time.Sleep(time.Second * 2)
        done <- nil
    }()
    
    select {
    case err := <-done:
        return err
    case <-ctx.Done():
        return ctx.Err()  // 超时或取消
    }
}
```

# 7. 最佳实践

1. 避免Goroutine泄露或者一致阻塞。channel必须有写和读都要有
2. 适当的并发数量
3. 使用`sync.Pool`复用对象




