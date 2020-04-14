# Golang select

## 介绍

Go 语言中的 **select** 关键字也能够让 Goroutine 同时等待多个 Channel 的可读或者可写，在多个文件或者 Channel 发生状态改变之前，select 会一直阻塞当前线程或者 Goroutine。

select 是一种与 switch 相似的控制结构，与 switch 不同的是，select 中虽然也有多个 case，但是这些 case 中的表达式**必须都是 Channel 的收发操作** 。

## 示例

    package main

    import (
        "fmt"
        "time"
    )

    func test(consumer1, consumer2,consumer3 chan int,startTime time.Time) {
        fmt.Println("Blocking on read...")
        select {
            case <- consumer1:

                fmt.Printf("Unblocked %v later.\n", time.Since(startTime))

            case <- consumer2:

                fmt.Printf("ch2 case...")
            case <- consumer3:

                fmt.Printf("ch3 case...")
            default:

                fmt.Printf("default go...")
        }
    }

    func main() {
        startTime := time.Now()
        consumer1 := make(chan int)
        consumer2 := make(chan int)
        consumer3 := make(chan int)

        go func() {

            time.Sleep(4*time.Second)
            close(consumer1)
        }()

        go func() {

            time.Sleep(3*time.Second)
            consumer2 <- 3
        }()

        go func() {

            time.Sleep(3*time.Second)
            consumer3 <- 5
        }()
        
        test(consumer1, consumer2,consumer3,startTime)
    }

输出：

    Blocking on read...
    default go...

运行上述代码，由于当前时间还未到3s。所以，目前程序会走default的case。

修改代码，将default注释:输出：

    Blocking on read...
    ch3 case...

这时，select语句会阻塞，直到监测到一个可以执行的IO操作为止。这里，先会执行完睡眠3s的gorountine,此时两个channel(consumer2,consumer3)都满足条件，这时系统会随机选择一个case继续操作。

## 总结

通过上面示例，我们发现在 Go 语言中使用 select 控制结构时，会遇到两个有趣的现象：

- 1. select 能在 Channel 上进行非阻塞的收发操作(**有default操作时** 直接出发default)；
- 2. select 能在 Channel 上进行阻塞的收发操作(**无default操作时** 阻塞等待channel消息)；
- 3. select 在遇到多个 Channel 同时响应时会随机挑选 case 执行；
