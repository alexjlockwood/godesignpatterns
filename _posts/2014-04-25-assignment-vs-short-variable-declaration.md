---
layout: post
title: '= vs :='
---
This post will briefly describe the differences between
the assignments and short variable declarations in Go. 
It assumes that you have completed [A Tour of Go][6] 
and have consulted relevant sections of [Effective Go][7] and
the target audience is primarily newcomers to the Go programming
language.

The <i>assignment operator</i> (`=`) is used to perform
[assignments][1] (i.e. to assign or reassign values to
already declared variables). 

The <i>short declaration operator</i> (`:=`), on the other hand,
is used to perform [short variable declarations][2]),
which are shorthand for regular variable declarations but
without a specified type. 

<!--more-->

Here are four different ways to create a variable with
static type `int` and value `10`, each of which are equivalent:

```go
var a int = 10

var b = 10

var c int
c = 10

d := 10
```

Unlike regular variable declarations, a short variable declaration
may <i>redeclare</i> variables in multi-value assignments, assuming
at least one variable is new and the redeclared variable is assigned
a value of the same type. You'll see this property used frequently
when dealing with errors, as it avoids the need to declare differently
named variables such as `err1`, `err2`, etc. in the same block of code.
For example, consider the `CopyFile` function below, which opens two
files and copies the contents of one file to the other:

```go
func CopyFile(dstName, srcName string) (written int64, err error) {
    // 'err' is declared for the first time.
    src, err := os.Open(srcName)
    if err != nil {
        return
    }
    defer src.Close()

    // 'err' is redeclared (no need to declare a new variable
    // for the returned error).
    dst, err := os.Create(dstName)
    if err != nil {
        return
    }
    defer dst.Close()

    return io.Copy(dst, src)
}
```

In general, short variable declarations should be preferred because they
are more concise than regular variable declarations/assignments. Note,
however, that short variable declarations can only be used inside of
functions/methods (declaring global variables at the package level
can only be done using a regular variable declaration: `var name type = value`).

#### Recommended Readings:

* The [Assignments][1] and [Short variable declarations][2] sections in the
  official Go Programming Language Specification.
* The [Redeclaration and reassignment][3] section in Effective Go.
* [Handling errors and Go][4] from the Go Blog.
* The [Blank identifier in multiple assignment][5] section in Effective Go.

[1]: http://golang.org/ref/spec#Assignments
[2]: http://golang.org/ref/spec#Short_variable_declarations
[3]: http://golang.org/doc/effective_go.html#redeclaration
[4]: http://blog.golang.org/error-handling-and-go
[5]: http://golang.org/doc/effective_go.html#blank_assign
[6]: http://tour.golang.org/
[7]: http://golang.org/doc/effective_go.html
