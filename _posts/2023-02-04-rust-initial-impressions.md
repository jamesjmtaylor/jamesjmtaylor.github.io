---
title: Rust - Initial Impressions
date: '2023-02-04T06:31:14-08:00'
tags: rust
---
![Robot Crab](/assets/krabs.jpg)

As part of my New Year's resolution I've been studying the Rust programming language with the ultimate goal of building a bot for the game StarCraft (hence the robot Mr. Krabs, since the Rust mascot is a crab and my end goal is to build a bot with it).  

I've been reading through the [Rust Programming Language Guide](https://doc.rust-lang.org/book/title-page.html) and uploading my notes and intermediate projects [here](https://github.com/jamesjmtaylor/rust).

So far the language syntactically (if not semantically) is very similar to Swift.  You can use `if let` statements to unwrap options, just like in Swift:

```rust
let config_max = Some(3u8);
if let Some(max) = config_max {
    println!("The maximum is configured to be {}", max);
} else {
    println!("The maximum is null!");
}
```

Rust also has Swift-style parameters and return type syntax, i.e.:

```rust
fn plus_one(x: i32) -> i32
```

Rust ranges and iterators also use the same `for` loop syntax as Swift:

```rust
let mut sum = 0;
for n in 1..11 {
    sum += n;
}

// Or

let menu = &["apple", "cake", "coffee"];

for item in menu  {
    println!("I want{}.", item);
}
```

Finally, Rust has a struct keyword like Swift:

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Borrows immutable self w/reference; we don't want to write, just read.
    fn area(&self) -> u32 { // if you need to write, use `&mut self`
        self.width * self.height
    }
}
```

Unlike Swift, structs are either entirely mutable or immutable.  They can't contain functions and instead must declare them in an `impl` block as demonstrated above.

The semantics of Rust are actually much more similar to the first programming language I learned, [Ada](https://en.wikipedia.org/wiki/Ada_(programming_language)). Memory and null safety are critical concepts in Rust and the language is designed to ensure that most memory and null-pointer exceptions are caught at compile time. Memory is managed through ownership. 

There are two possible storage mechanisms:


* Stack: Stores & removes data in LIFO order. Data must have known, fixed size.
* Heap: Stores arbitrarily sized data at the first address with enough space. Tracks data address with pointers, which are stored on a stack.

The ownership rules in Rust are:

1. Each value has an owner
2. There can only be one owner at a time
3. When an owner goes out of scope, the value is dropped

The last rule especially caught me by surprise. Unlike Kotlin or Swift, passing a heap pointer to a function will cause the variable to invalidate afterwards! 

There are a number of solutions to keep the variable in scope afterwards:


1. Make a clone first (slow).
2. Have the fn return the pointer in a tuple at the end (ugly).
3. Pass a reference type.

Passing a reference is like letting an inner scope "borrow" a pointer. Reference types are created by prepending the parameter type and heap variable with `&`. You can explicitly de-reference with `*`. References are immutable by default. They can be made mutable with `&mut`, but a mutable reference can only have one borrower.  You'll get compile error otherwise. 

Here are the usage of these symbols:

```
i32          // a pointer
&i32         // a reference
&'a i32      // a reference with an explicit lifetime
&'a mut i32  // a mutable reference with an explicit lifetime
&'static i32 // a static explicit lifetime
```

These notes only scratch the surface of Rust. I haven't even gotten to package management (cargo), closures (very similar to Swift), multithreading, or data passing.  My hope is that by highlighting the similarities and differences Rust has with other high-level programming languages you can get an idea of whether or not Rust is right for you.  

Until next time!
