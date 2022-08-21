## 为什么要用context

* context主要用来在 goroutine 之间传递上下文信息，包括：取消信号、超时时间、截止时间、k-v 等。
* 在 Go 语言编写的服务器程序中，服务器通常要为每个 HTTP 请求创建一个 goroutine 以并发地处理业务。同时，这个 goroutine 也可能会创建更多的 goroutine 来访问数据库或者 RPC 服务。当这个请求超时或者被终止的时候，需要优雅地退出所有衍生的 goroutine，并释放资源。因此，我们需要一种机制来通知衍生 goroutine 请求已被取消。
* 随着context 包的引入，标准库中很多接口因此加上了 context 参数，例如database/sql 包。context 几乎成为了并发控制和超时控制的标准做法。

## context

Context 包提供上下文机制在 goroutine 之间传递 deadline、取消信号（cancellation signals）或者其他请求相关的信息。使用方法是：

* 首先，服务器程序为每个接受的请求创建一个 Context 实例（称为根 context，通过 `context.Background()`方法创建）；
* 之后的 goroutine 接受根 context 的一个派生 Context 对象。比如通过调用根 context 的 `WithCancel` 方法，创建子 context；
* goroutine 通过 `context.Done()` 方法监听取消信号。`func Done() <-chan struct{}` 是一个通信操作，会阻塞 goroutine，直到收到取消信号接触阻塞。（可以借助 select 语句，如果收到取消信号，就退出 goroutine；否则，默认子句是继续执行 goroutine）；
* 当一个 `Context` 被取消（比如执行了 `cancelFunc()`），那么该 `context`派生出来的`context` 也会被取消。

## context类型

```go
type Context interface {
    Done() <-chan struct{}
    Deadline() (deadline time.Time, ok bool)
    Err() error
    Value(key interface{}) interface{}
}
```

`Done() <-chan struct{}`

* Done 方法返回一个 channel，阻塞当前运行的代码，直到以下条件之一发生时，channel 才会被关闭，进而解除阻塞：
* WithCancel 创建的 context，cancelFunc 被调用。该 context 以及派生子 context 的 Done channel 都会收到取消信号;
* WithDeadline 创建的 context，deadline 到期。
* WithTimeout 创建的 context，timeout 到期
* Done 要配合 select 语句使用:

```go
// DoSomething 生产数据并发送给通道 out
// 但如果 DoSomething 返回一个则退出函数，
// 或者 ctx.Done 被关闭时也会退出函数.

func Stream(ctx context.Context, out chan<- Value) error {
    for {
        v, err := DoSomething(ctx)
        if err != nil {
            return err
        }
        select {
        case <-ctx.Done():
            return ctx.Err()
        case out <- v:
        }
    }
}
```

`Deadline() (deadline time.Time, ok bool)`

* WithDeadline 方法会给 context 设置 deadline，到期自动发送取消信号。调用 Deadline() 返回 deadline 的值。如果没设置，ok 返回 false。该方法可用于确定当前时间是否临近 deadline。

`Err() error`

* 如果 Done 的 channel 被关闭了, Err 函数会返回一个 error，说明错误原因:
* 如果 channel 是因为被取消而关闭，打印 canceled；
* 如果 channel 是因为 deadline 到时了，打印 deadline exceeded。
* 重复调用，返回相同值。

`Value(key interface{}) interface{}`

* 返回由 WithValue 关联到 context 的值。

## 使用

#### 创建根context

* context.Background()
* context.TODO()

根 context 不会被 cancel。这两个方法只能用在最外层代码中，比如 main 函数里。一般使用 Background() 方法创建根 context。TODO() 用于当前不确定使用何种 context，留待以后调整。

#### 派生根context

一个 Context 被 cancel，那么它的派生 context 都会收到取消信号（表现为 context.Done() 返回的 channel 收到值）。有四种方法派生 context ：

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
func WithDeadline(parent Context, d time.Time) (Context, CancelFunc)
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
func WithValue(parent Context, key, val interface{}) Context
```

`WithCancel`

* 最常用的派生 context 方法。该方法接受一个父 context。父 context 可以是一个 background context 或其他 context。返回的 cancelFunc，如果被调用，会导致 Done channel 关闭。因此，绝不要把 cancelFunc 传给其他方法。

`WithDeadline`

* 该方法会创建一个带有 deadline 的 context。当 deadline 到期后，该 context 以及该 context 的可能子 context 会受到 cancel 通知。另外，如果 deadline 前调用 cancelFunc 则会提前发送取消通知。

`WithTimeout`

* 与 WithDeadline 类似。创建一个带有超时机制的 context。

`WithValue`

* WithValue 方法创建一个携带信息的 context，可以是 user 信息、认证 token等。该 context 与其派生的子 context 都会携带这些信息。
* WithValue 方法的第二个参数是信息的唯一 key。该 key 类型不应对外暴露，为了避免与其他包可能的 key 类型冲突。所以使用 WithValue 也应像下面例子的方式间接调用 WithValue。
* WithValue 方法的第三个参数即是真正要存到 context 中的值。

## 例子

```go
func sleepRandom_1(stopChan chan struct{}) {
    i := 0
    for {
        time.Sleep(1 * time.Second)
        fmt.Printf("This is sleep Random 1: %d\n", i)
        i++
        if i == 5 {
            fmt.Println("cancel sleep random 1")
            stopChan <- struct{}{}
            break
        }
    }
}
func sleepRandom_2(ctx context.Context) {
    i := 0
    for {
        time.Sleep(1 * time.Second)
        fmt.Printf("This is sleep Random 2: %d\n", i)
        i++
        select {
        case <-ctx.Done():
            fmt.Printf("Why? %s\n", ctx.Err())
            fmt.Println("cancel sleep random 2")
            return
        default:
        }
    }
}
func main() {
    ctxParent, cancelParent := context.WithCancel(context.Background())
    ctxChild, _ := context.WithCancel(ctxParent)
    stopChan := make(chan struct{})
    go sleepRandom_1(stopChan)
    go sleepRandom_2(ctxChild)
    select {
        case <-stopChan:
            fmt.Println("stopChan received")
        default:
            fmt.Println("chan received")
    }
    cancelParent()
    for {
        time.Sleep(1 * time.Second)
        fmt.Println("Continue...")
    }
}
```
