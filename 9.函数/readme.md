## 函数

```rust
1、函数的定义顺序，不像c c++一样有函数的定义顺序限制，rust没有函数的定义顺序限制。示例：
// Unlike C/C++, there's no restriction on the order of function definitions
fn main() {
    // We can use this function here, and define it somewhere later
    fizzbuzz_to(100);
}

// Function that returns a boolean value
fn is_divisible_by(lhs: u32, rhs: u32) -> bool {
    // Corner case, early return
    if rhs == 0 {
        return false;
    }

    // This is an expression, the `return` keyword is not necessary here
    lhs % rhs == 0
}

// Functions that "don't" return a value, actually return the unit type `()`
fn fizzbuzz(n: u32) -> () {
    if is_divisible_by(n, 15) {
        println!("fizzbuzz");
    } else if is_divisible_by(n, 3) {
        println!("fizz");
    } else if is_divisible_by(n, 5) {
        println!("buzz");
    } else {
        println!("{}", n);
    }
}

// When a function returns `()`, the return type can be omitted from the
// signature
fn fizzbuzz_to(n: u32) {
    for n in 1..=n {
        fizzbuzz(n);
    }
}
```

## self

```rust
1、`self` desugars to `self: Self`。self 实际上是 self: Self 的语法糖
2、解释：
self 代表调用该方法的实例对象。通过 self，方法能够访问和操作实例的字段以及调用其他方法。
Self 是一个类型别名，它代表实现该方法的类型本身。比如，在 impl Pair 块中，Self 就代表 Pair 类型。示例：
impl Pair {
    // This method "consumes" the resources of the caller object
    // `self` desugars to `self: Self`
    fn destroy(self) {
        // Destructure `self`
        let Pair(first, second) = self;

        println!("Destroying Pair({}, {})", first, second);

        // `first` and `second` go out of scope and get freed
    }
}
```

## 关联函数和方法的区别

```rust
1、关联函数是定义在类型上的函数，不依赖于该类型的具体实例。调用关联函数时，需要使用类型名加双冒号 :: 的方式。示例：
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // 关联函数：创建一个正方形的 Rectangle 实例
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}

fn main() {
    // 调用关联函数创建实例
    let sq = Rectangle::square(5);
    println!("Square width: {}, height: {}", sq.width, sq.height);
}
2、方法是与类型的具体实例相关联的函数，需要通过类型的实例来调用，使用实例名加点号 . 的方式。方法的第一个参数通常是 self，用于表示调用该方法的实例。示例：
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // 方法：计算矩形的面积
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect = Rectangle {
        width: 3,
        height: 4,
    };
    // 调用方法
    let area = rect.area();
    println!("Rectangle area: {}", area);
}
```

## 关联函数和方法示例

```rust
struct Point {
    x: f64,
    y: f64,
}

// Implementation block, all `Point` associated functions & methods go in here
impl Point {
    // This is an "associated function" because this function is associated with
    // a particular type, that is, Point.
    //
    // Associated functions don't need to be called with an instance.
    // These functions are generally used like constructors.
    fn origin() -> Point {
        Point { x: 0.0, y: 0.0 }
    }

    // Another associated function, taking two arguments:
    fn new(x: f64, y: f64) -> Point {
        Point { x: x, y: y }
    }
}

struct Rectangle {
    p1: Point,
    p2: Point,
}

impl Rectangle {
    // This is a method
    // `&self` is sugar for `self: &Self`, where `Self` is the type of the
    // caller object. In this case `Self` = `Rectangle`
    fn area(&self) -> f64 {
        // `self` gives access to the struct fields via the dot operator
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        // `abs` is a `f64` method that returns the absolute value of the
        // caller
        ((x1 - x2) * (y1 - y2)).abs()
    }

    fn perimeter(&self) -> f64 {
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        2.0 * ((x1 - x2).abs() + (y1 - y2).abs())
    }

    // This method requires the caller object to be mutable
    // `&mut self` desugars to `self: &mut Self`
    fn translate(&mut self, x: f64, y: f64) {
        self.p1.x += x;
        self.p2.x += x;

        self.p1.y += y;
        self.p2.y += y;
    }
}

// `Pair` owns resources: two heap allocated integers
struct Pair(Box<i32>, Box<i32>);

impl Pair {
    // This method "consumes" the resources of the caller object
    // `self` desugars to `self: Self`
    fn destroy(self) {
        // Destructure `self`
        let Pair(first, second) = self;

        println!("Destroying Pair({}, {})", first, second);

        // `first` and `second` go out of scope and get freed
    }
}

fn main() {
    let rectangle = Rectangle {
        // Associated functions are called using double colons
        p1: Point::origin(),
        p2: Point::new(3.0, 4.0),
    };

    // Methods are called using the dot operator
    // Note that the first argument `&self` is implicitly passed, i.e.
    // `rectangle.perimeter()` === `Rectangle::perimeter(&rectangle)`
    println!("Rectangle perimeter: {}", rectangle.perimeter());
    println!("Rectangle area: {}", rectangle.area());

    let mut square = Rectangle {
        p1: Point::origin(),
        p2: Point::new(1.0, 1.0),
    };

    // Error! `rectangle` is immutable, but this method requires a mutable
    // object
    //rectangle.translate(1.0, 0.0);
    // TODO ^ Try uncommenting this line

    // Okay! Mutable objects can call mutable methods
    square.translate(1.0, 1.0);

    let pair = Pair(Box::new(1), Box::new(2));

    pair.destroy();

    // Error! Previous `destroy` call "consumed" `pair`
    //pair.destroy();
    // TODO ^ Try uncommenting this line
}

注：
pair.destroy();使用一次后，再次使用pair示例会报错。
```

