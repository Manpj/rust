## HelloWorld

```
// This is a comment, and is ignored by the compiler.
// You can test this code by clicking the "Run" button over there ->
// or if you prefer to use your keyboard, you can use the "Ctrl + Enter"
// shortcut.

// This code is editable, feel free to hack it!
// You can always return to the original code by clicking the "Reset" button ->

// This is the main function.
fn main() {
    // Statements here are executed when the compiled binary is called.

    // Print text to the console.
    println!("Hello World!");
}
```

```
编译： 
rustc hello.rs
运行
./hello
```

### 格式化打印

```
format!   不会输出到控制台
print!    会输出到控制台
println!  可以换行
eprint!   会打印标准错误，错误提示信息打印
eprintln! 可以换行的标准错误打印
```

```
打印参数
1、{0}，{1}，{2}方式。举例：println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");
2、{name},{age}方式。举例：
	 println!("{subject} {verb} {object}",
             object="the lazy dog",
             subject="the quick brown fox",
             verb="jumps over");
3、{}，{:b},{:o},{:x}进制转换方式。举例：
	println!("Base 10:               {}",   69420); // 69420   十进制
    println!("Base 2 (binary):       {:b}", 69420); // 10000111100101100  二进制
    println!("Base 8 (octal):        {:o}", 69420); // 207454    八进制
    println!("Base 16 (hexadecimal): {:x}", 69420); // 10f2c     十六进制
4、{:>5}，{:<5}方式。举例：
		// output "    1". (Four white spaces and a "1", for a total width of 5.)
    	println!("{number:>5}", number=1); 大于表示左侧增加空格，小于表示右侧增加空格
5、{number:3>5},{number:0<5}方式。举例：左侧增加4个3，右侧增加4个0
		// You can pad numbers with extra zeroes,
    	println!("{number:3>5}", number=1); // 33331
    	// and left-adjust by flipping the sign. This will output "10000".
    	println!("{number:0<5}", number=1); // 10000
6、{number:0>width$}方式。举例：宽度可以传入变量
		// You can use named arguments in the format specifier by appending a `$`.
    	println!("{number:0>width$}", number=1, width=5);    
```

## #[allow(dead_code)]

```
#[allow(dead_code)] // disable `dead_code` which warn against unused module
 struct Structure(i32);
 
 可以取消警告
```

## {:?} {}

```
fmt::Debug: Uses the {:?} marker. Format text for debugging purposes.
fmt::Display: Uses the {} marker. Format text in a more elegant, user friendly fashion.

两种方式格式化文本写法不一样
```

## \#[derive(Debug)]

```
标准的输出，使用示例：
#[derive(Debug)]
struct Structure(i32);
// `Structure` is printable!
println!("Now {:?} will print!", Structure(3));//Structure(3)

也可以格式化
// Pretty print
println!("{:#?}", peter);
```

## fmt::Display

```
自定义输出，需要重写输出方法，示例：
struct MinMax(i64, i64);

// Implement `Display` for `MinMax`.
impl fmt::Display for MinMax {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Use `self.number` to refer to each positional data point.
        write!(f, "({}, {})", self.0, self.1)
    }
}

write!(f, "({}, {})", self.0, self.1)解释：
f相当于输出流，第二个参数是输出格式，第三、四个参数是{}内的内容

注意：std::fmt有很多实现，比如这个fmt::Binary要使用对应的实现，否则不生效：参考：https://doc.rust-lang.org/std/fmt/#formatting-traits
```

## struct List(Vec<i32>);

```
数据类型，for遍历方式
use std::fmt; // Import the `fmt` module.

// Define a structure named `List` containing a `Vec`.
struct List(Vec<i32>);

impl fmt::Display for List {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Extract the value using tuple indexing,
        // and create a reference to `vec`.
        let vec = &self.0;

        write!(f, "[")?;

        // Iterate over `v` in `vec` while enumerating the iteration
        // count in `count`.
        for (count, v) in vec.iter().enumerate() {
            // For every element except the first, add a comma.
            // Use the ? operator to return on errors.
            if count != 0 { write!(f, ", ")?; }
            write!(f, "{}", v)?;
        }

        // Close the opened bracket and return a fmt::Result value.
        write!(f, "]")
    }
}

fn main() {
    let v = List(vec![1, 2, 3]);
    println!("{}", v);
}
```

## write特殊情况

```
struct City {
    name: &'static str,
    // Latitude
    lat: f32,
    // Longitude
    lon: f32,
}

impl Display for City {
    // `f` is a buffer, and this method must write the formatted string into it.
    fn fmt(&self, f: &mut Formatter) -> fmt::Result {
        let lat_c = if self.lat >= 0.0 { 'N' } else { 'S' };
        let lon_c = if self.lon >= 0.0 { 'E' } else { 'W' };

        // `write!` is like `format!`, but it will write the formatted string
        // into a buffer (the first argument).
        write!(f, "{}: {:.3}°{} {:.3}°{}",
               self.name, self.lat.abs(), lat_c, self.lon.abs(), lon_c)
    }
}

{:.3}:保留小数点后三位
.abs()：用于获取浮点数的绝对值，确保是正数（和业务有关）。
```



