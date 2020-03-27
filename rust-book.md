Rust Book Notes
===============

Source: https://doc.rust-lang.org/stable/book/

Programming a Guessing Game
---------------------------

In Rust, variables and references are immutable by default.

The :: syntax in the ::new line indicates that new is an associated function of the String type. An associated function is implemented on a type, in this case String, rather than on a particular instance of a String. Some languages call this a static method.

Common Programming Concepts
---------------------------

Constants aren’t just immutable by default—they’re always immutable. Constants may be set only to a constant expression, not the result of a function call or any other value that could only be computed at runtime.

The difference between mut and shadowing is that because we’re effectively creating a new variable when we use the let keyword again, we can change the type of the value but reuse the same name.

Rust has four primary scalar types: integers, floating-point numbers, Booleans, and characters. Variable (computing), or scalar, an atomic quantity that can hold only one value at a time.

```
Integer Types in Rust
Length   Signed     Unsigned
8-bit	    i8	      u8
16-bit	    i16	      u16
i32-bit	    i32	      u32
64-bit	    i64	      u64
128-bit	    i128	  u128
arch	    isize	  usize
```

Floating-point numbers are represented according to the IEEE-754 standard. The f32 type is a single-precision float, and f64 has double precision.

Rust’s char type is four bytes in size and represents a Unicode Scalar Value, which means it can represent a lot more than just ASCII.

Compound types can group multiple values into one type. Rust has two primitive compound types: tuples and arrays. These are also members of built-in Composite types together with struct and enum.

A tuple is a general way of grouping together a number of values with a variety of types into one compound type. Tuples have a fixed length: once declared, they cannot grow or shrink in size.

Another way to have a collection of multiple values is with an array. Unlike a tuple, every element of an array must have the same type. Arrays in Rust are different from arrays in some other languages because arrays in Rust have a fixed length, like tuples.

Arrays are useful when you want your data allocated on the stack rather than the heap or when you want to ensure you always have a fixed number of elements.

```
fn main() {
    let tup = (500, 6.4, 1);

    println!("The value of second element in tuple is: {}", tup.1);
    let (x, y, z) = tup;
    println!("The value of z is: {}", z);
}
```

A vector is a similar collection type provided by the standard library that is allowed to grow or shrink in size. If you’re unsure whether to use an array or a vector, you should probably use a vector.

```
fn main() {
    let a = [1, 2, 3, 4, 5];
    let a: [i32; 5] = [1, 2, 3, 4, 5];
    // initialize array with five elements, all equal to 3
    let a = [3; 5];
    // access by index
    let first = a[0];
    // access by index out of bounds, compile but panic at runtime
    let first = a[10];
}
```

Rust is an expression-based language. Statements are instructions that perform some action and do not return a value. Expressions evaluate to a resulting value.

The let statement does not return a value, so there isn’t anything for to bind to. This is different from what happens in other languages, such as C and Ruby, where the assignment returns the value of the assignment.

```
fn main() {
    let a = b = [1, 2, 3, 4, 5]; // invalid, cannot assign statement to statement
}
```

Calling a function is an expression. Calling a macro is an expression. The block that we use to create new scopes, {}, is an expression. Expressions do not include ending semicolons. If you add a semicolon to the end of an expression, you turn it into a statement, which will then not return a value

Unlike languages such as Ruby and JavaScript, Rust will not automatically try to convert non-Boolean types to a Boolean.

As if is an expression, we can use it on the right side of a let statement.

Rust has three kinds of loops: loop, while, and for. Let’s try each one.

Using while to iterate over collection is slow as the compiler adds runtime code to perform the conditional check on every element on every iteration through the loop. Moreover, it is error prone, as it is error is caught only at runtime. For loop is very often superior approach to iterate over the collection.

Understanding Ownership
-----------------------

All programs have to manage the way they use a computer’s memory while running. Some languages have garbage collection that constantly looks for no longer used memory as the program runs; in other languages, the programmer must explicitly allocate and free the memory. Rust uses a third approach: memory is managed through a system of ownership with a set of rules that the compiler checks at compile time. None of the ownership features slow down your program while it’s running.

