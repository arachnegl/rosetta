+++
title = "Practical Golang"
date = "2023-08-31T20:04:56+02:00"
author = "Greg Loyse"
authorTwitter = "" #do not include @
cover = ""
tags = ["GoLang", "Programming Languages", "Comparison", ]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# reference

## operators
```go
Arithmetic: +, -, *, /, %
Increment/Decrement: ++, --
Comparison: ==, !=, <, >, <=, >=
Logical: &&, ||, !
Bitwise: &, |, ^, <<, >>
Assignment: =, +=, -=, *=, /=, %=, <<=, >>=, &=, ^=, |=
Others: &, *, <- (address, dereference, channel operations)
Ternary: There is no ternary conditional operator ? : in Go.
```

## variables

zero value defaults.

Variables declared without an initial value are given their zero value: 
- 0 for numeric types, 
- false for booleans, 
- "" for strings.

## control flow


### conditionals


```go
# if else
func main() {
    x := 10
    if x > 5 {
        fmt.Println("x is greater than 5")
    } else if x == 5 {
        fmt.Println("x is 5")
    } else {
        fmt.Println("x is less than 5")
    }
}
```

```go
# switch
func main() {
    x := 5
    switch x {
    case 1:
        fmt.Println("x is 1")
    case 2:
        fmt.Println("x is 2")
    case 5:
        fmt.Println("x is 5")
    default:
        fmt.Println("x is unknown")
    }
}
```

```go
# select for channels
func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)

    go func() {
        time.Sleep(1 * time.Second)
        ch1 <- "Message from ch1"
    }()
    go func() {
        time.Sleep(2 * time.Second)
        ch2 <- "Message from ch2"
    }()

    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-ch1:
            fmt.Println(msg1)
        case msg2 := <-ch2:
            fmt.Println(msg2)
        }
    }
}
```

### loops with `for`


```go
# traditional
for i := 0; i < 10; i++ {
    fmt.Println(i)
}

# while
i := 0
for i < 10 {
    fmt.Println(i)
    i++
}

# infinite
for {
    fmt.Println("Infinite loop")
}

# range
nums := []int{1, 2, 3}
for i, num := range nums {
    fmt.Printf("Index: %d, Value: %d\n", i, num)
}

```

`range` keyword for iterating through slices, maps, arrays, and strings.


## collections


```go
// array - fixed length homogenous type
var arr [3]int 
arr[0] = 1
arr[1] = 2
arr[2] = 3
fmt.Println(arr)  // Output: [1 2 3]


// slice - dynamic length homogenous type
sl := []int{1, 2, 3}
sl = append(sl, 4)
fmt.Println(sl)  // Output: [1 2 3 4]

// map
m := make(map[string]int)
m["one"] = 1
m["two"] = 2
fmt.Println(m)  // Output: map[one:1 two:2]

```

Go doesn't have built-in support for sets, linked lists, trees, or other advanced data structures.


## functions

Supports first class & higher order functions.

```go
package main

import "fmt"

func add(a int, b int) (int, error) {
  return a + b, nil
}

func subtract(a, b int) int { // same types can be grouped
  return a - b
}

func namedReturn(a int) (result int) { // named return variable
  result = a * 2
  return
}

func variadicFunc(nums ...int) int {
  sum := 0
  for _, num := range nums {
    sum += num
  }
  return sum
}
```

defer   - allows you to ensure that a function call is performed later in a program’s execution, usually for purposes of cleanup.
panic   - stops the ordinary flow of control and begins panicking.
recover - regains control of a panicking goroutine.

No default parameters.

custom structs
TODO option pattern

## object oriented programming

### basic

public and private attributes and methods are declared by the casing of the first letter of the identifier.

