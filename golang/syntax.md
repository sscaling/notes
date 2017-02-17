# Go cheatsheet

How to write Go code [https://golang.org/doc/code.html](https://golang.org/doc/code.html)

## Concepts

* go tool - used to fetch / build go packages
* GOPATH - environment variable specifying location of workspace
* workspace - where src / binaries / pkgs reside
* import path - unique location of package within workspace
	* standard library use short paths, such as "fmt"
* Exported name - starts with capital letter. Available outside package.

## Go sourcefile

	package name # 1st line must be
	             # executable commands must always use 'main'

## Syntax

### Everyday

	import "fmt"
	fmt.Println()
	fmt.Printf("%d\n", 1)
	fmt.Printf("%v, %T\n", x, x) # print value and type


### Types

Basic types:

	bool
	string
	int  int8  int16  int32  int64
	uint uint8 uint16 uint32 uint64 uintptr
	byte // alias for uint8
	rune // alias for int32
	     // represents a Unicode code point
	float32 float64
	complex64 complex128

### Variables

Declaration syntax [https://blog.golang.org/gos-declaration-syntax](https://blog.golang.org/gos-declaration-syntax)

Go's declaration reads left-to-right:

	name type # x int
	p *int # pointer to int
	f [3]int # array of 3 ints
	x, y int # two consecutive vars share same type
	var a, b, c int # declare list of variables
	var a, b = 1, 3 # initialized var list
	k := 3 # short var can be used inside function, with implicit type
	k := float64(3) # type conversion
	const Foo = "foo"  # constant

Variables with no initial value will be given the 'zero' value (0 / false / "" / nil)

### Pointers

*NOTE*: No pointer artithmetic

	var p *int # pointer-to-int
	var i = 42
	p = &i # put address of i in p (ala C)
	fmt.Println(*p) # de-reference p

### Arrays

Length is immutable

	var a []int = [4]int{1,2,3,4} # declare with values
	              [...]int{1,2,3,4} is equivelant
	a[0:2] # slice (view) of first 2 elements
	       # changing slice, changes backing array
	a[1:]  # slice till end
	a[:1]  # slice from start till 1

	# A slice can be re-sliced to change the view on the backing array
	s := []int{1,2,3,4}
	s = s[:2] # reduces to length of 2
	s = s[:]  # but now can see full array again

Noteworthy slice functions

	func make([]T, len, cap) []T
	func append(s []T, x ...T) []T
	func copy(dst, src []T) int


### Maps

	var m map[string]Vertex   # map Type, with string key and Vertex values
	m = make(map[string]Vertex)	  # initialize map ready to use
	m["foo"] = Vertex{ 3, 2 }	# assign a vertex to 'foo' key in map

	# map literal
	m := map[string]Vertex{
			"foo": Vertex{
				1, 2
			}
		}

	# short hand
	m := map[string]Vertex { "foo": {1,2} }

	delete(m, "foo")  # delete an element

	v, ok := m[key] # ok is true if present, else false.

### Structs

	type Point struct {  # declare a Point struct as a type
		X int
		Y int
	}

	p := Point{1,3}  # create struct on stack
	p.X = 3 # dot access to struct fields

	# pointers can de-reference struct fields, without *
	x = &p
	x.Y = 3 # is equivalent to (*x).Y = 3

### Functions

Built-in functions [https://golang.org/pkg/builtin/](https://golang.org/pkg/builtin/)

	func name(args) return-value { }
	func name(args) (rv1, rv2) {} # multiple return values

	# named return values, use a single 'naked' return statement
	func foo(a int) (b int) {
		b = a * 2
		return
	}

	func x(x int) {
		defer f(x)  # will evaluate args, but not execute until x returns.
		            # useful for cleanup, similar to Java finally
		            # evaluated last-in-first-out
		x = 3
	}

	# Functions are a first class type, and can be passed around too
	func apply(fn func() bool) bool {
		return fn()
	}

	var f = func() bool {
		return true
	}

	fmt.Println(apply(f))  # print's true

### Methods

Allow functions to receive a special type, giving some OO-like syntax to Go

	type Point struct {
		X, Y float64
	}

	# This is a 'value' receiver, it operates on a copy of the value
	func (p Point) Foo() bool {  # first argument list is the 'receiver' argument
		return p.x > 1.3
	}

	func main() {
		p := Point{3, 4}
		fmt.Println(v.Foo())  # Foo receives a Point
	}

	# 'pointer' receivers accept a reference to a value, and can change it directory
	func (p *Point) Translate(f float64) {
		p.X = p.X*2
	}


### Interfaces

an interface type is defined as a set of method signatures. A value of interface type can hold any value that implements those methods.

	type MyInt uint8

	func (i MyInt) Foo() {
		fmt.Println(i)
	}

	type Bar interface {
		Foo()
	}

	var b Bar = MyInt(4)  # there is a Foo method for MyInt receiver arg, so this
	b.Foo()               # is compatible with the interface

	var i interface {}   # the 'empty' interface. Can hold any values of any type (i.e. fmt.Print)


CONTINUE: https://tour.golang.org/methods/15


*NOTE*: it is important that all methods for a type implement either ALL value or ALL pointer receiver args as an interface matches the type as a whole.



### Iteration

	#for <init>; <condition>; <post> {}
	# e.g.
	for i :=0; i < 5; i++ {
		fmt.Println(i)
	}

	for x < y { } # <init> / <post> are optional (aka while)
	for { } # infinite

	a := []int{1,2,3,4}
	for i,v := range a { } # for-range for slice, has index and value for each element
	for _,v := range a { } # don't care about index
	for i := range a { } # don't care about value

### Conditionals

	#if <condition> { } else { }
	# e.g.
	if x < y {
		fmt.Println("lower")
	}

	if r := get(x); r < y { } # in-line & in-scope statement pre-condition

	switch [stmt]; s { # optional statement
	    case "foo": // auto break
	    case "bar": ...; fallthrough // fallthrough to next case
	    case f(): // evaluate result of f
	    default: ...
	}



## Testing

Uses the `go` tool's `test` command.

* filename must end in `_test.go`.
* functions must be named `TestXXX` with signature `func (t *testing.T)`