Both the stack and the heap are parts of memory that are available to your code to use at runtime, but they are structured in different ways. The stack stores values in the order it gets them and removes the values in the opposite order. This is referred to as last in, first out. All data stored on the stack must have a known, fixed size. Data with an unknown size at compile time or a size that might change must be stored on the heap instead.

The heap is less organized: when you put data on the heap, you request a certain amount of space. The operating system finds an empty spot in the heap that is big enough, marks it as being in use, and returns a pointer, which is the address of that location. This process is called allocating on the heap and is sometimes abbreviated as just allocating. Pushing values onto the stack is not considered allocating. Because the pointer is a known, fixed size, you can store the pointer on the stack, but when you want the actual data, you must follow the pointer.

Pushing to the stack is faster than allocating on the heap because the operating system never has to search for a place to store new data; that location is always at the top of the stack. Comparatively, allocating space on the heap requires more work, because the operating system must first find a big enough space to hold the data and then perform bookkeeping to prepare for the next allocation. Accessing data in the heap is slower than accessing data on the stack because you have to follow a pointer to get there. Contemporary processors are faster if they jump around less in memory.

Ownership Rules:

-	Each value in Rust has a variable that’s called its owner.
-	There can only be one owner at a time.
-	When the owner goes out of scope, the value will be dropped.

In C++, the pattern of deallocating resources at the end of an item’s lifetime is sometimes called Resource Acquisition Is Initialization (RAII). The drop function in Rust will be familiar to you if you’ve used RAII patterns.

When one variable takes over value pointed to by other variable (when value is on stack), the first variable is automatically invalidated (in sense that it cannot be referenced/used later in the execution). Thus, the assignment performs kind of shallow copy and the ownership is moved from first to second variable. Such mechanism is needed to prevent "double free" programming error and follows the rule that each value is owned by one owner at a time.

Rust will never automatically create “deep” copies of your data (as deep copy is expensive operation, it is better if it is consciously performed by the programmer). Therefore, any automatic copying can be assumed to be inexpensive in terms of runtime performance.

If we do want to deeply copy the heap data of the String, not just the stack data, we can use a common method called clone. As a general rule, any group of simple scalar values can be Copy, and nothing that requires allocation or is some form of resource is Copy. Here are some of the types that are Copy:

-	All the integer types, such as u32.
-	The Boolean type, bool, with values true and false.
-	All the floating point types, such as f64.
-	The character type, char.
-	Tuples, if they only contain types that are also Copy. For example, (i32, i32) is Copy, but (i32, String) is not.

Passing a variable to a function will move or copy, just as assignment does. Ampersand is used to declare reference of variable, and it allows you to refer to some value without taking ownership of it. The opposite of referencing by using & is dereferencing, which is accomplished with the dereference operator, `*`.

Just as variables are immutable by default, so are references. We’re not allowed to modify something we have a reference to.

Mutable references have one big restriction: you can have only one mutable reference to a particular piece of data in a particular scope. This restriction allows for mutation but in a very controlled fashion. The benefit of having this restriction is that Rust can prevent data races at compile time:

-	Two or more pointers access the same data at the same time.
-	At least one of the pointers is being used to write to the data.
-	There’s no mechanism being used to synchronize access to the data.

Moreover, it is not possible to have a mutable reference while we have an immutable one.

References recap:

-	At any given time, you can have either one mutable reference or any number of immutable references.
-	References must always be valid.

Another data type that does not have ownership is the slice. Slices let you reference a contiguous sequence of elements in a collection rather than the whole collection.

The string slice, denoted &str, is a slice pointing to that specific point of memory (either stack or heap). String literals are immutable and &str is an immutable reference. String slice is a reference to part of String.

```
let s = String::from("hello world");
let hello = &s[0..5];
```

