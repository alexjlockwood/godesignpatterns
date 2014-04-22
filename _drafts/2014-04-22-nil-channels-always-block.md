---
layout: post
title: 'nil Channels Always Block'
---
Another useful idiom in Go makes use of the fact that receiving and
sending on `nil` channels will always block:

```go
// Create an uninitialized (nil) channel.
var ch chan struct{}{}

// Receiving on a nil channel blocks forever.
<-ch

// Sending on a nil channel will also block forever.
ch <- struct{}{}
```

This might not seem very useful at first, and may have even
introduced bugs in your programs in the past (for example,
if you forget to initialize your channels with `make` before
you use them). However, this property can be leveraged in
a couple of clever ways, especially when you need to dynamically
disable a case in a select statement: 

```go
if shouldDisable {
	ch1 = nil
} else {
	ch1 = someOtherNonNilChan
}

select {
case <-ch1:
	// This case will only be considered if ch1 is not nil.
case <-ch2:
	// This case will always be considered (assuming ch2 is non-nil).
}
```

The code example above illustrates the fact that <b>when a nil
channel is a case in a select statement, it is effectively ignored.</b>
If the `shouldDisable` condition is `true`, we prevent `ch1` channel
from being considered as part of the select statement by setting
it to `nil`.

This property can also be used to avoid spin loops—for example,
if you need to wait on multiple channels to close:

```go
// WARNING! Spin loop might occur!
var isClosed1, isClosed2 bool
for !isClosed1 || !isClosed2 {
	select {
	case _, ok := <-ch1:
		if !ok {
			isClosed1 = true
		}
	case _, ok := <-ch2:
		if !ok {
			isClosed2 = true
		}
	}
}
```

The code above will cause a very subtle bug in your program
if one of the two channels is closed.
Since a closed channel will never block, if `ch1` is closed, `ch1` will
<i>always</i> be ready to receive on subsequent iterations
through the loop. As a result, the program will enter an
infinite spin loop, and `case <-ch2:` may or may not be
given a chance to run later on.

To solve this problem, we can leverage the property that `nil`
channels always block:

```go
// Safely disable channels after they are closed. 
for ch1 != nil || ch2 != nil {
	select {
	case _, ok := <-ch1:
		if !ok {
			ch1 = nil
		}
	case _, ok := <-ch2:
		if !ok {
			ch2 = nil
		}
	}
}
```

Special thanks to [Dave Cheney](http://dave.cheney.net/2013/04/30/curious-channels)
for motivating the topic of this blog post!