## From and Into

```rust
1、From，从结构体转换为基本类型。示例：
use std::convert::From;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl From<i32> for Number {
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let num = Number::from(30);
    println!("My number is {:?}", num);
}
2、Into，从数字转换为结构体。示例：
use std::convert::Into;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl Into<Number> for i32 {
    fn into(self) -> Number {
        Number { value: self }
    }
}

fn main() {
    let int = 5;
    // Try removing the type annotation
    let num: Number = int.into();
    println!("My number is {:?}", num);
}
3、当实现了from，默认into也会实现（即只实现from。into不用实现也可使用）。相反实现into，不实现from，则不可以使用from。如：
use std::convert::From;

#[derive(Debug)]
struct Number {
    value: i32,
}

// Define `From`
impl From<i32> for Number {
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let int = 5;
    // use `Into`
    let num: Number = int.into();
    println!("My number is {:?}", num);
}

```

## `PartialEq` 介绍

```rust
PartialEq 特征用于定义类型的实例之间的相等性比较。当为一个类型派生 PartialEq 特征后，就可以使用 == 和 != 操作符来比较该类型的两个实例是否相等。示例：
#[derive(Debug, PartialEq)]
struct EvenNumber(i32);

fn main() {
    let num1 = EvenNumber(2);
    let num2 = EvenNumber(2);
    let num3 = EvenNumber(4);

    // 使用 Debug 特征打印实例
    println!("num1: {:?}", num1);

    // 使用 PartialEq 特征比较实例
    println!("num1 == num2: {}", num1 == num2);
    println!("num1 == num3: {}", num1 == num3);
}
注：如下方法也可使用
assert_eq!(result, Ok(EvenNumber(8)));
```

## TryFrom and TryInto

```rust
1、和from与into功能类似
2、是易出错的转换
示例：
use std::convert::TryFrom;
use std::convert::TryInto;

#[derive(Debug, PartialEq)]
struct EvenNumber(i32);

impl TryFrom<i32> for EvenNumber {
    type Error = ();

    fn try_from(value: i32) -> Result<Self, Self::Error> {
        if value % 2 == 0 {
            Ok(EvenNumber(value))
        } else {
            Err(())
        }
    }
}

fn main() {
    // TryFrom

    assert_eq!(EvenNumber::try_from(8), Ok(EvenNumber(8)));
    assert_eq!(EvenNumber::try_from(5), Err(()));

    // TryInto

    let result: Result<EvenNumber, ()> = 8i32.try_into();
    assert_eq!(result, Ok(EvenNumber(8)));
    let result: Result<EvenNumber, ()> = 5i32.try_into();
    assert_eq!(result, Err(()));
}

```

## Result解构介绍

```rust
在 Rust 中，Result 是一个枚举类型，它被广泛用于处理可能会失败的操作。Result 类型的定义如下
enum Result<T, E> {
    Ok(T),
    Err(E),
}

so:Result<Self, Self::Error> 这样定义。
```

## unwrap

```rust
1、解释
unwrap() 是 Result 和 Option 类型都有的一个方法。对于 Result 类型，unwrap() 的功能是：
若 Result 是 Ok(value)，unwrap() 会返回 value。
若 Result 是 Err(error)，unwrap() 会触发 panic，程序会崩溃并输出错误信息。
2、虽然 unwrap() 用起来很方便，但它也存在风险。如果解析失败，程序就会 panic 并崩溃
3、为了避免程序因为 unwrap() 而崩溃，可以使用 match 语句或者 if let 语句来处理 Result 类型的值。例如：
fn main() {
    let result = "abc".parse::<i32>();
    match result {
        Ok(value) => println!("Parsed value: {}", value),
        Err(error) => println!("Parsing failed: {}", error),
    }
}
```

## 转换成字符串 或 从字符串转换

```rust
1、自定义结构体转换成字符串
实现fmt::Display即可（也是自定义输出的方式），示例：
use std::fmt;

struct Circle {
    radius: i32
}

impl fmt::Display for Circle {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "Circle of radius {}", self.radius)
    }
}

fn main() {
    let circle = Circle { radius: 6 };
    println!("{}", circle.to_string());
}
2、字符串转换为基本类型
直接调用parse()方法转换即可，如：
fn main() {
    let parsed: i32 = "5".parse().unwrap();
    let turbo_parsed = "10".parse::<i32>().unwrap();

    let sum = parsed + turbo_parsed;
    println!("Sum: {:?}", sum);
}
3、字符串转换为自定义结构体
需要实现FromStr。如：
use std::num::ParseIntError;
use std::str::FromStr;

#[derive(Debug)]
struct Circle {
    radius: i32,
}

impl FromStr for Circle {
    type Err = ParseIntError;
    fn from_str(s: &str) -> Result<Self, Self::Err> {
        match s.trim().parse() {
            Ok(num) => Ok(Circle{ radius: num }),
            Err(e) => Err(e),
        }
    }
}

fn main() {
    let radius = "    3 ";
    let circle: Circle = radius.parse().unwrap();
    println!("{:?}", circle);
}
```