## 初识闭包

```rust
1、概念
闭包是一种可以捕获其周围环境变量的函数。即闭包不仅可以访问传递给它的参数，还可以访问定义它的作用域中的变量。使得闭包在处理一些需要访问外部状态的场景时非常方便。
示例：
fn main() {
    let x = 5;
    // 定义一个闭包，捕获外部变量 x
    let add_x = |val| val + x;
    let result = add_x(3);
    println!("Result: {}", result);
}
解释：let add_x = |val| val + x;
其中x即捕获的作用域中的变量，val是传递给闭包的参数。这个闭包的含义是这两个数相加。
2、语法
>使用||包围输入的变量，而不是像普通函数那样使用（）。例如：|val|表示闭包接受一个参数val。
>闭包返回类型可以由编译器自动推断，不需要显示指定。
>函数体：如果闭包的函数体只有一行表达式，可以省略{}。如果函数体包含多行语句，则必须使用{}来包围。如：
let print_and_square = |x| {
    println!("Input: {}", x);
    x * x
};
3、调用方式
调用闭包的方式和调用普通函数相同。
4、捕获外部环境变量的方式
>不可变引用。这种方式在闭包内部只能读取该变量，不能修改。
>可变引用。闭包可以可变的借用外部变量，需要在闭包定义前加上mut关键字。如：
fn main() {
    let mut y = 10;
    let mut increment_y = || {
        y += 1;
        println!("y: {}", y);
    };
    increment_y();
}
>所有权转移。如果闭包需要拥有外部变量的所有权，可以使用move关键字。如：
fn main() {
    let z = vec![1, 2, 3];
    let print_z = move || {
        println!("z: {:?}", z);
    };
    print_z();
    // 下面这行代码会报错，因为 z 的所有权已经转移到闭包中
    // println!("{:?}", z);
}

整体示例：
fn main() {
    let outer_var = 42;
    
    // A regular function can't refer to variables in the enclosing environment
    //fn function(i: i32) -> i32 { i + outer_var }
    // TODO: uncomment the line above and see the compiler error. The compiler
    // suggests that we define a closure instead.

    // Closures are anonymous, here we are binding them to references.
    // Annotation is identical to function annotation but is optional
    // as are the `{}` wrapping the body. These nameless functions
    // are assigned to appropriately named variables.
    let closure_annotated = |i: i32| -> i32 { i + outer_var };
    let closure_inferred  = |i     |          i + outer_var  ;

    // Call the closures.
    println!("closure_annotated: {}", closure_annotated(1));
    println!("closure_inferred: {}", closure_inferred(1));
    // Once closure's type has been inferred, it cannot be inferred again with another type.
    //println!("cannot reuse closure_inferred with another type: {}", closure_inferred(42i64));
    // TODO: uncomment the line above and see the compiler error.

    // A closure taking no arguments which returns an `i32`.
    // The return type is inferred.
    let one = || 1;
    println!("closure returning one: {}", one());

}
注：
println!("cannot reuse closure_inferred with another type: {}", closure_inferred(42i64));
这部分会报错，推断成功后再传入其他类型会报错。如:
let outer_var = 42;
let closure_inferred  = |i     |          i + outer_var  ;
println!("cannot reuse closure_inferred with another type: {}", closure_inferred(42i64));
println!("cannot reuse closure_inferred with another type: {}", closure_inferred(42i32));
第二个输出会报错。
```

