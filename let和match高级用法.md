# 高级匹配模式和守护

就是 match 和 let模式的高级用法。

### 匹配守护

匹配守护（Match Guard）就是匹配臂后面那个额外的 `if` 条件，它用来给模式匹配再加一层“过滤器”。

- **匹配守护的本质**

  - **位置**：在模式（Pattern）和箭头（`=>`）之间。

  - **作用**：只有当**模式匹配成功** **且** **守护条件为 `true`** 时，才会执行该分支。

  - **语法**：`pattern if condition => expression,`

一定要小心在守护中使用了模式匹配出来的**非 `Copy` 类型**（比如 `String`），该值会被**移动（move）**到守护条件中，导致后续分支无法再使用它。

```rust
fn main() {
    let code = 404;

    // 匹配 400 到 500 之间（含两端）
    match code {
        c if c >= 400 && c <= 500 => {
            println!("命中 400~500 区间: {}", c);
        }
        _ => println!("不在此区间"),
    }
    
    //绑定并过滤内部值， 解构出来的变量
    let num = Some(10);

    match num {
        // 模式匹配出 Some 里的值赋给 n，然后判断 n 是否大于 5
        Some(n) if n > 5 => println!("数字大于5: {}", n),
        Some(n) => println!("数字小于等于5: {}", n),
        None => println!("空值"),
    }

    let s = Some(String::from("Hello"));

    // 穷尽性检查（Exhaustiveness）：在 match 中，你必须把所有可能的情况都列出来，否则编译不通过。这极大地避免了忘记处理空值（Null Pointer Exception）之类的崩溃。
    match s {
        // 这里 Some(val) 中的 val 是 String
        // 如果在守护中调用 val.len()，val 只是被借用（&String），没问题
        Some(val) if val.len() > 5 => println!("长度大于5"),
        // 这里 val 依然可用，因为上面只是借用了 len()
        Some(val) => println!("长度小于等于5: {}", val),
        None => println!("空"),
    }
    // println!("s:{}", s); // 这里的s已经无法使用了。
}
```

### 高级let构造

“高级 `let` 构造”在 Rust 语境下，通常指**将 `let` 语句与模式匹配（pattern matching）结合使用**，而不仅仅是简单的变量绑定。它允许你在一个 `let` 声明中解构（destructure）复杂的数据结构，从而直接提取内部的值。

**`let` 本质上是模式匹配**

编译器会检查**模式**（左边）是否与**值**（右边）的结构相匹配。这种能力就是“高级 `let` 构造”的基础。

```rust
//每个 let 绑定，其实都是模式匹配的简化形式：
let x = 5;        // 模式 x 匹配值 5，绑定成功
let (a, b) = (1, 2); // 模式 (a, b) 匹配元组 (1, 2)
struct Inner { val: i32 }
struct Outer { inner: Inner }
let outer = Outer { inner: Inner { val: 42 } };
let Outer { inner: Inner { val } } = outer; // 一步到位拿到 val , val = 42
```

```rust
// 条件绑定
if let Some(x) = optional {
    println!("有值: {}", x);
}

while let Ok(data) = reader.read() {
    // 处理 data
}

let Some(x) = optional else {  // 当模式匹配失败时执行 else 分支并跳出
    println!("没有值");
    return;
};


// while let：只要模式能匹配上，就一直循环。
let mut stack = vec![1, 2, 3];
while let Some(top) = stack.pop() {
    println!("弹出: {}", top); // 依次弹出 3, 2, 1
}

// 调用时传入元组，函数内部直接拿到了 x 和 y
fn print_coordinates((x, y): (i32, i32)) {
    println!("x: {}, y: {}", x, y);
}

```

