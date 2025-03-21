## if/else

```rust
1、条件表达式不需要（）
2、所有的分支必须返回相同的类型
如：
fn main() {
    let n = 5;

    if n < 0 {
        print!("{} is negative", n);
    } else if n > 0 {
        print!("{} is positive", n);
    } else {
        print!("{} is zero", n);
    }

    let big_n =
        if n < 10 && n > -10 {
            println!(", and is a small number, increase ten-fold");

            // This expression returns an `i32`.
            10 * n
        } else {
            println!(", and is a big number, halve the number");

            // This expression must return an `i32` as well.
            n / 2
            // TODO ^ Try suppressing this expression with a semicolon.
        };
    //   ^ Don't forget to put a semicolon here! All `let` bindings need it.

    println!("{} -> {}", n, big_n);
}
```

## loop

```rust
1、无限循环
2、continue,跳过后面程序。类似于java等其他语言的关键字功能
3、break，结束循环
示例：
fn main() {
    let mut count = 0u32;

    println!("Let's count until infinity!");

    // Infinite loop
    loop {
        count += 1;

        if count == 3 {
            println!("three");

            // Skip the rest of this iteration
            continue;
        }

        println!("{}", count);

        if count == 5 {
            println!("OK, that's enough");

            // Exit this loop
            break;
        }
    }
}
输出：
Let's count until infinity!
1
2
three
4
5
OK, that's enough
```

## loop 的嵌套和标签

```rust
1、标签使用方式
一个引号+label名字+loop关键字。如：
’outer：loop{
2、break 后 可以加标签
   break 'outer;
示例：
#![allow(unreachable_code, unused_labels)]

fn main() {
    'outer: loop {
        println!("Entered the outer loop");

        'inner: loop {
            println!("Entered the inner loop");

            // This would break only the inner loop
            //break;

            // This breaks the outer loop
            break 'outer;
        }

        println!("This point will never be reached");
    }

    println!("Exited the outer loop");
}    
输出：
Entered the outer loop
Entered the inner loop
Exited the outer loop  
```

## loop返回值

```rust
1、loop常用来重试某个操作直到成功
2、loop可以有返回值，在break后增加返回值。
如：
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;//返回值
        }
    };

    assert_eq!(result, 20);
}

```

## while

```rust
while类似于java中的用法。示例：
fn main() {
    // A counter variable
    let mut n = 1;

    // Loop while `n` is less than 101
    while n < 101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }

        // Increment counter
        n += 1;
    }
}
```

## for循环范围

```rust
1、a..b 左闭右开区间。示例：
fn main() {
    // `n` will take the values: 1, 2, ..., 100 in each iteration
    for n in 1..101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }
    }
}
2、a..=b。示例：
fn main() {
    // `n` will take the values: 1, 2, ..., 100 in each iteration
    for n in 1..=100 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }
    }
}
```

## for循环迭代

```rust
1、iter。对集合元素进行不可变借用，不改变集合所有权，迭代后集合仍可用，用于只读遍历。示例：
fn main() {
    let names = vec!["Bob", "Frank", "Ferris"];

    for name in names.iter() {
        match name {
            &"Ferris" => println!("There is a rustacean among us!"),
            // TODO ^ Try deleting the & and matching just "Ferris"
            _ => println!("Hello {}", name),
        }
    }
    
    println!("names: {:?}", names);
}
2、into_iter。消耗集合，转移元素所有权，迭代后集合不可用，用于完全拥有元素所有权的场景。示例：
fn main() {
    let names = vec!["Bob", "Frank", "Ferris"];

    for name in names.into_iter() {
        match name {
            "Ferris" => println!("There is a rustacean among us!"),
            _ => println!("Hello {}", name),
        }
    }
    
    println!("names: {:?}", names);
    // FIXME ^ Comment out this line
}
3、iter_mut。对集合元素进行可变借用，不改变集合所有权，迭代后集合仍可用，用于边遍历边修改元素。示例：
fn main() {
    let mut names = vec!["Bob", "Frank", "Ferris"];

    for name in names.iter_mut() {
        *name = match name {
            &mut "Ferris" => "There is a rustacean among us!",
            _ => "Hello",
        }
    }

    println!("names: {:?}", names);
}

```

