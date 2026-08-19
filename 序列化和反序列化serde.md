序列化是将内存数据类型转换为字节序列的过程，反序列化则与此相反，这意味着它可以读取数据。

很多编程语言都为将数据结构转换为字节序列提供了支持。

serde 的强大之处在于它能够在编译期生成任何支持的类型的序列化数据，并且深度依赖过程宏。大多数情况下序列化和反序列化对 serde 都是零成本操作。

```toml
# Cargo.toml
[package]
name = "serde_demo"
version = "0.1.0"
edition = "2024"

[dependencies]
serde = "1.0.84"
serde_derive = "1.0.84"
serde_json = "1.0.36"
```

```rust
// main.rs
use serde_derive::{Serialize, Deserialize};

#[derive(Debug, Serialize, Deserialize)]
struct Foo {
    a :String,
    b :u64
}

impl Foo {
    fn new(a: &str, b:u64)->Self {
        Self { a: a.to_string(), b }
    }
}

fn main() {
    let foo_json: String = 
        serde_json::to_string(&Foo::new("It`s that simple",101)).unwrap();
    println!("{:?}", foo_json);
    let foo_value : Foo = serde_json::from_str(&foo_json).unwrap();
    println!("{:?}", foo_value);
}
/*
输出：
"{\"a\":\"It`s that simple\",\"b\":101}"
Foo { a: "It`s that simple", b: 101 }
*/
```



