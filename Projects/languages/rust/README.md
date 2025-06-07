
# An Introduction to Rust

Rust is a modern systems programming language focusing on safety, speed, and concurrency. It accomplishes these goals by being memory safe without using garbage collection.

- [Rust Documentation](https://doc.rust-lang.org/stable/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)



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

### Shadowing

You can declare a new variable with the same name as a previous variable. The first variable is sead to be `shadowed` by the second, which means that the second variable is what the compiler will see when you use the name of the variable. In effect, the second variable overshadows the first, taking any uses of the variable name to itself until either it itself is shadowed or the scope ends. We can shadow a variable using the same variable's name and repeating the use of the `let` keyword.

```rust
fn main() {
	let x = 5; // x is 5
	let x = x + 1; // x is 6

	{
		let x = x * 2; // x is 6*2 or 12
		println!("The value of x in the inner scope is {x}");
	}

	println!("The value of x in the outer scope is {x}"); // x is 6
}
```

## Constants

Like immutable variables, constants are values that are bound to a name and are not allowed to change, but there are a few differences between constants and variables.

You aren't allowed to use `mut` with constants. Constants aren't just immutable by default they  are always immutable. You declare constants using the `const` keyword instead of the `let` keyword, and the type of the value must be included.

Constants can be declared in any scope, including the global scope.

Constants may be set only to a constant expression, not the result of a value that could only be computed at runtime.

```rust
const ALPHA: u32 = 60 * 60 * 3;
```

Rusts naming convention for constants is to use all uppercase with underscores between words.

Constants are valid for the entire time a program runs, within the scope in which they were declared.

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

## Data Types

Rust is a statically typed language, which means that it must know the types of all variables at compile time.

### Data Type Subsets

- scalar
- compound

#### Scalar Types

A scalar type represents a single value. Rust has four primary scalar types:

- Integers
- Floating-Point Numbers
- Booleans
- Characters

#### Integer Types

- `i8` signed integer (8 bit)
- `u8` unsigned integer (8 bit)
- `i16` signed integer (16 bit)
- `u16` unsigned integer (16 bit)
- `i32` signed integer (32 bit)
- `u32` unsigned integer (32 bit)
- `i128` signed integer (128 bit)
- `u128` unsigned integer (128 bit)
- `isize` signed integer (arch)
- `usize` unsigned integer (arch)

Note: `isize` and `usize` types depend on the architecture of the computer your program is running on: 64 bits if you're on a 64 bit architecture and 32 bits if you are on a 32 bit architecture.

Signed numbers are stored using two's complement representation.

Each signed variant can store numbers from $-(2^(n-1))$ to $2^(n-1)-1$ inclusive, where `n` is the number of bits that variant uses.

Unsigned variants can store numbers from 0 to $2^(n)-1$.

So `i8` can store numbers from -128 to 127. and `u8` can store numbers from 0 to 255.

#### Integer Literals

- Decimal (98_222 or 98222)
- Hex (0xff)
- Octal (Oo77)

## References

- [Rust Website](https://www.rust-lang.org/)
