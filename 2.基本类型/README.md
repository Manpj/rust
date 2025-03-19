# 基本类型

## 基本类型

```
有符号整型：i8, i16, i32, i64, i128 and isize (pointer size)
无符号整型：u8, u16, u32, u64, u128 and usize (pointer size)
浮点：f32, f64
char：Unicode scalar values like 'a', 'α' and '∞' (4 bytes each)
布尔：true 、false
unit type ():空元组

()解释：
1、单元类型用()表示，它只有一个值，即()，从形式上看，这是一个空元组。例如在函数返回值为单元类型时，
fn do_something() -> (){ /* 执行一些操作凡是不反悔有意义的值 */} ，这里函数返回的就是单元类型的值。
2、不被视为复合类型，单元类型虽然在形式上有元组的样子，但它并不包含多个值，只有单一的一个()值。这个特性在Rust中很重要，理解它有助于争取使用和区分不同类型，在函数定义、类型匹配等场景中都有体现。比如在模式匹配中，对于单元类型值()有专门的匹配规则，与复合类型的匹配规则不同。

isize和usize解释：
isize是有符号整数类型，去哦大下与目标平台的指针大小相同。在32位系统上，isize是32位，在64位系统上，isize是64位。usize同样。
使用场景：let index: isize = 1;let value = array[index as usize];  定义数组的下标类型，
		let length: usize = vector.len();数组长度等。
```

## 复合类型

```
数组Arrays like [1, 2, 3]
元组Tuples like (1, true)

示例：
    // Array signature consists of Type T and length as [T; length].
    let my_array: [i32; 5] = [1, 2, 3, 4, 5];

    // Tuple is a collection of values of different types 
    // and is constructed using parentheses ().
    let my_tuple = (5u32, 1u8, true, -5.04f32);
```

## 变量多种声明类型的方式

```
1、变量类型标注：
let num1: i32 = 5;
let str1: &str = "hello";
2、数字类型的标注方式：
   后缀标注：let num2 = 5u32; let num3 = 3.14f64
   默认类型：Integers default to i32 and floats to f64
3、rust支持根据上下文类型推断
   让开发者可以更专注于实现业务逻辑，而不必过多关注一些显而易见的类型标注
```

## mut

```
1、使用mut关键字，意味着它的值可以改变
// A type can also be inferred from context.
    let mut inferred_type = 12; // Type i64 is inferred from another line.
    inferred_type = 4294967296i64;
2、 上下文推断类型引起的错误
 // A mutable variable's value can be changed.
    let mut mutable = 12; // Mutable `i32`
    mutable = 21;

    // Error! The type of a variable can't be changed.
    mutable = true;
    推断位i32后，又赋值给bool，所以报错
```

## 变量可以被重写

```
// A type can also be inferred from context.
    let mut inferred_type = 12; // Type i64 is inferred from another line.
    inferred_type = 4294967296i64;

    // A mutable variable's value can be changed.
    let mut mutable = 12; // Mutable `i32`
    mutable = 21;

    // Error! The type of a variable can't be changed.
    mutable = true;

    // Variables can be overwritten with shadowing.
    let mutable = true;
    
    相同的变量名，可以再次使用，重新定义为其他类型。
```

## char 和 str

```
let single_char1: char = '∞';

let string_slice1: &str = "Hello, Rust!";
```

## 数字字面值允许插入下划线

```
1_000与1000在数值上完全相同
0.000_001和0.000001的值相等
```

## 支持科学计数法

```
let large_number = 1e6;
let small_number = 7.6e-4;
// 这里large_number和small_number的类型都是f64
```

## 详细介绍元组

```
1、元组可以用于函数的参数和函数的返回值，如：
fn reverse(pair: (i32, bool)) -> (bool, i32) {
    // `let` can be used to bind the members of a tuple to variables.
    let (int_param, bool_param) = pair;

    (bool_param, int_param)
}
2、结构体可以使用元组
#[derive(Debug)]
struct Matrix(f32, f32, f32, f32);
3、可以通过下标获取元组的值
	println!("Long tuple first value: {}", long_tuple.0);
    println!("Long tuple second value: {}", long_tuple.1);
4、元组可以被打印，表示实现了输出方法（不像结构体一样，没有实现输出方法）
	println!("tuple of tuples: {:?}", tuple_of_tuples);
	注意：超过12个元素的元组不能被打印
5、可以反转元组
	println!("The reversed pair is {:?}", reverse(pair));
6、一个元素的元组要带逗号，否则不会当成元组类型
	println!("One element tuple: {:?}", (5u32,));
    println!("Just an integer: {:?}", (5u32));
7、元组里面可以包含元组
	let tuple_of_tuples = ((1u8, 2u16, 2u32), (4u64, -1i8), -2i16);
```

## 详细解释数组和切片

```
1、数组的类型
let numbers = [1, 2, 3]; 写作[T; length]，其中，T代表数组元素的类型，length代表数组中元素的数量。
2、切片
a、切片与数组类似，但有一个关键区别：切片的长度在编译时是未知的。
b、第一个字是指向数据的指针，它指向切片所引用的数据在内存中的起始位置。第二个字则表示切片的长度，即切片包含的元素个数。这里提到的字的大小与usize类型相同，而usize的大小取决于处理器架构，例如在 x86 - 64 架构上是 64 位
示例：let numbers = [1, 2, 3, 4, 5];let slice = &numbers[1..3];
3、数组所有的元素可以赋值为相同的值
/ All elements can be initialized to the same value.
    let ys: [i32; 500] = [0; 500];
4、数组的下标从0开始
	println!("First element of the array: {}", xs[0]);
    println!("Second element of the array: {}", xs[1]);
5、数组的长度
	println!("Number of elements in array: {}", xs.len());
6、数组的栈分配
	println!("Array occupies {} bytes", mem::size_of_val(&xs));
7、空数组
	let empty_array: [u32; 0] = [];

```

## 安全的使用数组

```
    // Arrays can be safely accessed using `.get`, which returns an
    // `Option`. This can be matched as shown below, or used with
    // `.expect()` if you would like the program to exit with a nice
    // message instead of happily continue.
    for i in 0..xs.len() + 1 { // Oops, one element too far!
        match xs.get(i) {
            Some(xval) => println!("{}: {}", i, xval),
            None => println!("Slow down! {} is too far!", i),
        }
    }
    
    1、使用数组的get方法,可以安全的访问数组。get方法接受一个索引作为参数，并返回一个Option类型的值。Option类型是 Rust 标准库中的一个枚举，它有两个变体：Some(T)和None。当索引在数组的有效范围内时，get方法返回Some(T)，其中T是数组元素的类型；当索引越界时，get方法返回None。
    2、循环遍历及越界情，也不会报错
    3、match xs.get(i) { 使用match表达式来处理get方法返回的Option值
    4、expect()方法的替代用法
    expect方法会使程序终止，并输出传递给它的错误消息
    但需要谨慎使用，因为它会直接终止程序
```



##  mem::size_of_val

```
mem::size_of_val是 Rust 标准库中的一个函数，它接受一个值的引用，并返回该值在内存中占用的字节数
引入方式：use std::mem;
```



