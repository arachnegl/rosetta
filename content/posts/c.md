+++
title = "C programming"
date = "2023-08-31T21:04:56+02:00"
author = "Greg Loyse"
authorTwitter = "" #do not include @
cover = ""
tags = ["C", "Programming Languages", "Comparison", ]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++


# reference

## operators

many behave differently when applied to pointers.

```c
+ - * / %                     // arithmetic
== != < > <= =>               // relational
&& || !                       // logical
& | ^ ~ << >>                 // bitwise

= += *= /= %=                 // assignment
<<= >>= &= ^= |=              // assignment bitwise

<expr1> ? <expr2> : <expr3>   // ternary

-> . * &                      // pointer & member access
sizeof (type)

[] ()                         // array & function call

```


## control flow

### conditionals

```c
if (condition1) {
    // code
} else if (condition2) {
    // code
} else {
    // code
}

```

```c
switch(expression) {
    case constant1:
        // code
        break;
    case constant2:
        // code
        break;
    default:
        // code
}
```

```c
// goto
goto label; // Jumps to the label

label:
// code after label
```

### loops

```c
for (initialization; condition; update) {
    // code
}

// exiting loops
for (int i = 0; i < 10; i++) {
    if (i < 5) {
        continue;       // skip to next iteration
    }
    else if (i == 5) {
        break;          // break out of iteration
    }
}


while (condition) {
    // code
}

do {
    // code
} while (condition);
```

## collections

Only array is natively supported.

```c
int arr[5] = {1, 2, 3, 4, 5};
```

You can use `glibc` as a standard library.

## functions

no support for docs or default parameters. use idioms instead.

```c
#include <stdio.h>
#include <stdarg.h>

/**
 * Function: add_numbers
 * ---------------------
 * Add up to two numbers. If only one number is given, it will add zero
 * to that number, effectively returning the number itself.
 * 
 * @param count  Number of integers to be added (1 or 2)
 * @param ...    Up to two integers
 * @return       Sum of the integers
 *
 */
int add_numbers(int count, ...) {
    va_list args;
    va_start(args, count);

    int sum = 0;

    for (int i = 0; i < count; ++i) {
        sum += va_arg(args, int);
    }

    va_end(args);
    
    return sum;
}

int main() {
    // Using the function with two arguments
    printf("The sum of 3 and 4 is: %d\n", add_numbers(2, 3, 4));

    // Using the function with one argument (simulating default parameter)
    printf("The sum when only 3 is given is: %d\n", add_numbers(1, 3));

    return 0;
}

```


## `struct`

approximation of OOP.

```c
#include <stdio.h>
#include <stdlib.h>

// Define the Bicycle structure
typedef struct {
    char* color;  // public
    int speed;    // public
    
    // Function pointers (equivalent to methods)
    void (*pedal)(struct Bicycle*); 
    void (*brake)(struct Bicycle*);
} Bicycle;

// function pointers can be used to emulate object-oriented behavior.
void pedal(Bicycle *this) {
    this->speed += 5;
    printf("Speed increased to: %d\n", this->speed);
}

void brake(Bicycle *this) {
    this->speed -= 5;
    printf("Speed decreased to: %d\n", this->speed);
}

Bicycle* newBicycle(char* color, int speed) {
    Bicycle* bike = (Bicycle*) malloc(sizeof(Bicycle));
    bike->color = color;
    bike->speed = speed;
    bike->pedal = pedal;
    bike->brake = brake;
    return bike;
}

void deleteBicycle(Bicycle* bike) {
    // Clean up any dynamically allocated resources here
    free(bike);
    printf("Bicycle destroyed\n");
}

int main() {
    // Object creation
    Bicycle* myBike = newBicycle("Red", 0);
    
    // Access and Mutation
    printf("Initial color: %s\n", myBike->color);
    printf("Initial speed: %d\n", myBike->speed);
    
    // Using function pointers to call methods
    myBike->pedal(myBike);
    myBike->brake(myBike);
    
    // Object deletion
    deleteBicycle(myBike);
    
    return 0;
}

```


## concurrency

no native support.

POSIX threads on Linux and MacOS.


```c
#include <pthread.h>
#include <stdio.h>

void* print_hello(void* data) {
    printf("Hello from thread!\n");
    return NULL;
}

int main() {
    pthread_t thread1, thread2;

    // Create two threads
    pthread_create(&thread1, NULL, print_hello, NULL);
    pthread_create(&thread2, NULL, print_hello, NULL);

    // Wait for the threads to finish
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    return 0;
}

```

## preprocessor directives

```c
#include: // Includes a header file.
#define:  // Defines a macro.

// Conditional compilation.
#ifdef, 
#ifndef, 
#else, 
#endif:   

```

## error handling

No explicit support.

```c
// DIY
int result = some_function();

if (result == ERROR_CODE) {
    // Handle error
}

// 
#include <errno.h>
#include <stdio.h>

FILE *fp = fopen("nonexistent_file.txt", "r");

if (fp == NULL) {
    perror("Failed");  // Output: Failed: No such file or directory
    printf("%d\n", errno);  // Output will depend on your system
}

// output parameters
int error_code;
int result = some_function_with_output_error(&error_code);

if (result == ERROR_CODE) {
    // Handle error using error_code
}

// exit & abort
if (some_fatal_error_condition) {
    exit(EXIT_FAILURE);
}

```

## standard library

```c
<stdio.h>    // io ops
<stdlib.h>   // malloc() free() atoi()
<string.h>   // strcpy(), strlen()
```


## tooling

### debugging

### profiling 

`gprof`, `Valgrind`...

### testing

Check, Unity, and Ceedling


## popular open source projects in C

- curl
- linux kernal
- git
- sqlite
- redis
- VLC media player
- nginx
