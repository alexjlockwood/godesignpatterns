---
layout: post
title: 'Arrays vs Slices'
---
This post will briefly describe the differences between arrays and slices in Go. It assumes that you have completed
[A Tour of Go][8] and have consulted relevant sections of [Effective Go][9] and
the target audience is primarily newcomers to the Go programming language.

[Arrays][1] are fixed-length sequences of items of the same type.
Arrays in Go can be created using the following syntaxes:

```go
[N]Type
[N]Type{value1, value2, ..., valueN}
[...]Type{value1, value2, ..., valueN}
```

Unlike in C/C++ (where arrays act like pointers) and Java (where arrays
are object references), arrays in Go are <i>values</i>. This has a
couple of important implications: (1) assigning one array to another
copies all of the elements, and (2) if you pass an array to a function,
it will receive a copy of the array (not a pointer or reference to it).

<!--more-->

As you might imagine, this can be very expensive, especially when
you are working with arrays that have a large number of elements.

[Slices][2], on the other hand, are much more flexible, powerful,
and convenient than arrays. Unlike arrays, slices can be resized
using the built-in [`append`][3]
function. Further, slices are <i>reference types</i>, meaning
that they are cheap to assign and can be passed to other functions
without having to create a new copy of its underlying array. Lastly,
the functions in Go's standard library all use slices rather than
arrays in their public APIs.

Slices can be created using the following syntaxes:

```go
make([]Type, length, capacity)
make([]Type, length)
[]Type{}
[]Type{value1, value2, ..., valueN}
new([10]int)[0:5]   #length=5, capacity=10
```

Overall, slices are cleaner, more flexible, and less bug-prone than
arrays, so you should prefer using them over arrays whenever possible.

### Recommended Readings:

* [Usage and Internals of Go Slices][4] from the Go Blog (<i>highly recommended</i>).
* [Arrays, slices (and strings): The mechanics of 'append'][5] from the Go Blog.
* Check out this article on [Slice Tricks][6] (but only if one-liners
  easily excite you).
* Refer to the [Go Programming Language Specification][7] for other
  technical/syntactical questions about arrays and slices.

[1]: http://golang.org/doc/effective_go.html#arrays
[2]: http://golang.org/doc/effective_go.html#slices
[3]: http://golang.org/pkg/builtin/#append
[4]: http://blog.golang.org/go-slices-usage-and-internals
[5]: http://blog.golang.org/slices
[6]: https://code.google.com/p/go-wiki/wiki/SliceTricks
[7]: http://golang.org/ref/spec
[8]: http://tour.golang.org/
[9]: http://golang.org/doc/effective_go.html
