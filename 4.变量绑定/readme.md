## 下划线消除警告

```
let tuple = (1, 2, 3);
let (first, _second, _third) = tuple;
// 这里只使用了 first 变量，_second 和 _third 不会触发警告

1、对未使用的变量前增加下划线，可以消除警告
```

## mut关键字

```rust
1、没有使用mut修饰的变量，重新修改值会报错
fn main() {
    let _immutable_binding = 1;
    let mut mutable_binding = 1;

    println!("Before mutation: {}", mutable_binding);

    // Ok
    mutable_binding += 1;

    println!("After mutation: {}", mutable_binding);

    // Error! Cannot assign a new value to an immutable variable
    _immutable_binding += 1;
}
```

## 块

```rust
大括号表示block，会有作用域的限制。示例:
fn main() {
    // This binding lives in the main function
    let long_lived_binding = 1;

    // This is a block, and has a smaller scope than the main function
    {
        // This binding only exists in this block
        let short_lived_binding = 2;

        println!("inner short: {}", short_lived_binding);
    }
    // End of the block

    // Error! `short_lived_binding` doesn't exist in this scope
    println!("outer short: {}", short_lived_binding);
    // FIXME ^ Comment out this line

    println!("outer long: {}", long_lived_binding);
}
```

## 变量遮蔽

```rust
1、块内修改变量的类型不会影响到块外。示例:
fn main() {
    let shadowed_binding = 1;

    {
        println!("before being shadowed: {}", shadowed_binding);

        // This binding *shadows* the outer one
        let shadowed_binding = "abc";

        println!("shadowed in inner block: {}", shadowed_binding);
    }
    println!("outside inner block: {}", shadowed_binding);

    // This binding *shadows* the previous binding
    let shadowed_binding = 2;
    println!("shadowed in outer block: {}", shadowed_binding);
}

输出：
before being shadowed: 1
shadowed in inner block: abc
outside inner block: 1
shadowed in outer block: 2
2、注意：当块内改变块外变量值的时候，块外可以看见。如
fn main() {
    // Declare a variable binding
    let a_binding;

    {
        let x = 2;

        // Initialize the binding
        a_binding = x * x;
    }

    println!("a binding: {}", a_binding);
}
输出：
a binding: 4
3、因为变量遮蔽相当于块内重新定义了一个相同名字的变量。
```

## 变量初始化和使用

```rust
1、变量未初始化就使用会报错。如：

fn main() {
    let another_binding;

    // Error! Use of uninitialized binding
    println!("another binding: {}", another_binding);
    // FIXME ^ Comment out this line

}
```

## 冻结

```rust
当可变的变量重新赋值给不可变的变量，并且名字相同。可变变量就变成不可以重新赋值，直到超出作用域，才可以重新赋值。示例：
fn main() {
    let mut _mutable_integer = 7i32;

    {
        // Shadowing by immutable `_mutable_integer`
        let _mutable_integer = _mutable_integer;

        // Error! `_mutable_integer` is frozen in this scope
        _mutable_integer = 50;
        // FIXME ^ Comment out this line

        // `_mutable_integer` goes out of scope
    }

    // Ok! `_mutable_integer` is not frozen in this scope
    _mutable_integer = 3;
}
```