```go
package main

import "fmt"

type Bicycle struct {
    Brand  string
    Model  string
    Wheels int
    age    int    // private
}

// Constructor-like function to initialize a new Bicycle
func NewBicycle(brand, model string, wheels int) *Bicycle {
    return &Bicycle{Brand: brand, Model: model, Wheels: wheels}
}

func (b *Bicycle) Describe() string {
    return fmt.Sprintf("This is a %s %s with %d wheels.", b.Brand, b.Model, b.Wheels)
}

func (b *Bicycle) UpdateBrand(newBrand string) {
    b.Brand = newBrand
}

func (b *Bicycle) AddWheel() {
    b.Wheels++
}

func (b *Bicycle) AddOneYear() {
    b.age++
}

func main() {
    myBike := NewBicycle("Trek", "Domane", 2)
    
    fmt.Println("Brand:", myBike.Brand)

    description := myBike.Describe()
    fmt.Println(description)

    myBike.UpdateBrand("Giant")
    fmt.Println(myBike.Describe())

    myBike.Wheels = 3
    fmt.Println(myBike.Describe())

    myBike.AddWheel()
    fmt.Println(myBike.Describe())
}

```

### interfaces

Go uses a type of duck typing through interfaces. 
If a type provides all the methods declared by an interface, 
it implicitly implements that interface without requiring an explicit declaration.

```go
package main

import "fmt"

// Bicycle interface
type Bicycle interface {
    Move()
    Stop()
}

// MountainBike struct
type MountainBike struct {
    Gear int
}

// Move method for MountainBike
func (mb MountainBike) Move() {
    fmt.Println("MountainBike is moving")
}

// Stop method for MountainBike
func (mb MountainBike) Stop() {
    fmt.Println("MountainBike has stopped")
}

// RoadBike struct
type RoadBike struct {
    Speed int
}

// Move method for RoadBike
func (rb RoadBike) Move() {
    fmt.Println("RoadBike is moving")
}

// Stop method for RoadBike
func (rb RoadBike) Stop() {
    fmt.Println("RoadBike has stopped")
}

// Function that expects a Bicycle interface
func RideBike(b Bicycle) {
    b.Move()
    b.Stop()
}

func main() {
    // Declare a MountainBike and a RoadBike
    mb := MountainBike{Gear: 10}
    rb := RoadBike{Speed: 20}

    // Both types can be used with the RideBike function, because they implement the Bicycle interface
    RideBike(mb)
    RideBike(rb)
}


```

## concurrency

concepts:
- goroutines - light weight threads managed by go runtime. Cheaper than OS threads (smaller stack size...). Multiplexed into smaller number of OS threads usually 1 per core.
- channels   - thread communication. CSP model. Blocking by default and so can be used as sync mechanism.


```go

func main() {
	go printCount("from goroutine")                         // Goroutines
	var wg sync.WaitGroup
	wg.Add(1)                                               // WaitGroup inc
	go func() {
		printCount("from goroutine with WaitGroup")
		wg.Done()                                           // WaitGroup dec
	}()
	wg.Wait()                                               // WaitGroup counter is 0

	messageChannel := make(chan string)                     // Channels
	go sendMessage("Hello from channel!", messageChannel)
	message := <-messageChannel
	fmt.Println(message)
}

// A function to simulate some work
func printCount(msg string) {
	for i := 0; i < 3; i++ {
		fmt.Println(msg, i)
		time.Sleep(time.Millisecond * 100)
	}
}

// Function to send a message through a channel
func sendMessage(msg string, ch chan string) {
	ch <- msg
}
```

## error handling

Go handles errors through explicit return values rather than exceptions. This can be both more verbose and more straightforward.

```go
func divide(a, b float64) (float64, error) {
    if b == 0.0 {
        return 0.0, errors.New("cannot divide by zero")
    }
    return a / b, nil
}

```


## structure

### modules

### packages





## not available

- Classes and Inheritance: Use composition and interfaces instead.
- Generics: This could change.
- Ternary Operator.
- Function Overloading: Functions cannot have the same name with different parameters.
- Implicit Type Conversion.
- Enums: Typically, use constants.
- Optional Parameters: Go does not have a syntax for optional parameters or default values. You can use variadic functions or function options pattern as alternatives.
- Built-in Set Type: Use map[T]bool to simulate a set.
- List Comprehensions: Common in Python but not in Go.
- Exceptions: Error handling is done explicitly through return values.
- Pointer arithmetic.