Note: String slice range indices must occur at valid UTF-8 character boundaries.

It is possible to create slice referencing other types, e.g.:

```

let a = [1, 2, 3, 4, 5];
let slice = &a[1..3];
```

It works the same way as string slices do, by storing a reference to the first element and a length.

Using Structs to Structure Related Data
---------------------------------------

A struct, or structure, is a custom data type that lets you name and package together multiple related values that make up a meaningful group. If you’re familiar with an object-oriented language, a struct is like an object’s data attributes.

Note that the entire instance of struct must be either mutable or immutable; Rust doesn’t allow us to mark only certain fields as mutable.

Struct methods are defined using impl block for given struct and self is passed as the first argument. Based on self declaration, the method is:

-	reading (&self) - not taking ownership, just reading struct values
-	mutating (&mut self) - not taking ownership, but able to mutate struct values
-	consuming (self) - very rare, usually used when the method transforms self into something else and you want to prevent the caller from using the original instance after the transformation.

By defining functions within impl blocks that don’t take self as a parameter, one can create associated functions associated with the struct. Usually, these are used as constructors.

Enums and Pattern Matching
--------------------------

Enums allow you to define a type by enumerating its possible variants.

```
enum IpAddrKind {
    V4,
    V6,
}
```

You can put any kind of data inside an enum variant: strings, numeric types, or structs, for example.

