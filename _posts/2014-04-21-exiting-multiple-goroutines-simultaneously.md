---
layout: post
title: 'Exiting Multiple Goroutines Simultaneously'
---
The ability to close channels (using Go's builtin `close()` function) forms the basis of a useful pattern in Go: <i>using a single channel to exit multiple goroutines</i>. 

This pattern makes use of the fact that performing a receive operation on a closed channel will never block. A short example is given below. You can also view it on the [Go Playground](http://play.golang.org/p/BFpSmmcnsE).

<div class="scrollable">
{% highlight go linenos=table %}
package main

import (
	"fmt"
	"time"
)

var (
	shutdown = make(chan struct{})
	done     = make(chan int)
)

func main() {
	const n = 3

	// Start up the goroutines and wait...
	for i := 0; i < n; i++ {
		go routine(i)
	}

	time.Sleep(2 * time.Second)

	// Close the channel. All goroutines will immediately "unblock".
	close(shutdown)

	for i := 0; i < n; i++ {
		fmt.Println("routine", <-done, "is done!")
	}
}

func routine(i int) {
	select {
	case <-shutdown:
		done <- i
	}
}
{% endhighlight %}
</div>