## 闭包通过可变引用特殊情况

```rust
1、闭包通过可变引用捕获变量的情况下，只要闭包还可能被调用，变量就不能被再次被借用。因为同一时间不能同时存在可变引用和其他引用（可变或不可变），以确保Rust的内存安全和借用规则。
示例：
fn main() {
    let mut count = 0;
    let mut inc = || {
        count += 1;
        println!("`count`: {}", count);
    };
    inc();
    // 尝试不可变借用 count，会导致编译错误
    let _reborrow = &count; //这行会报错
    inc();
    let _reborrow = &count; //这行不会报错，因为inc()闭包在后面程序中没有再使用
}
当你尝试编译这段代码时，编译器会给出错误信息，提示你不能在闭包持有可变引用的情况下再次借用 count。这就是 “An attempt to reborrow will lead to an error.” 这句话的含义。因为inc()闭包被再次调用。
```

## Box

```rust
1、Box是rust中的智能指针，用于在堆上分配内存。
2、Box::new(3)创建一个Box<i32>类型的实例，它包含一个整数值3。
3、Box类型不是copy类型，因为它管理着堆上的内存资源，复制它可能会导致多个指针指向同一块内存，从而引发内存呢安全问题
```

## mem

```rust
mem::drop(movable)，mem::drop 函数的参数类型是 T，意味着它需要获取变量的所有权。因此，闭包 consume 会通过值的方式捕获 movable。对于非复制类型，这意味着变量的所有权会被移动到闭包内部。
// `mem::drop` requires `T` so this must take by value. A copy type
// would copy into the closure leaving the original untouched.
// A non-copy must move and so `movable` immediately moves into
// the closure.
let consume = || {
    println!("`movable`: {:?}", movable);
    mem::drop(movable);
};
如果是
let consume = || {
    println!("`movable`: {:?}", movable);
    mem::drop(&movable);
};
这样，就可以多次调用consume。
```

## 闭包捕获示例

```rust
fn main() {
    use std::mem;
    
    let color = String::from("green");

    // A closure to print `color` which immediately borrows (`&`) `color` and
    // stores the borrow and closure in the `print` variable. It will remain
    // borrowed until `print` is used the last time. 
    //
    // `println!` only requires arguments by immutable reference so it doesn't
    // impose anything more restrictive.
    let print = || println!("`color`: {}", color);

    // Call the closure using the borrow.
    print();

    // `color` can be borrowed immutably again, because the closure only holds
    // an immutable reference to `color`. 
    let _reborrow = &color;
    print();

    // A move or reborrow is allowed after the final use of `print`
    let _color_moved = color;


    let mut count = 0;
    // A closure to increment `count` could take either `&mut count` or `count`
    // but `&mut count` is less restrictive so it takes that. Immediately
    // borrows `count`.
    //
    // A `mut` is required on `inc` because a `&mut` is stored inside. Thus,
    // calling the closure mutates `count` which requires a `mut`.
    let mut inc = || {
        count += 1;
        println!("`count`: {}", count);
    };

    // Call the closure using a mutable borrow.
    inc();

    // The closure still mutably borrows `count` because it is called later.
    // An attempt to reborrow will lead to an error.
    // let _reborrow = &count; 
    // ^ TODO: try uncommenting this line.
    inc();

    // The closure no longer needs to borrow `&mut count`. Therefore, it is
    // possible to reborrow without an error
    let _count_reborrowed = &mut count; 

    
    // A non-copy type.
    let movable = Box::new(3);

    // `mem::drop` requires `T` so this must take by value. A copy type
    // would copy into the closure leaving the original untouched.
    // A non-copy must move and so `movable` immediately moves into
    // the closure.
    let consume = || {
        println!("`movable`: {:?}", movable);
        mem::drop(movable);
    };

    // `consume` consumes the variable so this can only be called once.
    consume();
    // consume();
    // ^ TODO: Try uncommenting this line.
}
```