## 简单match例子

```rust
1、多个分支，最后的分支可以用_（表示其他的）。示例：
fn main() {
    let number = 13;
    // TODO ^ Try different values for `number`

    println!("Tell me about {}", number);
    match number {
        // Match a single value
        1 => println!("One!"),
        // Match several values
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        // TODO ^ Try adding 13 to the list of prime values
        // Match an inclusive range
        13..=19 => println!("A teen"),
        // Handle the rest of cases
        _ => println!("Ain't special"),//其他情况使用_
        // TODO ^ Try commenting out this catch-all arm
    }
}
2、还可以匹配boolean基本类型。示例：
fn main() {
    let boolean = true;
    // Match is an expression too
    let binary = match boolean {
        // The arms of a match must cover all the possible values
        false => 0,
        true => 1,
        // TODO ^ Try commenting out one of these arms
    };

    println!("{} -> {}", boolean, binary);
}
```

## match解构元组类型数据

```rust
解构元组类型数据示例：
fn main() {
    let triple = (0, -2, 3);
    // TODO ^ Try different values for `triple`

    println!("Tell me about {:?}", triple);
    // Match can be used to destructure a tuple
    match triple {
        // Destructure the second and third elements
        (0, y, z) => println!("First is `0`, `y` is {:?}, and `z` is {:?}", y, z),
        (1, ..)  => println!("First is `1` and the rest doesn't matter"),
        (.., 2)  => println!("last is `2` and the rest doesn't matter"),
        (3, .., 4)  => println!("First is `3`, last is `4`, and the rest doesn't matter"),
        // `..` can be used to ignore the rest of the tuple
        _      => println!("It doesn't matter what they are"),
        // `_` means don't bind the value to a variable
    }
}
```

## match 解构数组、切片类型数据

```rust
解析示例：
fn main() {
    // Try changing the values in the array, or make it a slice!
    let array = [1, -2, 6];

    match array {
        // Binds the second and the third elements to the respective variables
        //second,third表示，第一个元素匹配上之后，分别将数组的第二个元素给second，第三个元素给third
        [0, second, third] =>
            println!("array[0] = 0, array[1] = {}, array[2] = {}", second, third),

        // Single values can be ignored with _
        [1, _, third] => println!(
            "array[0] = 1, array[2] = {} and array[1] was ignored",
            third
        ),

        // You can also bind some and ignore the rest
        [-1, second, ..] => println!(
            "array[0] = -1, array[1] = {} and all the other ones were ignored",
            second
        ),
        // The code below would not compile
        // [-1, second] => ...

        // Or store them in another array/slice (the type depends on
        // that of the value that is being matched against)
        //tail表示，rest部分是数组tail就是数组，rest部分是切片，tail就是切片
        [3, second, tail @ ..] => println!(
            "array[0] = 3, array[1] = {} and the other elements were {:?}",
            second, tail
        ),

        // Combining these patterns, we can, for example, bind the first and
        // last values, and store the rest of them in a single array
        [first, middle @ .., last] => println!(
            "array[0] = {}, middle = {:?}, array[2] = {}",
            first, middle, last
        ),
    }
}
```

## match解构枚举

