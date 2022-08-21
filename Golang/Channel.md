```go
type hchan struct {
	qcount   uint           // total data in the queue
	dataqsiz uint           // size of the circular queue
	buf      unsafe.Pointer // points to an array of dataqsiz elements
	elemsize uint16
	closed   uint32
	elemtype *_type // element type
	sendx    uint   // send index
	recvx    uint   // receive index
	recvq    waitq  // list of recv waiters
	sendq    waitq  // list of send waiters

	// lock protects all fields in hchan, as well as several
	// fields in sudogs blocked on this channel.
	//
	// Do not change another G's status while holding this lock
	// (in particular, do not ready a G), as this can deadlock
	// with stack shrinking.
	lock mutex
}
```

* `buf`是有缓冲的channel所特有的结构，用来存储缓存数据。是个循环链表
* `sendx`和`recvx`用于记录`buf`这个循环链表中的发送或者接收的index
* `lock`是个互斥锁。
* `recvq`和`sendq`分别是接收(`<-channel`)或者发送(`channel <- xxx`)的`goroutine`抽象出来的结构体的队列。是个双向链表

## channel如何创建

创建channel实际上就是在内存（heap）中实例化了一个`hchan`的结构体，并返回一个ch指针，我们使用过程中channel在函数之间的传递都是用的这个指针，这就是为什么函数传递中无需使用channel的指针，而直接用channel就行了，因为channel本身就是一个指针。

## channel中buf是如何实现的
