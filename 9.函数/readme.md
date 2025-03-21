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

