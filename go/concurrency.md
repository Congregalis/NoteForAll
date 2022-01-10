## 创建新线程

`go f(x, y, z)` 即可以创建新的 GO 程（即 go 运行时管理的线程），f 函数的执行会发生在新的 Go 程中。

## 同步

可以使用**信道**进行同步

首先在使用信道之前必须先创建它
`ch := make(chan int)`

基础用法：
```go
ch <- v    // 将 v 发送至信道 ch。
v := <-ch  // 从 ch 接收值并赋予 v。
```

不带缓冲的信道实现同步：

如以下代码段，先开启 f 线程，然后 f 线程会阻塞直到能从信道接收值
```go
func f(c chan int) {
    
    fmt.Println("get data: ", <-c)
}

func main() {
    c := make(chan int)
    x := 0
    
    // c <- x 若把这行代码放在这里，就会导致主线程在这行阻塞住，也不会再执行下面的新开线程操作
    go f(c)
    x += 1
    c <- x

    fmt.Println("Finish")
}
```

带缓冲的信道实现同步：

仅当信道的缓冲区填满后，向其发送数据时才会阻塞。当缓冲区为空时，接受方会阻塞。

如下例子中若取消注释1，则会因为缓冲区满了阻塞，取消注释2，则会因为缓存区空了阻塞。
```go
package main

import "fmt"

func main() {
    ch := make(chan int, 2)
    ch <- 1
    ch <- 2
    // 1. ch <- 3
    fmt.Println(<-ch)
    fmt.Println(<-ch)
    // 2. fmt.Println(<-ch)
}
```

为了在尝试发送或者接收时不发生阻塞，可使用 select 的 default 分支：
```go
select {
    case i := <-c:
        // 使用 i，若 c 中没有数据这里可能会阻塞，那就会执行 default 分支
    default:
        // 从 c 中接收会阻塞时执行
}
```

### 练习：使用信道同步来判断等价二叉查找树
原题在[练习：等价二叉查找树](https://tour.go-zh.org/concurrency/7)

```go
package main

import "fmt"
import "golang.org/x/tour/tree"

// Walk 步进 tree t 将所有的值从 tree 发送到 channel ch。
func Walk(t *tree.Tree, ch chan int) {
	if t != nil {
		Walk(t.Left, ch)
		ch <- t.Value;
		Walk(t.Right, ch)
	}
	
}

// Same 检测树 t1 和 t2 是否含有相同的值。
	func Same(t1, t2 *tree.Tree) bool {
	ch1 := make(chan int)
	ch2 := make(chan int)
	
	go Walk(t1, ch1)
	go Walk(t2, ch2)
	
	for i := 0; i < 10; i++ {
		a := <- ch1
		b := <- ch2
		if a != b {
			return false
		}
	}
	
	return true
}

func main() {
	ch := make(chan int)
	go Walk(tree.New(1), ch)
	
	fmt.Println("test Walk:")
	for i := 0; i < 10; i++ {
		fmt.Print(<-ch, " ")
	}
	
	fmt.Println()
	fmt.Println("test Same:")
	fmt.Println(Same(tree.New(1), tree.New(1)))

}
```

## 互斥

互斥可以使用 Go 标准库中提供的互斥锁类型 `sync.Mutex` 来实现

调用 `Lock` 方法则加锁， `UnLock` 方法则解锁


## 综合练习：Web爬虫
原题在[练习：Web 爬虫](https://tour.go-zh.org/concurrency/10)

我的做法如下：
```go
package main

import (
	"fmt"
	"sync"
)

type Fetcher interface {
	// Fetch 返回 URL 的 body 内容，并且将在这个页面上找到的 URL 放到一个 slice 中。
	Fetch(url string) (body string, urls []string, err error)
}

var urlMap URLMap

// Crawl 使用 fetcher 从某个 URL 开始递归的爬取页面，直到达到最大深度。
func Crawl(url string, depth int, fetcher Fetcher) {
	// TODO: 并行的抓取 URL。
	// TODO: 不重复抓取页面。
	// 下面并没有实现上面两种情况：
	if depth <= 0 {
		return
	}

	// 判重
	urlMap.mutex.Lock()
	_, exist := urlMap.v[url]
	if exist {
		urlMap.mutex.Unlock()
		return
	}
	urlMap.mutex.Unlock()

	urlMap.mutex.Lock()
	urlMap.v[url]++
	urlMap.mutex.Unlock()

	body, urls, err := fetcher.Fetch(url)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Printf("found: %s %q\n", url, body)

	ch := make(chan int) // 使用信道控制，当所有线程都执行完了才返回
	for _, u := range urls {
		go func(url string) {
			Crawl(url, depth-1, fetcher)
			ch <- 1
		}(u)
	}

	for range urls {
		<-ch
	}

	return
}

func main() {
	urlMap = URLMap{v: make(map[string]int)}
	Crawl("https://golang.org/", 4, fetcher)
}

// fakeFetcher 是返回若干结果的 Fetcher。
type fakeFetcher map[string]*fakeResult

type URLMap struct {
	v map[string]int
	mutex sync.Mutex
}

type fakeResult struct {
	body string
	urls []string
}

func (f fakeFetcher) Fetch(url string) (string, []string, error) {
	if res, ok := f[url]; ok {
		return res.body, res.urls, nil
	}
	return "", nil, fmt.Errorf("not found: %s", url)
}

// fetcher 是填充后的 fakeFetcher。
var fetcher = fakeFetcher{
	"https://golang.org/": &fakeResult{
		"The Go Programming Language",
		[]string{
			"https://golang.org/pkg/",
			"https://golang.org/cmd/",
		},
	},
	"https://golang.org/pkg/": &fakeResult{
		"Packages",
		[]string{
			"https://golang.org/",
			"https://golang.org/cmd/",
			"https://golang.org/pkg/fmt/",
			"https://golang.org/pkg/os/",
		},
	},
	"https://golang.org/pkg/fmt/": &fakeResult{
		"Package fmt",
		[]string{
			"https://golang.org/",
			"https://golang.org/pkg/",
		},
	},
	"https://golang.org/pkg/os/": &fakeResult{
		"Package os",
		[]string{
			"https://golang.org/",
			"https://golang.org/pkg/",
		},
	},
}
```