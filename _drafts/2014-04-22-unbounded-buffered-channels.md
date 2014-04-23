---
layout: post
title: 'Implementing an Unbounded, Buffered Channel'
---
In Go, channels can either be unbuffered or buffered, and those that are buffered
cannot grow in size. Through some clever use of these channels, however, we can
easily implement such a structure ourselves. This blog post will walk through
the implementation of an "unbounded buffered channel".

Our data structure will The example below describes how one might go about
implementing a buffered channel with unlimited size.

```go
package main

import (
	"container/list"
	"fmt"
)

// Change 'int' to something else if you wish to use a different type.
type T int

var vals = []T{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}

func main() {
	in, out := make(chan T), make(chan T)
	go handleBuffer(in, out)

	// Send values through the 'in' channel.
	for i := range vals {
		fmt.Printf("Sending value: %v\n", i)
		in <- T(i)
	}

	// Close the 'in' channel. New values are no longer
	// allowed to be sent through 'in'.
	close(in)

	// Receive values from the 'out' channel.
	for i := range out {
		fmt.Printf("Received value: %v\n", i)
	}
}

func handleBuffer(in <-chan T, out chan<- T) {
	defer close(out)

	// This list will store all values received from 'in'.
	// All values should eventually be sent back through 'out',
	// even if the 'in' channel is suddenly closed.
	buffer := list.New()

	for {
		// Make sure that the list always has values before
		// we select over the two channels.
		if buffer.Len() == 0 {
			v, ok := <-in
			if !ok {
				// 'in' has been closed. Flush all values
				// in the buffer and return.
				flush(buffer, out)
				return
			}
			buffer.PushBack(v)
		}

		select {
		case v, ok := <-in:
			if !ok {
				// 'in' has been closed. Flush all values
				// in the buffer and return.
				flush(buffer, out)
				return
			}
			buffer.PushBack(v)
		case out <- (buffer.Front().Value).(T):
			buffer.Remove(buffer.Front())
		}
	}
}

// Blocks until all values in the buffer have been sent through
// the 'out' channel.
func flush(buffer *list.List, out chan<- T) {
	for e := buffer.Front(); e != nil; e = e.Next() {
		out <- (e.Value).(T)
	}
}
```

You can also view the above example on the [Go Playground](http://play.golang.org/p/RxHn6olGs1).