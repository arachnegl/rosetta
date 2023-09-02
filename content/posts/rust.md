+++
title = "rust"
date = "2023-09-02T21:04:56+02:00"
author = "Greg Loyse"
authorTwitter = "" #do not include @
cover = ""
tags = ["rust", "Programming Languages", "Comparison", ]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++



# reference

The language emphasizes zero-cost abstractions, type safety, and a rich type system. 
Rust achieves safety without employing a garbage collector by utilizing a set of ownership rules enforced at compile-time.

## operators

C-style

```rust
// ranges
for i in 0..5 { /* exclusive, up to 4 */ }
for i in 0..=4 { /* inclusive, up to 4 */ }

```

## assignments & variables

```rust
// explicit mutations
fn main() {
    let mut sum = 0;
    for i in 1..=10 {
        sum += i;
    }
    println!("Sum: {}", sum);
}
```

### ownership & borrowing

The ownership model ensures that each value has a single "owner," 
and borrows must be either mutable or immutable, but not both simultaneously. 
This prevents data races and makes concurrent programming safer.

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1; // Ownership transferred
    // println!("{}", s1); // Error: value borrowed here after move
}

```

```rust
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1); // Borrowing

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

## control flow

### conditional

C-style

```rust

let x = 5;
if x > 0 {
    println!("Positive");
} else if x < 0 {
    println!("Negative");
} else {
    println!("Zero");
}

```

### pattern matching

more powerful switch.

```rust

let value = Some(4);
match value {
    Some(x) => println!("Got an integer: {}", x),
    None => println!("Got nothing"),
}

```

The if let and while let are syntactic sugar that combines if or while with pattern matching. 
They're useful for matching on a single pattern and ignoring others.

```rust

if let Some(x) = value {
    println!("Got an integer: {}", x);
}

let mut optional = Some(4);
while let Some(x) = optional {
    if x == 0 {
        optional = None;
    } else {
        optional = Some(x - 1);
    }
}

```

### loops

```rust

for i in 1..=5 {
    println!("{}", i); // Prints numbers from 1 to 5
}


let mut counter = 0;
loop {
    counter += 1;
    if counter > 5 {
        break;
    }
}


let mut x = 5;
while x > 0 {
    x -= 1;
}


```
### error handling

different from try-catch-finally paradigm.

functions that can fail typically return a Result<T, E> type, where T is the type of the successful return value, and E is the type of the error. Rust also uses the Option<T> type for functions that might not return a meaningful value.

uses `Result` and `Option` types for error handling

This approach encourages developers to handle errors explicitly, resulting in more robust and predictable code.

Functions that can fail should return a Result type.
The Result type is an enum with two variants: 
- Ok for successful results
- Err for errors. 

```rust
use std::fs::File;

fn open_file(filename: &str) -> Result<File, std::io::Error> {
    File::open(filename)
}

fn main() {
    match open_file("some_file.txt") {
        Ok(file) => {
            // Do something with the file
        },
        Err(error) => {
            // Handle the error
            println!("An error occurred: {}", error);
        },
    }
}

```

The Option type is used when a value can be either something or nothing (Some or None). 
often used for simple cases where you just want to know whether a function could produce a value.

```rust
fn divide(numerator: f64, denominator: f64) -> Option<f64> {
    if denominator == 0.0 {
        None
    } else {
        Some(numerator / denominator)
    }
}
```

```rust
fn division(dividend: f64, divisor: f64) -> Result<f64, &'static str> {
    if divisor == 0.0 {
        Err("Division by zero")
    } else {
        Ok(dividend / divisor)
    }
}
```

Note: compare & contrast with go's approach.

## collections

```rust
// Vec<T>
// growable array type
// fast O(1) indexed access and O(1) amortized time complexity for appending an element.
let mut vec = Vec::new();
vec.push(1);


// HashMap<K, V>
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert("Alice", 10);


// HashSet<T>
//  fast O(1) average time complexity for inserts, lookups, and deletions.
use std::collections::HashSet;

let mut set = HashSet::new();
set.insert(1);


// LinkedList<T>
// doubly-linked list
// where you require constant-time insertions and removals from both ends of the sequence.
use std::collections::LinkedList;

let mut list = LinkedList::new();
list.push_back('a');


// BinaryHeap<T>
// fast O(log N) inserts and O(1) time to find the maximum 
use std::collections::BinaryHeap;

let mut heap = BinaryHeap::new();
heap.push(1);


// BTreeMap<K, V> and BTreeSet<T>
// O(log N) time complexity for inserts, lookups, and deletions
// maintain their keys in sorted order
use std::collections::BTreeMap;

let mut map = BTreeMap::new();
map.insert(1, "a");


// VecDeque<T>
// double-ended queue implemented with a growable ring buffer.
// provides O(1) indexed access and O(1) time complexity 
// for pushing and popping elements from both ends.
use std::collections::VecDeque;

let mut deque = VecDeque::new();
deque.push_back(1);
deque.push_front(3);
```

## functions

```rust
/// Returns the square of a given integer `n`.
///
pub fn square(n: i32) -> i32 {
    n * n
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_square() {
        assert_eq!(square(4), 16);
        assert_eq!(square(-4), 16);
    }
}
```