```rust
示例：
// `allow` required to silence warnings because only
// one variant is used.
#[allow(dead_code)]
enum Color {
    // These 3 are specified solely by their name.
    Red,
    Blue,
    Green,
    // These likewise tie `u32` tuples to different names: color models.
    RGB(u32, u32, u32),
    HSV(u32, u32, u32),
    HSL(u32, u32, u32),
    CMY(u32, u32, u32),
    CMYK(u32, u32, u32, u32),
}

fn main() {
    let color = Color::RGB(122, 17, 40);
    // TODO ^ Try different variants for `color`

    println!("What color is it?");
    // An `enum` can be destructured using a `match`.
    match color {
        Color::Red   => println!("The color is Red!"),
        Color::Blue  => println!("The color is Blue!"),
        Color::Green => println!("The color is Green!"),
        Color::RGB(r, g, b) =>
            println!("Red: {}, green: {}, and blue: {}!", r, g, b),
        Color::HSV(h, s, v) =>
            println!("Hue: {}, saturation: {}, value: {}!", h, s, v),
        Color::HSL(h, s, l) =>
            println!("Hue: {}, saturation: {}, lightness: {}!", h, s, l),
        Color::CMY(c, m, y) =>
            println!("Cyan: {}, magenta: {}, yellow: {}!", c, m, y),
        Color::CMYK(c, m, y, k) =>
            println!("Cyan: {}, magenta: {}, yellow: {}, key (black): {}!",
                c, m, y, k),
        // Don't need another arm because all variants have been examined
    }
}
```

## &val和*val

```rust
1、&val，这是解构模式，从引用中提取被引用的值。如：
let reference = &4;
表示：这里创建了一个 i32 类型值 4 的引用 reference。& 符号表明这是一个引用赋值操作，即 reference 存储的是值 4 的内存地址
match reference {
    &val => println!("Got a value via destructuring: {:?}", val),
}
2、*val，是解引用。如：
match *reference {
    val => println!("Got a value via dereferencing: {:?}", val),
}
*reference 会获取 reference 所指向的值，也就是 4。这里先对 reference 进行解引用，得到一个 i32 类型的值
```

## ref和&、ref mut和&mut

```rust
1、&，最常见的用途是创建引用。例如：
    let num = 42;
    let ref_num = &num; // 创建一个指向 num 的不可变引用
    ref_num 是一个 &i32 类型的引用，它指向 num 变量
2、& 在匹配模式中，&也用于匹配引用类型。如：
    let num = 42;
    let ref_num = &num;
    match ref_num {
        &val => println!("Value: {}", val),
    }
    这里的 &val 模式会匹配 ref_num 这个引用，并将引用所指向的值绑定到 val 变量。
3、&mut 用于创建可变引用，允许修改引用指向的值。如：
    let mut num = 42;
    let ref_num = &mut num; // 创建一个指向 num 的可变引用
    *ref_num += 1; // 修改引用指向的值
4、ref 赋值时候创建引用，当你在变量名前加上 ref 时，会为右侧的值创建一个引用并赋值给左侧的变量。例如：
    let ref ref_num = 42; // 创建一个指向 42 的不可变引用
    这里的 ref_num 是一个 &i32 类型的引用，它指向值 42。
5、ref在匹配模式中引用绑定。如：
    let tuple = (1, 2);
    let (ref a, ref b) = tuple; // 创建指向 tuple 元素的不可变引用
    这里的 ref a 和 ref b 分别是指向 tuple 中第一个和第二个元素的不可变引用。
6、ref mut 用于在模式匹配中绑定可变引用。如：
	let mut tuple = (1, 2);
    let (ref mut a, ref mut b) = tuple; // 创建指向 tuple 元素的可变引用
    *a += 1; // 修改引用指向的值
	
```

## ref  与 所有权

```rust
使用ref可以保证原变量的所有权不会被转移，例如字符串不使用ref重新赋值后，所有权会转移。如
fn main() {
    let s1 = String::from("hello");
    let s2 = s1; // 这里发生了所有权转移，s1 不再有效
    // println!("{}", s1); // 这行代码会报错，因为 s1 的所有权已经转移
    println!("{}", s2);
}
```

## match解构针和ref

