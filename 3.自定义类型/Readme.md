## 结构体

```rust
三种类型结构体
1、元组结构体
2、普通结构体
3、unit结构体，在泛型的时候很有用；也可用于实现特征（Trait）示例：
        trait Printable {
            fn print(&self);
        }

        struct Unit;

        impl Printable for Unit {
            fn print(&self) {
                println!("This is a unit struct implementation.");
            }
        }

        fn main() {
            let _unit = Unit;
            _unit.print();
        }
```

## 元组结构体

```rust
1、定义
struct Pair(i32, f32);
2、实例化
let pair = Pair(1, 0.1);
3、获取值
println!("pair contains {:?} and {:?}", pair.0, pair.1);
4、解构
let Pair(integer, decimal) = pair;
```

## 普通结构体

```rust
1、定义
#[derive(Debug)]
struct Person {
    name: String,
    age: u8,
}
struct Point {
    x: f32,
    y: f32,
}

2、实例化
方式1：直接赋值
let name = String::from("Peter");//String的方法直接获取字面量
let age = 27;
let peter = Person { name, age };
方式2：写类似于json
let point: Point = Point { x: 5.2, y: 0.4 };
let another_point: Point = Point { x: 10.3, y: 0.2 };
方式3：从另外一个结构体获取没有赋值的元素
let bottom_right = Point { x: 10.3, ..another_point };//表示y从another_point中获取
3、获取值
println!("point coordinates: ({}, {})", point.x, point.y);
4、解构
let Point { x: left_edge, y: top_edge } = point;
```

## 元组结构体

```rust
1、定义
struct Unit;
2、实例化
let _unit = Unit;
```

## 枚举

```rust
1、概念
	a、有多种变体
	b、变体类型可以是无数据的简单变体、类似元组结构体的变体、类似普通结构体的变体。示例
	enum Message {
        Quit,  // 无数据的简单变体
        Move { x: i32, y: i32 },  // 类似普通结构体的变体
        Write(String),  // 类似元组结构体的变体
        ChangeColor(i32, i32, i32),  // 类似元组结构体的变体
	}
2、遍历
	使用match，示例
	enum WebEvent {
        // An `enum` variant may either be `unit-like`,
        PageLoad,
        PageUnload,
        // like tuple structs,
        KeyPress(char),
        Paste(String),
        // or c-like structures.
        Click { x: i64, y: i64 },
    }

    // A function which takes a `WebEvent` enum as an argument and
    // returns nothing.
    fn inspect(event: WebEvent) {
        match event {
            WebEvent::PageLoad => println!("page loaded"),
            WebEvent::PageUnload => println!("page unloaded"),
            // Destructure `c` from inside the `enum` variant.
            WebEvent::KeyPress(c) => println!("pressed '{}'.", c),
            WebEvent::Paste(s) => println!("pasted \"{}\".", s),
            // Destructure `Click` into `x` and `y`.
            WebEvent::Click { x, y } => {
                println!("clicked at x={}, y={}.", x, y);
            },
        }
    }
3、别名
	枚举名字太长的时候，可以使用type定义别名。如
	enum VeryVerboseEnumOfThingsToDoWithNumbers {
        Add,
        Subtract,
    }

    // Creates a type alias
    type Operations = VeryVerboseEnumOfThingsToDoWithNumbers;

    fn main() {
        // We can refer to each variant via its alias, not its long and inconvenient
        // name.
        let x = Operations::Add;
    }
4、给枚举定义方法
	使用impl。如
	enum VeryVerboseEnumOfThingsToDoWithNumbers {
        Add,
        Subtract,
    }

    impl VeryVerboseEnumOfThingsToDoWithNumbers {
        fn run(&self, x: i32, y: i32) -> i32 {
            match self {
                Self::Add => x + y,
                Self::Subtract => x - y,
            }
        }
    }

    fn main() {
        let add_op = VeryVerboseEnumOfThingsToDoWithNumbers::Add;
        let result_add = add_op.run(5, 3);
        println!("5 + 3 = {}", result_add);

        let subtract_op = VeryVerboseEnumOfThingsToDoWithNumbers::Subtract;
        let result_subtract = subtract_op.run(5, 3);
        println!("5 - 3 = {}", result_subtract);
    }
```

##  to_owned()

```
// `to_owned()` creates an owned `String` from a string slice.
    let pasted  = WebEvent::Paste("my text".to_owned());
之所以使用to_owned()，因为字面量"my text"是&str类型，字符串切片 &str 转换为一个拥有所有权的 String 类型，否则是地址，不安全。
```

## use

