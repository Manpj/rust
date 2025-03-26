## 依赖

```rust
1、创建rust项目
# A binary
cargo new foo

# A library
cargo new --lib bar

2、cargo.html文件介绍
version：crate version number using Semantic Versioning
authors： a list of authors used when publishing the crate
dependencies： section lets you add dependencies for your project

3、依赖可以使用clap
示例：
[package]
name = "foo"
version = "0.1.0"
authors = ["mark"]

[dependencies]
clap = "2.27.1" # from crates.io
rand = { git = "https://github.com/rust-lang-nursery/rand" } # from online repo
bar = { path = "../bar" } # from a path in the local filesystem

```

## 约定

```rust
添加额外的main文件
--bin my_other_bin

更多的解构参考：https://doc.rust-lang.org/cargo/guide/project-layout.html
```

## 测试

```rust
1、unit、doc、itegration 三种不同测试风格
2、测试命令：
$ cargo test
3、多个测试如果写入到相同文件，打印结果会有并发的方式，不是一个测试一个测试打印
```

## 创建脚本

```rust
参考：
https://doc.rust-lang.org/rust-by-example/cargo/build_scripts.html
```