```
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

This enum has four variants with different types:

-	Quit has no data associated with it at all.
-	Move includes an anonymous struct inside it.
-	Write includes a single String.
-	ChangeColor includes three i32 values.

Also, it is possible to define methods on enums the same way as with structs:

```
impl Message {
    fn call(&self) {
        // method body would be defined here
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

Expressing all possible variants using enum in terms of the type system means the compiler can check whether you’ve handled all the cases you should be handling and thus prevent bugs that are extremely common in other programming languages at compile time.

Rust doesn’t have the null feature that many other languages have. Null is a value that means there is no value there. In languages with null, variables can always be in one of two states: null or not-null. As such, Rust does not have nulls, but it does have an enum that can encode the concept of a value being present or absent - Option type:

```
enum Option<T> {
    Some(T),
    None,
}
```

The goal is to enforce user to convert an Option<T> to a T before performing operations on T. Generally, this helps catch one of the most common issues with null: assuming that something isn’t null when it actually is.

Patterns can be made up of literal values, variable names, wildcards, and many other things - The power of match comes from the expressiveness of the patterns and the fact that the compiler confirms that all possible cases are handled. The code associated with each match arm is an expression, and the resulting value of the expression in the matching arm is the value that gets returned for the entire match expression. Matches in Rust are exhaustive: we must exhaust every last possibility in order for the code to be valid.

Managing Growing Projects with Packages, Crates, and Modules
------------------------------------------------------------

A package can contain multiple binary crates and optionally one library crate.

Rust module system includes:

-	Packages: A Cargo feature that lets you build, test, and share crates
-	Crates: A tree of modules that produces a library or executable
-	Modules and use: Let you control the organization, scope, and privacy of paths
-	Paths: A way of naming an item, such as a struct, function, or module

A crate is a binary or library. A package is one or more crates that provide a set of functionality. A package contains a Cargo.toml file that describes how to build those crates. A package must contain zero or one library crates, and no more. It can contain as many binary crates as you’d like, but it must contain at least one crate (either library or binary).

Modules let us organize code within a crate into groups for readability and easy reuse. Modules also control the privacy of items, which is whether an item can be used by outside code (public) or is an internal implementation detail and not available for outside use (private).

If we want to call a function, we need to know its path. A path can take two forms:

-	An absolute path starts from a crate root by using a crate name or a literal crate.
-	A relative path starts from the current module and uses self, super, or an identifier in the current module.

Using relative or absolute path is a decision which should depend on whether it is more likely to move item definition code

-	separately from the code that uses the item -> absolute
-	together with the code that uses the item -> relative

Rust preference is to specify absolute paths.

Modules aren’t useful only for organizing your code but also to define privacy boundary: the line that encapsulates the implementation details external code isn’t allowed to know about, call, or rely on. The way privacy works in Rust is that all items (functions, methods, structs, enums, modules, and constants) are private by default.

Default for enum variants is to be public. Struct fields follow the general rule of everything being private by default unless annotated with pub.

If we want to bring all public items defined in a path into scope, we can specify that path followed by `*`, the glob operator

Using a semicolon after mod rather than using a block tells Rust to load the contents of the module from another file with the same name as the module. Module structure can follow the structure of the file system - each module is defined in the file named the same as module and its children are part of the directory with the same name:

```
.
├── module
│   ├── first
│   │   └── second.rs
│   └── first.rs
└── lib.rs

// lib.rs
mod module;

pub use crate::module::first::second

pub fn print_second() {
    println!("{}", second::print_self());
}

// first.rs
pub mod second;
```

Common Collections
------------------

Collections can contain multiple values. The data these collections point to is stored on the heap (compared to the built-in array and tuple types), which means the amount of data does not need to be known at compile time and can grow or shrink as the program runs.

Basic collections:

-	A vector allows you to store a variable number of values next to each other.
-	A string is a collection of characters. We’ve mentioned the String type previously, but in this chapter we’ll talk about it in depth.
-	A hash map allows you to associate a value with a particular key. It’s a particular implementation of the more general data structure called a map.

The String type, which is provided by Rust’s standard library rather than coded into the core language, is a growable, mutable, owned, UTF-8 encoded string type. Rust strings don’t support indexing. A String is a wrapper over a Vec<u8>.

Another point about UTF-8 is that there are actually three relevant ways to look at strings from Rust’s perspective: as bytes, scalar values, and grapheme clusters (the closest thing to what we would call letters).

If we look at the Hindi word “नमस्ते” written in the Devanagari script, it is stored as a vector of u8 values that looks like this:

```
[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164,
224, 165, 135]
```

That’s 18 bytes and is how computers ultimately store this data. If we look at them as Unicode scalar values, which are what Rust’s char type is, those bytes look like this:

```
['न', 'म', 'स', '्', 'त', 'े']
```

There are six char values here, but the fourth and sixth are not letters: they’re diacritics that don’t make sense on their own. Finally, if we look at them as grapheme clusters, we’d get what a person would call the four letters that make up the Hindi word:

```
["न", "म", "स्", "ते"]
```

The type HashMap<K, V> stores a mapping of keys of type K to values of type V. It does this via a hashing function, which determines how it places these keys and values into memory. Like vectors, hash maps are homogeneous: all of the keys must have the same type, and all of the values must have the same type.

Error Handling
--------------

Rust groups errors into two major categories: recoverable and unrecoverable errors. Unrecoverable errors are always symptoms of bugs. Rust doesn’t have exceptions. Instead, it has the type Result<T, E> for recoverable errors and the panic! macro that stops execution when the program encounters an unrecoverable error.

When the panic! macro executes, the program will print a failure message, unwind and clean up the stack, and then quit (alternatively, abort strategy might be configured to optimize binary size, leaving the responsibility to clean up the stack up to OS).

Result enum definition:

```
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Error values that have the ? operator called on them go through the from function, defined in the From trait in the standard library, which is used to convert errors from one type into another. The ? operator can be used in functions that have a return type of Result.

Panic when it’s possible that code could end up in a bad state - a bad state is when some assumption, guarantee, contract, or invariant has been broken, such as when invalid values, contradictory values, or missing values are passed to your code—plus one or:

-	The bad state is not something that’s expected to happen occasionally.
-	Your code after this point needs to rely on not being in this bad state.
-	There’s not a good way to encode this information in the types you use.

The panic! macro signals that your program is in a state it can’t handle and lets you tell the process to stop instead of trying to proceed with invalid or incorrect values. The Result enum uses Rust’s type system to indicate that operations might fail in a way that your code could recover from - use Result to tell code that calls your code that it needs to handle potential success or failure as well.

Generic Types, Traits, and Lifetimes
------------------------------------

Generics is a tool for effectively handling the duplication of concepts. Generics are abstract stand-ins for concrete types or other properties.

```
fn largest<T>(list: &[T]) -> T {
```

The function largest is generic over some type T. This function has one parameter named list, which is a slice of values of type T. The largest function will return a value of the same type T. Similarly, generic struct definition:

```
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}
```

Rust implements generics in such a way that your code doesn’t run any slower using generic types than it would with concrete types by performing monomorphization of the code that is using generics at compile time. Monomorphization is the process of turning generic code into specific code by filling in the concrete types that are used when compiled.

A trait tells the Rust compiler about functionality a particular type has and can share with other types so traits are used to define shared behavior in an abstract way. We can use trait bounds to specify that a generic can be any type that has certain behavior. Traits are similar to a feature often called interfaces in other languages, although with some differences.

It is possible to implement a trait on a type only if either the trait or the type is local to our crate. This restriction is there to ensure coherence - specifically the orphan rule, so named because the parent type is not present. This rule ensures that other people’s code can’t break your code and vice versa.

Train can be used as function signature parameter:

```
pub fn notify(item: impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
// more general form, called trait bound
pub fn notify<T: Summary>(item1: T, item2: T) {
    ...
}
// parameter requires multiple traits to be implemented
pub fn notify<T: Summary + Display>(item: T) {
    ...
}
// the as above using where to "de-clutter" the signature
fn some_function<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
```

Blanked implementation is conditionally implementation of a trait for any type that implements another trait.

```
// method ToString is defined for an type which implements Display trait
impl<T: Display> ToString for T {
    // --snip--
}
```

Lifetimes ensure that references are valid as long as we need them to be.

```
// illustration of borrow checker view on variable's lifetime
{
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
                          //          |
    println!("r: {}", r); //          |
}
// syntax
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```

The lifetime elision rules are patterns programmed into Rust’s analysis of references:

1.	each parameter that is a reference gets its own lifetime parameter.
2.	if there is exactly one input lifetime parameter, that lifetime is assigned to all output lifetime parameters.
3.	if there are multiple input lifetime parameters, but one of them is &self or &mut self because this is a method, the lifetime of self is assigned to all output lifetime parameters.

Special lifetime 'static means that this reference can live for the entire duration of the program.

Combining all together:

```
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(x: &'a str, y: &'a str, ann: T) -> &'a str
    where T: Display
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

Writing Automated Tests
-----------------------

Anatomy of test declaration:

```
// run with cargo test
#[cfg(test)]
mod tests {
    // use super to bring upper scope definition into test scope
    use super::*;
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);

        let val = 2 + 2;
        assert_ne!(val, 5, "Value `{}` should not be equal to 5", val);
    }

    // test function panic with expected message
    #[should_panic(expected = "Guess value must be less than or equal to 100")]
    fn greater_than_100() {
        Guess::new(200);
    }
    // test fails if err is returned
    #[test]
    fn it_works_using_err() -> Result<(), String> {
        if 2 + 2 == 4 {
            Ok(())
        } else {
            Err(String::from("two plus two does not equal four"))
        }
    }
}
```

By default, test runner executes multiple tests in parallel using threads (shared state might thus break the tests - shared enviroment, working directory state, ...). Rust’s test library captures anything printed to standard output for passing test.

Running particular subset of test can be done by:

-	specifying the test name `cargo test it_works`
-	specifying sub string `cargo test works`

Unit tests are by convention placed in the src directory in each file with the code that they’re testing in module named tests by providing annotation to the module `cfg(test)`.

Integration tests are entirely external to your library - they use your library in the same way any other code would, which means they can only call functions that are part of your library’s public API. They are placed by convention to tests directory at the top level of our project directory, next to src. Each file in the tests directory is compiled as its own separate crate. Common test util functions can be shared by placing Rust module within the test directory as `tests/common/mod.rs` (Rust understand that it is not module with integration tests).

Building a Command Line Program
-------------------------------

The responsibilities that remain in the main function should be limited to the following:

-	Calling the command line parsing logic with the argument values
-	Setting up any other configuration
-	Calling a run function in lib.rs
-	Handling the error if run returns an error

The motivation behind is separating the concerns: main.rs handles running the program, and lib.rs handles all the logic of the task at hand.

Iterators and Closures
----------------------

Rust’s closures are anonymous functions you can save in a variable or pass as arguments to other functions. Closures don’t require type annotations of the parameters or the return value like fn functions do because they are not part of explicit interface exposed to users - closures are usually short and relevant only within a narrow context rather than in any arbitrary scenario. Thus the compiler is reliably able to infer the types of the parameters and the return type, similar to how it’s able to infer the types of most variables.

```
// type annotations are optional - just for explicitness
let closure = |param1: &str, param2: &str| -> () {
    println!("Params: {} {}", param1, param2);
};
// comparison of declarations (all are valid)
fn  add_one_v1   (x: u32) -> u32 { x + 1 }
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

Closure types are infered once by the compiler so attemp to call closure with different types result in compiler error.

Example of implementing cached closure (memoization/lazy evaluation):

```
struct Cacher<T, U>
{
    calculation: T,
    cache: HashMap<U, U>,
}

impl<T, U> Cacher<T, U>
where
    T: Fn(U) -> U,
    U: Hash + Eq + Copy,
{
    fn new(calculation: T) -> Cacher<T, U> {
        Cacher {
            calculation,
            cache: HashMap::new(),
        }
    }

    fn value(&mut self, arg: U) -> U {
        match self.cache.get(&arg) {
            Some(v) => *v,
            None => {
                let v = (self.calculation)(arg);
                self.cache.insert(arg, v);
                v
            }
        }
    }
}
```

Closure can capture values from environment:

```
let x = 4;
let closure = |z| z == x;

```

When a closure captures a value from its environment, it uses memory to store the values for use in the closure body. The capture can have 3 forms:

-	FnOnce consumes the variables it captures from its enclosing scope, known as the closure’s environment. To consume the captured variables, the closure must take ownership of these variables and move them into the closure when it is defined. The Once part of the name represents the fact that the closure can’t take ownership of the same variables more than once, so it can be called only once.
-	FnMut can change the environment because it mutably borrows values.
-	Fn borrows values from the environment immutably

In Rust, iterators are lazy, meaning they have no effect until you call methods that consume the iterator to use it up.

Methods that call next are called consuming adaptors, because calling them uses up the iterator. Other methods defined on the Iterator trait, known as iterator adaptors, allow you to change iterators into different kinds of iterators. You can chain multiple calls to iterator adaptors to perform complex actions in a readable way. But because all iterators are lazy, you have to call one of the consuming adaptor methods to get results from calls to iterator adaptors.

Iterators are implemented as zero-cost abstractions - there is no performance penalty compared to hand written low level code.

> In general, C++ implementations obey the zero-overhead principle: What you don’t use, you don’t pay for. And further: What you do use, you couldn’t hand code any better.

Smart Pointers
--------------

Smart pointers are data structures that not only act like a pointer but also have additional metadata and capabilities. Smart pointers are usually implemented using structs. The characteristic that distinguishes a smart pointer from an ordinary struct is that smart pointers implement the Deref and Drop traits.

Most common smart pointers in the standard library:

-	Box<T> for allocating values on the heap
-	Rc<T>, a reference counting type that enables multiple ownership
-	Ref<T> and RefMut<T>, accessed through RefCell<T>, a type that enforces the borrowing rules at runtime instead of compile time

Boxes allow you to store data on the heap rather than the stack. Used when:

-	When you have a type whose size can’t be known at compile time and you want to use a value of that type in a context that requires an exact size
-	When you have a large amount of data and you want to transfer ownership but ensure the data won’t be copied when you do so
-	When you want to own a value and you care only that it’s a type that implements a particular trait rather than being of a specific type

Rust needs to know how much space a type takes up at compile time -- one type whose size can’t be known at compile time is a recursive type, where a value can have as part of itself another value of the same type.

Rust does deref coercion when it finds types and trait implementations in three cases:

-	From &T to &U when T: Deref<Target=U>
-	From &mut T to &mut U when T: DerefMut<Target=U>
-	From &mut T to &U when T: Deref<Target=U>

To enable multiple ownership, Rust has a type called Rc<T> - reference counting. The Rc<T> type keeps track of the number of references to a value which determines whether or not a value is still in use. If there are zero references to a value, the value can be cleaned up without any references becoming invalid. Rc<T> is only for use in single-threaded scenarios.

Interior mutability is a design pattern in Rust that allows you to mutate data even when there are immutable references to that data; normally, this action is disallowed by the borrowing rules - RefCell<T> is one of types that follows the interior mutability pattern.

The RefCell<T> type is useful when you’re sure your code follows the borrowing rules but the compiler is unable to understand and guarantee that. Similar to Rc<T>, RefCell<T> is only for use in single-threaded scenarios and will give you a compile-time error if you try using it in a multithreaded context.

Recap on smart pointers for single threaded apps::

-	Rc<T> enables multiple owners of the same data; Box<T> and RefCell<T> have single owners.
-	Box<T> allows immutable or mutable borrows checked at compile time; Rc<T> allows only immutable borrows checked at compile time; RefCell<T> allows immutable or mutable borrows checked at runtime.
-	Because RefCell<T> allows mutable borrows checked at runtime, you can mutate the value inside the RefCell<T> even when the RefCell<T> is immutable.

A test double is the general programming concept for a type used in place of another type during testing. Mock objects are specific types of test doubles that record what happens during a test so you can assert that the correct actions took place.

A common way to use RefCell<T> is in combination with Rc<T>. If you have an Rc<T> that holds a RefCell<T>, you can get a value that can have multiple owners and that you can mutate.

Fearless Concurrency
--------------------

Because threads can run simultaneously, there’s no inherent guarantee about the order in which parts of your code on different threads will run. This can lead to following problems:

-	Race conditions, where threads are accessing data or resources in an inconsistent order
-	Deadlocks, where two threads are waiting for each other to finish using a resource the other thread has, preventing both threads from continuing
-	Bugs that happen only in certain situations and are hard to reproduce and fix reliably

Programming languages implement threads in a few different ways:

-	a language calls the operating system APIs to create threads is sometimes called 1:1, meaning one operating system thread per one language thread.
-	a language provides own special implementation of threads. Programming language-provided threads are known as green threads, and languages that use these green threads will execute them in the context of a different number of operating system threads. For this reason, the green-threaded model is called the M:N model: there are M green threads per N operating system threads, where M and N are not necessarily the same number.

The Rust standard library only provides an implementation of 1:1 threading for performance reasons.

```
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(move || {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

Message passing, where threads or actors communicate by sending each other messages containing data, is implemented in Rust using the channel.

```
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
    });

    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

Shared memory concurrency, where multiple threads can access the same memory location at the same time, is another model for concurrent programming. In sense, it is very similar to multiple ownership. One tool alowing shared memory concurrency is mutex.

Mutexes have a reputation for being difficult to use because you have to remember two rules:

-	You must attempt to acquire the lock before using the data.
-	When you’re done with the data that the mutex guards, you must unlock the data so other threads can acquire the lock.

However, thanks to Rust’s type system and ownership rules, you can’t get locking and unlocking wrong.

```
use std::sync::{Mutex, Arc};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

Arc<T> is a type like Rc<T> that is safe to use in concurrent situations. Mutex<T> provides interior mutability, as the Cell family does. In the same way RefCell<T> allows us to mutate contents inside an Rc<T>, we use Mutex<T> to mutate contents inside an Arc<T>.