```rust
1、use声明可以避免手动进行作用域限定（rust语言中，每个模块、类型、函数都有其自身的作用域，如果想使用其他模块中的项时，需要名曲指定路径，这就是手动作用域限定）。示例
mod arithmetic {
    pub enum Operation {
        Add,
        Subtract,
    }

    pub fn calculate(op: Operation, x: i32, y: i32) -> i32 {
        match op {
            Operation::Add => x + y,
            Operation::Subtract => x - y,
        }
    }
}

fn main() {
    let op = arithmetic::Operation::Add;
    let result = arithmetic::calculate(op, 5, 3);
    println!("Result: {}", result);
}
2、使用use声明示例：
mod arithmetic {
    pub enum Operation {
        Add,
        Subtract,
    }

    pub fn calculate(op: Operation, x: i32, y: i32) -> i32 {
        match op {
            Operation::Add => x + y,
            Operation::Subtract => x - y,
        }
    }
}

use arithmetic::Operation;
use arithmetic::calculate;

fn main() {
    let op = Operation::Add;
    let result = calculate(op, 5, 3);
    println!("Result: {}", result);
}

```

## 枚举的显示判别值和隐式判别值

```rust
1、隐式
示例：
// enum with implicit discriminator (starts at 0)
enum Number {
    Zero,
    One,
    Two,
}
Rust 会为每个变体分配一个整数作为判别值，从 0 开始依次递增。所以，Number::Zero 的判别值是 0，Number::One 的判别值是 1，Number::Two 的判别值是 2
2、显示
示例：
// enum with explicit discriminator
enum Color {
    Red = 0xff0000,
    Green = 0x00ff00,
    Blue = 0x0000ff,
}
Color::Red 的判别值是 0xff0000，Color::Green 的判别值是 0x00ff00，Color::Blue 的判别值是 0x0000ff，这些值对应 RGB 颜色编码。
```

## 使用enum实现链表

```rust
use crate::List::*;

enum List {
    // Cons: Tuple struct that wraps an element and a pointer to the next node
    Cons(u32, Box<List>),
    // Nil: A node that signifies the end of the linked list
    Nil,
}

// Methods can be attached to an enum
impl List {
    // Create an empty list
    fn new() -> List {
        // `Nil` has type `List`
        Nil
    }

    // Consume a list, and return the same list with a new element at its front
    fn prepend(self, elem: u32) -> List {
        // `Cons` also has type List
        Cons(elem, Box::new(self))
    }

    // Return the length of the list
    fn len(&self) -> u32 {
        // `self` has to be matched, because the behavior of this method
        // depends on the variant of `self`
        // `self` has type `&List`, and `*self` has type `List`, matching on a
        // concrete type `T` is preferred over a match on a reference `&T`
        // after Rust 2018 you can use self here and tail (with no ref) below as well,
        // rust will infer &s and ref tail. 
        // See https://doc.rust-lang.org/edition-guide/rust-2018/ownership-and-lifetimes/default-match-bindings.html
        match *self {
            // Can't take ownership of the tail, because `self` is borrowed;
            // instead take a reference to the tail
            Cons(_, ref tail) => 1 + tail.len(),
            // Base Case: An empty list has zero length
            Nil => 0
        }
    }

    // Return representation of the list as a (heap allocated) string
    fn stringify(&self) -> String {
        match *self {
            Cons(head, ref tail) => {
                // `format!` is similar to `print!`, but returns a heap
                // allocated string instead of printing to the console
                format!("{}, {}", head, tail.stringify())
            },
            Nil => {
                format!("Nil")
            },
        }
    }
}

fn main() {
    // Create an empty linked list
    let mut list = List::new();

    // Prepend some elements
    list = list.prepend(1);
    list = list.prepend(2);
    list = list.prepend(3);

    // Show the final state of the list
    println!("linked list has length: {}", list.len());
    println!("{}", list.stringify());
}

1、len和stringify都使用了递归
2、match类似于java中的case when
3、&self表示对类型实例的不可变引用，调用该方法时不会获取实例的所有权，只是借用实例，在方法内部不能修改实例内容；
self：表示获取类型实例的所有权，调用该方法后，原实例将被移动（move），不能再被使用；
&mut self：表示对类型实例的可变引用，调用该方法时可以修改实例的内容。
4、ref作用，ref 关键字用于创建对值的引用，避免转移值的所有权。在 Cons(_, ref tail) 中，ref 确保了在不转移 tail 所有权的情况下可以递归调用 tail.len() 方法，从而正确计算链表的长度。
```

## const和static

```
1、const和static这两种常量可以在任何作用域中声明，其中也包括全局作用域。
2、都需要显示的指定类型。
3、const用于声明不可变的值，一旦声明了const常量，它的值在程序运行期间就不能被修改。
4、static变量可能是可变的，访问或修改static变量是不安全的操作，这是因为rust的所有权和借用机制目的在于保证内存安全和线程安全，而可变的static变量可能会在多个地方被同时访问和修改，从而引发数据竞争和其他安全问题。
5、对可变的static变量进行操作需要使用unsafe块。
```

