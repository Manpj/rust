## Crate

```rust
1、简介：
crate是基本的编译单元
2、crate可以被编译成两种类型的目标。
二进制文件
库文件
```

## 创建crate

```rust
rary.rs:
pub fn public_function() {
    println!("called rary's `public_function()`");
}

fn private_function() {
    println!("called rary's `private_function()`");
}

pub fn indirect_access() {
    print!("called rary's `indirect_access()`, that\n> ");

    private_function();
}
创建命令
$ rustc --crate-type=lib rary.rs
$ ls lib*
library.rlib

--crate-name  参数可以修改名字，不带的话使用默认名字

```

## 使用library

```rust
// extern crate rary; // May be required for Rust 2015 edition or earlier

fn main() {
    rary::public_function();

    // Error! `private_function` is private
    //rary::private_function();

    rary::indirect_access();
}

# Where library.rlib is the path to the compiled library, assumed that it's
# in the same directory here:
$ rustc executable.rs --extern rary=library.rlib && ./executable 
called rary's `public_function()`
called rary's `indirect_access()`, that
> called rary's `private_function()`

```