```rust
fn main() {
    // `Vec` has non-copy semantics.
    let haystack = vec![1, 2, 3];

    let contains = move |needle| haystack.contains(needle);

    println!("{}", contains(&1));
    println!("{}", contains(&4));

    // println!("There're {} elements in vec", haystack.len());
    // ^ Uncommenting above line will result in compile-time error
    // because borrow checker doesn't allow re-using variable after it
    // has been moved.
    
    // Removing `move` from closure's signature will cause closure
    // to borrow _haystack_ variable immutably, hence _haystack_ is still
    // available and uncommenting above line will not cause an error.
}
```

## where关键字

```rust
1、where关键字用于为泛型类型参数指定约束条件，它能让代码的类型约束表达更加清晰和灵活，特别实在约束条件较为复杂时优势明显。
示例：
// A function which takes a closure as an argument and calls it.
// <F> denotes that F is a "Generic type parameter"
fn apply<F>(f: F) where
    // The closure takes no input and returns nothing.
    F: FnOnce() {
    // ^ TODO: Try changing this to `Fn` or `FnMut`.

    f();
}
where 子句：where F: FnOnce() 为泛型类型参数 F 设定了约束条件。它要求 F 必须实现 FnOnce() 特征。FnOnce() 是一个闭包特征，代表该闭包不接受任何参数，也不返回任何值，并且闭包可以获取捕获变量的所有权（也就是通过值捕获）

// A function which takes a closure and returns an `i32`.
fn apply_to_3<F>(f: F) -> i32 where
    // The closure takes an `i32` and returns an `i32`.
    F: Fn(i32) -> i32 {

    f(3)
}
where 的作用：借助 where 子句，我们清晰地规定了传入 apply 函数的参数 f 必须是一个满足 FnOnce() 特征的闭包。如果不使用 where 子句，也可以把约束直接写在泛型类型参数后面，像 fn apply<F: FnOnce()>(f: F) 这样，但当约束条件变复杂时，使用 where 子句能让代码更易读。
```

## 字符串操作

```rust
1、let mut farewell = "goodbye".to_owned();
解释：
>"goodbye"：这是一个字符串字面量，在 Rust 里，字符串字面量的类型是 &'static str。它是一种不可变的字符串切片，存储在程序的只读内存区域，拥有静态生命周期（'static）.
>.to_owned()：这是 str 类型提供的一个方法，其作用是将一个不可变的字符串切片（&str）转换为一个拥有所有权的 String 类型。String 类型是可增长的、可变的字符串，它会在堆上分配内存来存储字符串数据.
>let mut farewell：这里使用 let 关键字声明了一个变量 farewell，并且使用 mut 关键字将其标记为可变的。这意味着后续可以对 farewell 进行修改操作.
2、farewell.push_str("!!!");
>push_str 方法：这是 String 类型提供的一个方法，用于在 String 类型的字符串末尾追加一个字符串切片（&str）。push_str 方法不会获取传入字符串切片的所有权，只是将其内容复制到 String 类型的字符串末尾。
>"!!!"：这是一个字符串字面量，类型为 &'static str，作为参数传递给 push_str 方法。
```

## &mut

```rust
表示可变引用
```

## 函数使用闭包作为参数  与 Fn、FnMut、FnOnce特性

