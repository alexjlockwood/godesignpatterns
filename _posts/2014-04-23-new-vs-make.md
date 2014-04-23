---
layout: post
title: 'new() vs. make()'
---
This post will quickly describe the differences between the
builtin `new()` and `make()` functions in Go. 
It assumes that you have completed [A Tour of Go][6] 
and have consulted relevant sections of [Effective Go][7].

The builtin `new(T)` function
allocates "zeroed" storage for a new item of type
`T` and returns its address, a value of type `*T`.
In Go terminology, it returns a pointer to a newly
allocated zero value of type `T`. For example,
here are three different ways to create a pointer
`p` that points to a zeroed `bytes.Buffer`
value, each of which are equivalent:

```go
// Allocate enough memory to store a bytes.Buffer value
// and return a pointer to the value's address.
var buf bytes.Buffer
p := &buf

// Use a composite literal to perform allocation and
// return a pointer to the value's address.
p := &bytes.Buffer{}

// Use the new function to perform allocation, which will
// return a pointer to the value's address.
p := new(bytes.Buffer)
```

<!--more-->

The `make()` function, on the other hand, is a special built-in function
that is used to initialize slices, maps, and channels.
Note that `make()` can only be used to initialize
slices, maps, and channels, and that, unlike the `new()`
function, `make()` does not return a pointer.

Slices, maps, and channels can also be initialized using
a composite literal expressions, as well as with `make()`. Two
different (yet equivalent) ways to initialize a map `m`
which maps `string` keys to `bool` values are given
below as examples:

```go
// Using make() to initialize a map.
m := make(map[string]bool, 0)

// Using a composite literal to initialize a map.
m := map[string]bool{}
```

You can also initialize maps with initial data using a composite literal, as shown below:

```go
m := map[string]bool{
    "java": false,
    "go":   true,
}
```

#### Recommended Readings:

* The [Allocation with new][1] and [Allocation with make][2] sections in Effective Go
  (the [Constructors and composite literals][3] section is worth reading as well).
* The [Allocation][4] and [Making slices, maps, and channels][5] sections in the
  Go Programming Language Specification.

[1]: http://golang.org/doc/effective_go.html#allocation_new
[2]: http://golang.org/doc/effective_go.html#allocation_make
[3]: http://golang.org/doc/effective_go.html#composite_literals
[4]: http://golang.org/ref/spec#Allocation
[5]: http://golang.org/ref/spec#Making_slices_maps_and_channels
[6]: http://tour.golang.org/
[7]: http://golang.org/doc/effective_go.html