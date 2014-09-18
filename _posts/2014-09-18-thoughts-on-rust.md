---
layout: post
title: Thoughts on Rust and Go
tags:
  - rust
  - go
comments: true
---

I've been working with [Rust](http://www.rust-lang.org/) and [Go](http://golang.org/) recently.
While I like both languages, I feel that Rust has several advantages over Go as a programming language.
What follows are some of my thoughts on them.

First, some background: (feel free to skip ahead if you're familiar with these languages)

## Rust

Rust is a systems programming language developed by Mozilla.
It was created for the [Servo](https://github.com/servo/servo/) browser engine project.

Rust is suited from general programming, but it has several features (among others) that make it ideal for embedded programming:

* C-compatible ABI
* Runtime and stdlib are optional
* Control over stack vs. heap allocation
* Raw pointers
* Optional GC

One other key feature of Rust is *lifetimes*.
Basically, the Rust compiler tracks how long a variable is valid for (local, global, etc.), and automatically deallocates it when it goes out of scope.
This allows things like returning a pointer to a local variable to be caught at **compile-time**, instead of at run-time.

For more information, see [the Rust lifetimes guide](http://doc.rust-lang.org/guide-lifetimes.html)

## Go

Go is a language developed by Google. Like Rust, it's statically typed.
However, Go has a much less powerful type system.
It lacks things like generics and pattern matching (though [the FAQ](https://golang.org/doc/faq#generics) doesn't view lack of generics as much of a problem)

Like Python and Ruby, Go gives the programmer no control over stack vs heap allocation.
This isn't much of an issue for general programming, but for embedded programming, it's a big issue.

However, several other aspects of Go make it (in my opinion) a less powerful language than Rust:

* Lack of generics
* **null**
* Data races

See [Why Go Is Not Good](http://yager.io/programming/go.html) for more details.

I won't go into much detail on generics and **null**; Will Yager does an excellent job of explaining them in his post (linked above).

### Generics

Generics are an essential part of building type-safe, generic classes and data structures.
Essentially, they allow you to use *type variables* instead of hardcoding a type.  
At compile time, the type variable is replaced by a type supplied by the user of the class.
This allows, for example, the `ArrayList` class in Java to have the `get` method return the type of whatever type you use, instead of having to use `Object`.

Go, on the other hand, has no generics. This means you're stuck using `interface{}`, which is akin to Java's `Object`.  
You then have to cast `interface{}` to whatever type you *expect* to be there, however, all compile-time type safety is lost.

### null

Like with generics, I'll only discuss this briefly.

**null** essentially bypasses the safety given to you by the type system. You might *think* a type is a `String`, but it could actually be `null`.  
This leads to a run-time error when trying to call any method on it, forcing you to put in checks for **null** everywhere.


### Data races

Go bills itself as an excellent language for concurrency.
While Channels and Goroutines are good, it's still possible (and common) to run into data races with Go.

Go's motto when it comes to concurrency is, "Don't communicate by sharing memory; share memory by communicating."
This refers to the use of Channels to communicate between Goroutines, as opposed to sharing memory between them.

However, unlike in Rust, this isn't actually enforced by the language.
You can still cause concurrency problems by sharing memory, just as in most other languages.

**A better way - Pointer ownership in Rust**

Closely tied to the concept of lifetimes are Rust's [pointer ownership semantics](http://doc.rust-lang.org/guide.html#pointers).

In Rust, a variable is immutable by default. To make a variable mutable, you have to explicitly declare it with the `mut` keyword.

You can only use one mutable reference to a variable at a time.
So, if I give a mutable reference to a task (similar to Go's Goroutines), I can't then use one myself.

This guarantees, at compile time, that most race conditions can't exist, as they would violate Rust's ownership rules.

# Summary

While Go and Rust both have interesting features, I believe that Rust has numerous advantages over Go.
Though Rust is still unstable (code may break from release to release), I think it has a lot of potential as a language
