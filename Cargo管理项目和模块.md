# Cargo管理项目

## 模块

### 嵌套模块

```rust
mod food {
    pub struct Cake;
    struct Smoothie;
    struct Pizza;
}
use  food::Cake;

fn main(){
    let eatable = Cake;
}
```

### 将文件用作模块

- 绝对导入  `use crate::当前项目的根目录下的模块`   ，这个是绝对导入前缀，指向当前项目的根目录
- 相对导入：
  - 相对导入前缀  `use self::当前模块相关的元素` 
  - 相对导入前缀  `use super::可以用于从父模块导入元素` 

  两个在相同目录下的文件 `main.rs` 和 `foo.rs` ，一个是模块，一个是使用模块。

```rust
// foo.rs
pub struct Bar ;  // 定义一个结构体，并变成公有的，让外部可以访问

impl Bar {
    pub fn  init(){  // 一个方法
        println!("Bar type initialized!");
    }
}
```

```rust
// main.rs
mod foo;   // 声明模块 foo
use crate::foo::Bar;	// 绝对导入模块

fn main(){
    let _bar = Bar::init();
}
```

## Cargo 与依赖项 

Cargo 的构建命令，它主要用于编译、链接及构建我们的项目。

Cargo 是通过两个文件来管理 Rust 项目的：Cargo.toml 文件、 Cargo.lock文件。

```bash
# 相关命令：

# 创建新项目， hello-rust（可运行），默认使用git进行版本控制
cargo new hello-rust
cargo new --lib hello-lib   # 动态库项目


# 更新全部依赖
cargo update
# 更新部分依赖
cargo update -p 依赖包名


# 只通过前端阶段运行代码，即编译器的解析和语义分析
cargo check

# 构建项目
cargo build
cargo build --release  #生产版本


# 构建项目并运行（默认是debug模式）， 生成文件位置在 target/debug/ 目录下
cargo run 
cargo run --release  #生产版本


# lib库启动测试（会自动执行#[test] 下的函数）
cargo test 
# 运行指定测试函数it_works （默认新建的lib会自动生成该测试，可执行运行该命令）
cargo test --package myexponent --lib -- tests::it_works --exact --nocapture --include-ignored 
# 运行在项目根目录下 examples 中的文件 main函数（可以写lib包使用范例 basic.rs 文件）
cargo run --example basic
```