```rust
1、函数使用闭包作为参数
示例：
// A function which takes a closure as an argument and calls it.
// <F> denotes that F is a "Generic type parameter"
fn apply<F>(mut f: F) where
    // The closure takes no input and returns nothing.
    F: FnMut() {
    // ^ TODO: Try changing this to `Fn` or `FnMut`.

    f();
}

// A function which takes a closure and returns an `i32`.
fn apply_to_3<F>(f: F) -> i32 where
    // The closure takes an `i32` and returns an `i32`.
    F: Fn(i32) -> i32 {

    f(3)
}

fn main() {
    use std::mem;

    let greeting = "hello";
    // A non-copy type.
    // `to_owned` creates owned data from borrowed one
    let mut farewell = "goodbye".to_owned();

    // Capture 2 variables: `greeting` by reference and
    // `farewell` by value.
    let mut diary = || {
        // `greeting` is by reference: requires `Fn`.
        println!("I said {}.", greeting);

        // Mutation forces `farewell` to be captured by
        // mutable reference. Now requires `FnMut`.
        farewell.push_str("!!!");
        println!("Then I screamed {}.", farewell);
        println!("Now I can sleep. zzzzz");

        // Manually calling drop forces `farewell` to
        // be captured by value. Now requires `FnOnce`.
        mem::drop(&farewell);
    };

    // Call the function which applies the closure.
    apply(&mut diary);

    // `double` satisfies `apply_to_3`'s trait bound
    let double = |x| 2 * x;

    println!("3 doubled: {}", apply_to_3(double));
}
2、Fn、FnMut、FnOnce
>Fn：当一个闭包实现了 Fn 特征时，它通过不可变引用（&T）来使用捕获的变量。这意味着闭包可以读取捕获的变量，但不能修改它们。
>FnMut:实现 FnMut 特征的闭包通过可变引用（&mut T）来使用捕获的变量。这意味着闭包可以修改捕获的变量.
>FnOnce:实现 FnOnce 特征的闭包通过值（T）来使用捕获的变量。这意味着闭包会获取捕获变量的所有权。
```

## 函数也可输入作为参数

```rust
函数也可以和闭包一样，作为函数的入参。示例：
// Define a function which takes a generic `F` argument
// bounded by `Fn`, and calls it
fn call_me<F: Fn()>(f: F) {
    f();
}

// Define a wrapper function satisfying the `Fn` bound
fn function() {
    println!("I'm a function!");
}

fn main() {
    // Define a closure satisfying the `Fn` bound
    let closure = || println!("I'm a closure!");

    call_me(closure);
    call_me(function);
}
```

## Fn、FnMut、FnOnce作为输出类型

```rust
1、必须使用move，获取所有权。保证闭包的独立性。
当函数返回闭包时，闭包可能会在函数返回之后才被调用。要是闭包以引用的方式捕获外部变量，那么当函数返回时，外部变量可能已经离开了作用域，从而导致悬垂引用。借助 move 关键字，闭包能够获取外部变量的所有权，这样即便外部作用域结束，闭包依然可以安全地使用这些变量。
示例：
fn create_fn() -> impl Fn() {
    let text = "Fn".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnmut() -> impl FnMut() {
    let text = "FnMut".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnonce() -> impl FnOnce() {
    let text = "FnOnce".to_owned();

    move || println!("This is a: {}", text)
}

fn main() {
    let fn_plain = create_fn();
    let mut fn_mut = create_fnmut();
    let fn_once = create_fnonce();

    fn_plain();
    fn_mut();
    fn_once();
}
输出：
This is a: Fn
This is a: FnMut
This is a: FnOnce
```

## Vec<T>

```rust
1、它是一个动态数组，也被叫做可增长数组或者向量.
Vec<T> 是一个标准库类型，其作用是在堆上存储一系列相同类型的元素。它的大小可以在运行时动态改变，这意味着你能够随时添加或者移除元素。
2、vec1.iter() 借用元素，不会获取元素的所有权
3、vec2.into_iter() 直接获取值，会获取所有权
```

## Iterator

```rust
Iterator源码中使用的any方法，支持传入闭包。源码如下：
pub trait Iterator {
    // The type being iterated over.
    type Item;

    // `any` takes `&mut self` meaning the caller may be borrowed
    // and modified, but not consumed.
    fn any<F>(&mut self, f: F) -> bool where
        // `FnMut` meaning any captured variable may at most be
        // modified, not consumed. `Self::Item` states it takes
        // arguments to the closure by value.
        F: FnMut(Self::Item) -> bool;
}

使用：
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `iter()` for vecs yields `&i32`. Destructure to `i32`.
    println!("2 in vec1: {}", vec1.iter()     .any(|&x| x == 2));
    // `into_iter()` for vecs yields `i32`. No destructuring required.
    println!("2 in vec2: {}", vec2.into_iter().any(|x| x == 2));

    // `iter()` only borrows `vec1` and its elements, so they can be used again
    println!("vec1 len: {}", vec1.len());
    println!("First element of vec1 is: {}", vec1[0]);
    // `into_iter()` does move `vec2` and its elements, so they cannot be used again
    // println!("First element of vec2 is: {}", vec2[0]);
    //println!("vec2 len: {}", vec2.len());
    // TODO: uncomment two lines above and see compiler errors.

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `iter()` for arrays yields `&i32`.
    println!("2 in array1: {}", array1.iter()     .any(|&x| x == 2));
    // `into_iter()` for arrays yields `i32`.
    println!("2 in array2: {}", array2.into_iter().any(|x| x == 2));
}
```

