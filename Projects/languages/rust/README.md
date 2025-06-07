
# An Introduction to Rust

Rust is a modern systems programming language focusing on safety, speed, and concurrency. It accomplishes these goals by being memory safe without using garbage collection.



### Cargo: the Rust build tool and package manager

When you install Rustup you’ll also get the latest stable version of the Rust build tool and package manager, also known as Cargo. Cargo does lots of things:

- build your project with `cargo build`
- run your project with `cargo run`
- test your project with `cargo test`
- build documentation for your project with `cargo doc`
- publish a library to [crates.io](https://crates.io/) with `cargo publish`

To test that you have Rust and Cargo installed, you can run this in your terminal of choice:

`cargo --version`

## Comments

Single line comments can be specified as follows:

```rust
// whatever you want to say
```

Multi-line comments can be specified as follows:

```rust
/*
be
explicit
*/
```

## Variables

Variables are declared as follows.

```rust
let x = 5;
```

In rust variables are immutable by default so no other value can be assigned to x after it has been declared this way.

It is possible to provide mutability using the `mut` keyword.

```rust
let mut x = 5;
```


## Functions

```rust
fn main() {
	// ...
}
```

## Hello World

```rust
// hello.rs
// This is a comment, and is ignored by the compiler.
// You can test this code by clicking the "Run" button over there ->
// or if you prefer to use your keyboard, you can use the "Ctrl + Enter"
// shortcut.

// This code is editable, feel free to hack it!
// You can always return to the original code by clicking the "Reset" button ->

// This is the main function.
fn main() {
    // Statements here are executed when the compiled binary is called.

    // Print text to the console.
    println!("Hello World!");
}
```

```shell
rustc hello.rs
./hello
```




## References

- [Rust Website](https://www.rust-lang.org/)
