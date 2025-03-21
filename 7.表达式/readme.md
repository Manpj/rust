## 块表达式

```rust
1、{}中最后一个表达式不带;号，表示将结果赋值给变量。带；号，表示将()赋值给变量。示例：
fn main() {
    let x = 5u32;

    let y = {
        let x_squared = x * x;
        let x_cube = x_squared * x;

        // This expression will be assigned to `y`
        x_cube + x_squared + x
    };

    let z = {
        // The semicolon suppresses this expression and `()` is assigned to `z`
        2 * x;
    };

    println!("x is {:?}", x);
    println!("y is {:?}", y);
    println!("z is {:?}", z);
}
输出：
x is 5
y is 155
z is ()
```