## iterators find 方法

```rust
find方法使用闭包参数源码
pub trait Iterator {
    // The type being iterated over.
    type Item;

    // `find` takes `&mut self` meaning the caller may be borrowed
    // and modified, but not consumed.
    fn find<P>(&mut self, predicate: P) -> Option<Self::Item> where
        // `FnMut` meaning any captured variable may at most be
        // modified, not consumed. `&Self::Item` states it takes
        // arguments to the closure by reference.
        P: FnMut(&Self::Item) -> bool;
}
使用：
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `iter()` for vecs yields `&i32`.
    let mut iter = vec1.iter();
    // `into_iter()` for vecs yields `i32`.
    let mut into_iter = vec2.into_iter();

    // `iter()` for vecs yields `&i32`, and we want to reference one of its
    // items, so we have to destructure `&&i32` to `i32`
    println!("Find 2 in vec1: {:?}", iter     .find(|&&x| x == 2));
    // `into_iter()` for vecs yields `i32`, and we want to reference one of
    // its items, so we have to destructure `&i32` to `i32`
    println!("Find 2 in vec2: {:?}", into_iter.find(| &x| x == 2));

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `iter()` for arrays yields `&&i32`
    println!("Find 2 in array1: {:?}", array1.iter()     .find(|&&x| x == 2));
    // `into_iter()` for arrays yields `&i32`
    println!("Find 2 in array2: {:?}", array2.into_iter().find(|&x| x == 2));
}
注意：|&&x| x部分，可以理解成|&x| *x

Iterator::position 使用示例：
fn main() {
    let vec = vec![1, 9, 3, 3, 13, 2];

    // `iter()` for vecs yields `&i32` and `position()` does not take a reference, so
    // we have to destructure `&i32` to `i32`
    let index_of_first_even_number = vec.iter().position(|&x| x % 2 == 0);
    assert_eq!(index_of_first_even_number, Some(5));
    
    // `into_iter()` for vecs yields `i32` and `position()` does not take a reference, so
    // we do not have to destructure    
    let index_of_first_negative_number = vec.into_iter().position(|x| x < 0);
    assert_eq!(index_of_first_negative_number, None);
}
```

## 函数式方法

```rust
函数式方法和命令式方法使用示例：
fn is_odd(n: u32) -> bool {
    n % 2 == 1
}

fn main() {
    println!("Find the sum of all the numbers with odd squares under 1000");
    let upper = 1000;

    // Imperative approach
    // Declare accumulator variable
    let mut acc = 0;
    // Iterate: 0, 1, 2, ... to infinity
    for n in 0.. {
        // Square the number
        let n_squared = n * n;

        if n_squared >= upper {
            // Break loop if exceeded the upper limit
            break;
        } else if is_odd(n_squared) {
            // Accumulate value, if it's odd
            acc += n_squared;
        }
    }
    println!("imperative style: {}", acc);

    // Functional approach
    let sum_of_squared_odd_numbers: u32 =
        (0..).map(|n| n * n)                             // All natural numbers squared
             .take_while(|&n_squared| n_squared < upper) // Below upper limit
             .filter(|&n_squared| is_odd(n_squared))     // That are odd
             .sum();                                     // Sum them
    println!("functional style: {}", sum_of_squared_odd_numbers);
}

类似于java8中的函数式方法。
使用的方法也需要学习一下。
```

## 发散函数

```rust
1、示例:
fn foo() -> ! {
    panic!("This call never returns.");
}
!代表空类型
2、和()元组类型有区别，（）类型可以实例化，
3、用处，比如continue、loop {}、exit()等不需要返回的函数，提供了一个类型。
```

