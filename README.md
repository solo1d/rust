## 安装Rust（Rustup）

安装 Rust 的主要方式是通过 Rustup 这一工具，它既是一个 Rust 安装器又是一个版本管理工具。

可以使用清华大学提供的安装源。https://mirrors.tuna.tsinghua.edu.cn/help/rustup/

```bash
# 先配置国内环境（默认是bash， 如果是 zshrc的话需要进行替换写入文件)
echo 'export RUSTUP_UPDATE_ROOT=https://mirrors.tuna.tsinghua.edu.cn/rustup/rustup' >> ~/.bashrc
echo 'export RUSTUP_DIST_SERVER=https://mirrors.tuna.tsinghua.edu.cn/rustup' >> ~/.bashrc

# 安装 rustup（官方方式）
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
#按提示选择 1（Proceed with installation），完成后执行：
source $HOME/.cargo/env
```



## 更新Rust和Rustup

```bash
#Rust 的升级非常频繁， 可使用下面命令即可更新到最新版本
rustup update

# 更新 rustup 自身
rustup self update
```

#### 卸载rust

```bash
rustup self uninstall
```



### 编译器rustc

单个文件可以使用该编译器

```bash
rustc  main.rs
```





### Cargo

Cargo：Rust 的构建工具和包管理器

在安装 Rustup 时，也会安装 Rust 构建工具和包管理器的最新稳定版，即 Cargo。Cargo 可以做很多事情：

- `cargo build` 可以构建项目
- `cargo run` 可以运行项目
- `cargo test` 可以测试项目
- `cargo doc` 可以为项目构建文档
- `cargo publish` 可以将库发布到 [crates.io](https://crates.io/)。

要检查您是否安装了 Rust 和 Cargo，可以在终端中运行：

```bash
cargo --version
```



### 创建新项目

```bash
# 使用命令即可创建一个 项目， hello-rust
cargo new hello-rust

# 目录结构如下：
hello-rust
|- Cargo.toml		#为 Rust 的清单文件。其中包含了项目的元数据和依赖库。
|- src					#代码目录
  |- main.rs		#源代码
```



## 添加依赖

现在我们来为应用添加依赖。您可以在 [crates.io](https://crates.io/)，即 Rust 包的仓库中找到所有类别的库。在 Rust 中，我们通常把包称作“crates”。

在本项目中，我们使用了名为 [`ferris-says`](https://crates.io/crates/ferris-says) 的库。

我们在 `Cargo.toml` 文件中添加以下信息（从 crate 页面上获取）：

```toml
[dependencies]
ferris-says = "0.3.1"
```

接着运行：

```bash
cargo build
```

…之后 Cargo 就会安装该依赖。

运行此命令会创建一个新文件 `Cargo.lock`，该文件记录了本地所用依赖库的精确版本。

要使用该依赖库，我们可以打开 `main.rs`，删除其中所有的内容（它不过是个示例而已），然后在其中添加下面这行代码：

```rust
use ferris_says::say;
```

这样我们就可以使用 `ferris-says` crate 中导出的 `say` 函数了。





### 安装ESP32开发板驱动相关内容

```bash
# 2. 安装 nightly 与 ESP 专用工具链
# ESP32（Xtensa）需要 esp 定制工具链：
# 安装 nightly（用于 no_std 开发）
rustup toolchain install nightly
rustup component add rust-src --toolchain nightly

# 安装 esp-rs 官方工具链（支持 M1）
curl -L https://github.com/esp-rs/rust-build/releases/latest/download/install-rust-toolchain.sh | bash -s -- --installation-method local

#验证安装
rustup toolchain list
# 应看到 esp、nightly、stable

#安装 ESP 烧录 / 串口工具
# 烧录 + 串口监视器（cargo 集成）
cargo install cargo-espflash espflash --locked
cargo install espup
espup install

# 验证
espflash --version
cargo espflash --version

#四、安装 ESP32 串口驱动（CH340/CP210x）
#1. CH340 驱动（最常见）
#下载：WCH CH34x macOS 驱动（Apple Silicon） https://www.wch.cn/downloads/CH34XSER_MAC_ZIP.html
#安装后重启，插拔 ESP32。
#2. 查看串口设备
```

