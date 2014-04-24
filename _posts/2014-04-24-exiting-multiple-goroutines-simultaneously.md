---
layout: post
title: 'Exiting Multiple Goroutines Simultaneously'
---
The ability to close channels (using Go's builtin `close()` function)
forms the basis of a useful pattern in Go:
<i>using a single channel to exit multiple goroutines</i>. 

Let's say we have launched a variable number of goroutines
in the background as follows:

```go
shutdown := make(chan struct{})

for i := 0; i < n; i++ {
	i := i
	go func() {
		select {
		case <-shutdown:
			done <- i
		}
	}()
}
```

<!--more-->

Once started, each goroutine will wait for a signal
from a single `shutdown` channel before exiting.
We would like to signal all `n` goroutines to be shut down
at once in the most elegant way possible. To do so, we can
make use of the
fact that receiving on a closed channel will <i>never</i> block.
Simply by closing the `shutdown` channel, we can
signal all goroutines to "unblock," causing them to
immediately exit. A complete example is given below:

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
	const n = 5

	// Start up the goroutines...
	for i := 0; i < n; i++ {
		i := i
		go func() {
			select {
			case <-shutdown:
				done <- i
			}
		}()
	}

	time.Sleep(2 * time.Second)

	// Close the channel. All goroutines will immediately "unblock".
	close(shutdown)

	for i := 0; i < n; i++ {
		fmt.Println("routine", <-done, "has exited!")
	}
}
{% endhighlight %}
</div>

You can also view and run the above example on the [Go Playground](http://play.golang.org/p/O7lMdIN0JZ).

Let me know if this example was helpful in the comments below,
and don't forget to +1 this post if you found it helpful!