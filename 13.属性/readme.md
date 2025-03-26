## 属性

```rust
1、两种
#[outer_attribute]：示例
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}


#![inner_attribute]：示例
#![allow(unused_variables)]

fn main() {
    let x = 3; // This would normally warn about an unused variable.
}

2、属性参数语法
#[attribute = "value"]
#[attribute(key = "value")]
#[attribute(value)]

可以可以有多个。示例
#[attribute(value, value2)]


#[attribute(value, value2, value3,
            value4, value5)]

```

## 内部属性和外部属性的区别

```rust
1、语法区别：#[outer_attribute] 是外部属性，紧跟在要修饰的项之前；#![inner_attribute] 是内部属性，通常位于模块或者 crate 的开头。
2、作用范围区别：#[outer_attribute] 作用于紧跟其后的单个项；#![inner_attribute] 作用于包含它的整个模块或者 crate。
```

## dead_code

```rust
禁用未使用代码检查。示例：
fn used_function() {}

// `#[allow(dead_code)]` is an attribute that disables the `dead_code` lint
#[allow(dead_code)]
fn unused_function() {}

fn noisy_unused_function() {}
// FIXME ^ Add an attribute to suppress the warning

fn main() {
    used_function();
}
```

## crate_name 与 crate_type

```rust
示例：
// This crate is a library
#![crate_type = "lib"]
// The library is named "rary"
#![crate_name = "rary"]

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


当设置这个属性后，命令行允许命令的时候就可以取消参数，示例：
$ rustc lib.rs
$ ls lib*
library.rlib

```

## cfg 与 cfg!

```rust
1、the cfg attribute: #[cfg(...)] in attribute position
the cfg! macro: cfg!(...) in boolean expressions

示例:
// This function only gets compiled if the target OS is linux
#[cfg(target_os = "linux")]
fn are_you_on_linux() {
    println!("You are running linux!");
}

// And this function only gets compiled if the target OS is *not* linux
#[cfg(not(target_os = "linux"))]
fn are_you_on_linux() {
    println!("You are *not* running linux!");
}

fn main() {
    are_you_on_linux();

    println!("Are you sure?");
    if cfg!(target_os = "linux") {
        println!("Yes. It's definitely linux!");
    } else {
        println!("Yes. It's definitely *not* linux!");
    }
}

2、自定义
#[cfg(some_condition)]
fn conditional_function() {
    println!("condition met!");
}

fn main() {
    conditional_function();
}

这种自定义的参数，执行编译命令的时候必须添加。示例：
$ rustc --cfg some_condition custom.rs && ./custom
condition met!

```