```rust
示例：
fn main() {
    // Assign a reference of type `i32`. The `&` signifies there
    // is a reference being assigned.
    let reference = &4;

    match reference {
        // If `reference` is pattern matched against `&val`, it results
        // in a comparison like:
        // `&i32`
        // `&val`
        // ^ We see that if the matching `&`s are dropped, then the `i32`
        // should be assigned to `val`.
        &val => println!("Got a value via destructuring: {:?}", val),
    }

    // To avoid the `&`, you dereference before matching.
    match *reference {
        val => println!("Got a value via dereferencing: {:?}", val),
    }

    // What if you don't start with a reference? `reference` was a `&`
    // because the right side was already a reference. This is not
    // a reference because the right side is not one.
    let _not_a_reference = 3;

    // Rust provides `ref` for exactly this purpose. It modifies the
    // assignment so that a reference is created for the element; this
    // reference is assigned.
    let ref _is_a_reference = 3;

    // Accordingly, by defining 2 values without references, references
    // can be retrieved via `ref` and `ref mut`.
    let value = 5;
    let mut mut_value = 6;

    // Use `ref` keyword to create a reference.
    match value {
        ref r => println!("Got a reference to a value: {:?}", r),
    }

    // Use `ref mut` similarly.
    match mut_value {
        ref mut m => {
            // Got a reference. Gotta dereference it before we can
            // add anything to it.
            *m += 10;//注意这里使用了*号，即获取引用的值
            println!("We added 10. `mut_value`: {:?}", m);
        },
    }
}
```

## match解构结构体

```rust
示例：
fn main() {
    struct Foo {
        x: (u32, u32),
        y: u32,
    }

    // Try changing the values in the struct to see what happens
    let foo = Foo { x: (1, 2), y: 3 };

    match foo {
        Foo { x: (1, b), y } => println!("First of x is 1, b = {},  y = {} ", b, y),

        // you can destructure structs and rename the variables,
        // the order is not important
        Foo { y: 2, x: i } => println!("y is 2, i = {:?}", i),

        // and you can also ignore some variables:
        Foo { y, .. } => println!("y = {}, we don't care about x", y),
        // this will give an error: pattern does not mention field `x`
        //Foo { y } => println!("y = {}", y),
    }

    let faa = Foo { x: (1, 2), y: 3 };

    // You do not need a match block to destructure structs:
    let Foo { x : x0, y: y0 } = faa;
    println!("Outside: x0 = {x0:?}, y0 = {y0}");

    // Destructuring works with nested(嵌套) structs as well:
    struct Bar {
        foo: Foo,
    }

    let bar = Bar { foo: faa };
    let Bar { foo: Foo { x: nested_x, y: nested_y } } = bar;
    println!("Nested: nested_x = {nested_x:?}, nested_y = {nested_y:?}");
}
```

## match 守卫

```rust
1、The `if condition` part ^ is a guard 。示例：
#[allow(dead_code)]
enum Temperature {
    Celsius(i32),
    Fahrenheit(i32),
}

fn main() {
    let temperature = Temperature::Celsius(35);
    // ^ TODO try different values for `temperature`

    match temperature {
        Temperature::Celsius(t) if t > 30 => println!("{}C is above 30 Celsius", t),
        // The `if condition` part ^ is a guard
        Temperature::Celsius(t) => println!("{}C is equal to or below 30 Celsius", t),

        Temperature::Fahrenheit(t) if t > 86 => println!("{}F is above 86 Fahrenheit", t),
        Temperature::Fahrenheit(t) => println!("{}F is equal to or below 86 Fahrenheit", t),
    }
}

2、match的模式必须匹配所有模式，否则编译器可能会报错，解决办法可以增加通用匹配。如：
fn main() {
    let number: u8 = 4;

    match number {
        i if i == 0 => println!("Zero"),
        i if i > 0 => println!("Greater than zero"),
        // _ => unreachable!("Should never happen."),
        // TODO ^ uncomment to fix compilation
    }
}
```

