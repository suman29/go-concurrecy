# Concurrency In GO

Go is designed with concurrency in mind and allows us to build complex concurrent pipelines. Go’s approach to concurrency can be best phrased by

> Do not communicate by sharing memory; instead, share memory by communicating.</i>

In any other mainstream programming language, when concurrent threads need to share data in order to communicate, a lock is applied to the piece of memory (this is also an overhead on programmer to select right locking mechanism). This typically causes issues like race conditions / deadlock, memory management etc. Instead of applying a lock on the shared memory (critical section), Go allows you to communicate (or send) the value from one routine to another via channel. The default behavior is that both the routines sending the data and the one receiving the data will wait till the value reaches its destination. The “waiting” of the routines forces proper synchronization between routines when data is being exchanged.

> A goroutine is an independently executing function launched by a go statement (managed by the Go runtime). It has its own call stack, which grows and shrinks as required. It's not a thread. There might be only one thread in a program with thousands of goroutines. Goroutines are multiplexed dynamically onto threads as needed to keep all the goroutines running.

> Goroutines run in the same address space, so access to shared memory must be synchronized. It is lightweight, costing little more than the allocation of stack space. And the stacks start small, so they are cheap, and grow by allocating (and freeing) heap storage as required.

> Goroutines are multiplexed onto multiple OS threads so if one should block, such as while waiting for I/O, others continue to run. Their design hides many of the complexities of thread creation and management.

Prefix a function or method call with the go keyword to run the call in a new goroutine. When the call completes, the goroutine exits, silently.

One way to think about this model is to consider a typical single-threaded program running on one CPU. It has no need for synchronization primitives. Now run another such instance; it too needs no synchronization. Now let those two communicate; if the communication is the synchronizer, there's still no need for other synchronization. Unix pipelines, for example, fit this model perfectly. Although Go's approach to concurrency originates in Hoare's Communicating Sequential Processes (CSP), it can also be seen as a type-safe generalization of Unix pipes.

A goroutine is a function that is capable of running concurrently with other functions. To create a goroutine we use the keyword ``` go ``` followed by a function invocation:

```
//greetings.go

package main
import (
         "fmt"
         "time"
      )
func Greet(msg string){
   for i:=0; ; i++ {
      fmt.Println(msg, i)
      time.Sleep(time.Second)
   }
}
func main() {
   go Greet("Hello,Go Dev!!")
   time.Sleep(2*time.Second)
}
```
```
// Output:
Hello,Go Dev!! 0
Hello,Go Dev!! 1
```
This program consists of two goroutines. The first goroutine is implicit and is the main function itself. The second goroutine is created when we call ```go Greet("Hello,Go Dev!!")```. Normally when we invoke a function our program will execute all the statements in a function and then return to the next line following the invocation. With a goroutine we return immediately to the next line and don't wait for the function to complete. This is why the call to the ```time.Sleep(2*time.Second)``` function has been included to wait for 2 seconds; without it the program would exit before being given the opportunity to print all the numbers.

## Channel
