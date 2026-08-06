let 语句是一个模式匹配语句。模式匹配是一种主要在类似Haskell 等函数式语言中出现的结构，它允许我们根据内部结构对值进行操作和判断，或者可以用于从代数数据类型中提取值。
- 在日常代码中，优先使用 & 和 &mut（如 let x = &y;），因为它们是表达式，读起来更自然。
- 只在解构结构体、元组或枚举时，才考虑使用 ref 和 ref mut，这是它们真正的用武之地。ref 通常不用于声明变量绑定，而主要用于 match 表达式中。
```rust
#[derive(Debug)]
struct Items(u32);

fn main(){
    let items = Items(2);
    
    // &items这是表达式，明确地对一个已有的变量调用“取地址”操作
    let items_ptr = &items;
    
    // 右侧ref 是模式匹配中的关键字，表示不要转移（move）右侧值的所有权，也不要复制它，请借用（Borrow）它，并把引用绑定到这个变量名上
    let ref items_ref = items;
    
    assert_eq!(items_ptr as *const Items, items_ref as *const Items);

    let mut a = Items(20);
    {
    // let ref mut b = a; 完全等价于 let b = &mut a; 生成了一个可变引用 &mut Items 绑定到 b
    // 大括号必须存在，分割作用域，否则会出现不能同时存在可变借用和不可变借用的报错。
        let ref mut b = a;
        b.0 += 25;
    }
    println!("{:?}", items);
    println!("{:?}", a);
}
```

```rust
enum Food {Pizza,Salad}
enum PayMentMode{Bitcoin, Credit }
struct Order {
    count: u8, item:Food, payment:PayMentMode
}
fn main(){
    let food_order = Order { count:2, 
                                    item: Food::Salad,
                                    payment: PayMentMode::Credit 
    };
    // let 可以通过模式匹配将内部字段添加到新的变量中，可以在变量名之前添加ref进行引用，
    // 还可以在右侧添加引用&， 不关心的字段可以使用“..”予以忽略
    let Order {ref count, item, .. } = food_order;
    println!("{}", count);
}
```

```rust
struct Person(String);

fn main(){
    let  a = Person("Ris".to_string());
    match a {
        // 在这种情况下，必须使用 ref 来将其解构为引用
        // 只是借用，a 的所有权完整保留，a 依然有效
        // ref: 创建内部字段的 不可变引用 并绑定, 只需要读取，不想破坏原结构
        Person(ref name) => println!("{} was", name),
        _ => panic!("Oh no!")
    }
    let b = a ;
    /*
    Person(ref name) 中的 ref 告诉 Rust：在匹配 Person 结构体时，
        不要将它内部的 String 的所有权移动 （move）到 name 变量中，
        而是仅创建一个指向该 String 的不可变引用 （&String），并将该引用绑定到 name。
    */
}
```

```rust
enum Container {
    Item(u64),
    Empty
}

fn main(){
    let maybe = Container::Item(0u64);
    // 如果 maybe 是 Container::Item 且内部的值恰好是 0，则执行真分支；否则执行假分支
    let has_item = if let Container::Item(0) = maybe {
        true
    } else {
        false
    };
    println!("{}",has_item);
}
```
```rust
struct Container {
    items_count:u32
}

fn incr_item(&mut Container { ref mut items_count, .. }: &mut Container) {
    *items_count += 1;  // items_count 是 &mut u32，需要解引用
}

fn calc_item(Container { items_count }: &Container) -> u32{
    let rate = 67;
    rate * items_count
}

fn main(){
    let mut container = Container {
        items_count: 10
    };
    incr_item(&mut container);
    let total = calc_item(&container);
    println!("Total cost: {}", total);
}
```