```bash
cargo test
```

## object oriented

Not explicitly supported. 
does provide a set of features that allow you to implement OOP designs, like encapsulation, inheritance, and polymorphism.

```rust
// Define a Bicycle trait with behaviors common to all bicycles.
pub trait Bicycle {
    // A method to pedal the bicycle; takes a speed parameter.
    fn pedal(&self, speed: u32);

    // A method to apply brakes.
    fn brake(&self);
}

// A MountainBike struct, representing a specific kind of bicycle.
pub struct MountainBike {
    gear: u8,
    brake_type: String,
}

// Implement the Bicycle trait for MountainBike.
impl Bicycle for MountainBike {
    fn pedal(&self, speed: u32) {
        println!("Pedaling a mountain bike at {} km/h with gear {}.", speed, self.gear);
    }

    fn brake(&self) {
        println!("Applying {} brakes.", self.brake_type);
    }
}

// A RoadBike struct, another kind of bicycle.
pub struct RoadBike {
    gear: u8,
    brake_type: String,
}

// Implement the Bicycle trait for RoadBike.
impl Bicycle for RoadBike {
    fn pedal(&self, speed: u32) {
        println!("Pedaling a road bike at {} km/h with gear {}.", speed, self.gear);
    }

    fn brake(&self) {
        println!("Applying {} brakes.", self.brake_type);
    }
}

// A function that takes a Bicycle trait object; showcasing polymorphism.
pub fn ride(bike: &dyn Bicycle, speed: u32) {
    bike.pedal(speed);
    bike.brake();
}

// Main function to create instances and call methods.
fn main() {
    let mtb = MountainBike { gear: 5, brake_type: String::from("disc") };
    let rb = RoadBike { gear: 10, brake_type: String::from("rim") };

    // Calling methods on struct instances.
    mtb.pedal(10);
    mtb.brake();
    
    rb.pedal(20);
    rb.brake();

    // Using trait objects for polymorphism.
    ride(&mtb, 10);
    ride(&rb, 20);
}

```

Encapsulation: Each bicycle struct (MountainBike, RoadBike) encapsulates its own data (gear, brake_type).

Inheritance: traits serve a similar purpose. Bicycle acts like a base class, and each struct (MountainBike, RoadBike) that implements Bicycle acts like a derived class.

Polymorphism: The ride function takes a &dyn Bicycle, which is a trait object. It can accept any object that implements the Bicycle trait, allowing for polymorphic behavior.

Type Safety, ensures that wrong types can't be used.

## concurrency

safe and efficient thanks to compile time checks of ownership, borrowing, and lifetimes 
rules help eliminate data races. 

### threads

```rust
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

Channels for thread communication.

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hello");
        tx.send(val).unwrap();
    });

    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

```python
from queue import Queue
from threading import Thread

def worker(q):
    while not q.empty():
        item = q.get()
        print(f"Processed {item}")
        q.task_done()

if __name__ == "__main__":
    q = Queue()
    for i in range(5):
        q.put(i)
    
    t1 = Thread(target=worker, args=(q,))
    t2 = Thread(target=worker, args=(q,))
    t1.start()
    t2.start()
    t1.join()
    t2.join()

```

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();

    for i in 0..5 {
        let tx1 = mpsc::Sender::clone(&tx);
        thread::spawn(move || {
            tx1.send(i).unwrap();
            println!("Sent {}", i);
        });
    }

    drop(tx);

    for received in rx {
        println!("Received {}", received);
    }
}

```

### multi-processing

not natively supported.

### async

not natively supported.

example dependency `tokio`:

```rust
async fn foo() {
    println!("Foo");
}

fn main() {
    let _handle = tokio::spawn(async {
        foo().await;
    });

    tokio::run(_handle);
}
```

## code organisation and packaging

concepts:
- modules  - logical unit, scope, visibility.
- crates   - compilation unit. library or executable.
- packages - one or more crates. has a `Cargo.toml`.

the package manager `cargo`.

Explicit module declaration: 
- you define hierarchy and visibility of modules using keywords like `mod` and `pub`.
- you can declare multiple modules within a single file or split a single module across multiple files.


### example

```bash
cargo new my_project
```

```bash
tree my_project
my_project/
├── Cargo.toml
├── src/
│   ├── main.rs
│   └── lib.rs
└── tests/
    └── integration_test.rs
```

```rust
// src/main.rs
use my_project::math;

fn main() {
    let result = math::add(1, 2);
    println!("Result: {}", result);
}
```

```rust
// src/lib.rs
pub mod math {
    /// Adds two integers together.
    ///
    pub fn add(a: i32, b: i32) -> i32 {
        a + b
    }
}
```

```rust
// tests/integration_test.rs

#[cfg(test)]
mod test_math {
    use greg::math;

    #[test]
    fn test_add() {
        assert_eq!(math::add(1, 2), 3);
        assert_eq!(math::add(0, 0), 0);
        assert_eq!(math::add(-1, -1), -2);
    }
}
```

```bash
cargo run
cargo test
```
