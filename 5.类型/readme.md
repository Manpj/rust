## 基本类型转换

```rust
1、应该显示类型转换，隐式类型转换会报错。如：
	let decimal = 65.4321_f32;

    // Error! No implicit conversion
    let integer: u8 = decimal;
    // FIXME ^ Comment out this line
2、浮点类型不能直接转换为char。如：
    // Error! There are limitations in conversion rules.
    // A float cannot be directly converted to a char.
    let character = decimal as char;
    // FIXME ^ Comment out this line
3、转换任何值到无符号整型规则，
	3.1、T：MAX+1 加或减，直到在范围之内。如：
	fn main() {
        // 示例 1: 将负数转换为 u8 类型
        let negative_num: i32 = -1;
        let converted_negative: u8 = negative_num as u8;
        println!("Converted negative number: {}", converted_negative); // 输出 255

        // 示例 2: 将超出 u8 范围的值转换为 u8 类型
        let large_num: i32 = 300;
        let converted_large: u8 = large_num as u8;
        println!("Converted large number: {}", converted_large); // 输出 44
    }
	示例 1：-1 是负数，u8 类型的 T::MAX 为 255，T::MAX + 1 就是 256。不断给 -1 加上 256，得到 -1 + 256 = 255，255 处于 u8 类型的取值范围（0 到 255）内，所以 -1 转换为 u8 类型的结果是 255。
	示例 2：300 超出了 u8 类型的取值范围，u8 类型的 T::MAX 为 255，T::MAX + 1 是 256。不断从 300 中减去 256，即 300 - 256 = 44，44 在 u8 类型的取值范围内，所以 300 转换为 u8 类型的结果是 44。
	3.2、3.1方法和正数进行取模运算（modulus operation）的效果是相同。
4、将值转换为有符号类型的规则和原理
	4.1、先转换为对应的无符号类型，再根据最高位判断正负。如：
    fn main() {
        // 示例 1: 转换为有符号类型后最高位为 0
        let num1: u8 = 100;
        let num1_i8: i8 = num1 as i8;
        println!("num1 (u8: {}): i8 value is {}", num1, num1_i8);

        // 示例 2: 转换为有符号类型后最高位为 1
        let num2: u8 = 200;
        let num2_i8: i8 = num2 as i8;
        println!("num2 (u8: {}): i8 value is {}", num2, num2_i8);
    }	
	示例 1：num1 的值是 100，它的二进制表示（8 位）是 0110_0100。将其转换为 i8 类型时，最高位是 0，所以在 i8 类型中它仍然表示正数 100。
	示例 2：num2 的值是 200，它的二进制表示（8 位）是 1100_1000。将其转换为 i8 类型时，最高位是 1，所以在 i8 类型中它表示负数。在补码表示中，1100_1000 对应的十进制负数是 -56。
	4.2、特殊情况，在范围内直接转换。如：
	// Unless it already fits, of course.
    println!(" 128 as a i16 is: {}", 128 as i16);
5、浮点转换为int（有符号或者无符号）
	使用饱和转换的原则。即超过最大值使用最大值，小于最小值使用最小值。如：
    // 300.0 as u8 is 255
    println!(" 300.0 as u8 is : {}", 300.0_f32 as u8);
    // -100.0 as u8 is 0
    println!("-100.0 as u8 is : {}", -100.0_f32 as u8);
    // nan as u8 is 0
    println!("   nan as u8 is : {}", f32::NAN as u8);
6、unsafe使用
	使用unsafe取消了检查，所有会增加效率，但是使用的时候要确保不会发生未知情况，否则会出现未知错误。如：
	// This behavior incurs a small runtime cost and can be avoided
    // with unsafe methods, however the results might overflow and
    // return **unsound values**. Use these methods wisely:
    unsafe {
        // 300.0 as u8 is 44
        println!(" 300.0 as u8 is : {}", 300.0_f32.to_int_unchecked::<u8>());
        // -100.0 as u8 is 156
        println!("-100.0 as u8 is : {}", (-100.0_f32).to_int_unchecked::<u8>());
        // nan as u8 is 0
        println!("   nan as u8 is : {}", f32::NAN.to_int_unchecked::<u8>());
    }

```

