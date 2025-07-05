---
title: "Beginner's Guide to Cross-Compiling Rust for Raspberry Pi 4B"
seoTitle: "Cross-Compiling Rust for Raspberry Pi 4B"
seoDescription: "Beginner’s guide to cross-compiling Rust projects for Raspberry Pi 4B, including environment setup, creating projects, and transferring binaries"
datePublished: Wed Jun 26 2024 12:51:54 GMT+0000 (Coordinated Universal Time)
cuid: clxvu4jiw000c0ak441dag4tg
slug: beginners-guide-to-cross-compiling-rust-for-raspberry-pi-4b
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/p-xSl33Wxyc/upload/699484e9f6822d92d0e15aab76b2c961.jpeg
tags: rust, raspberry-pi, embedded-systems, cross-compile

---

#### Introduction

Welcome to my blog! Today, I'll be sharing my experience in creating and cross-compiling a simple project in Rust for the Raspberry Pi 4. This guide will help you understand the steps involved and get you started on your own Rust projects for Raspberry Pi.

> Rust also has the cross tool. "Cross" is a popular tool designed to simplify cross-compiling Rust code for various target architectures. In this tutorial, we will do the cross-compilation without this tool and will cover it in later tutorials.

#### Setting Up the Environment

1. **Install Rust**: Ensure you have Rust installed on your development machine. If not, you can install it using the following command:
    
    ```sh
    $ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    ```
    
2. **Set Up the Rust Target**: To find the architecture running on your Raspberry Pi, you can run the following command on your board:
    
    ```sh
    $ uname -a
    Linux raspberrypi 6.6.28+rpt-rpi-v8 #1 SMP PREEMPT Debian 1:6.6.28-1+rpt1 (2024-04-22) aarch64 GNU/Linux
    ```
    
    As you can see, we are on an aarch64-linux-gnu machine. So, we select the target for the aarch64 architecture, which is needed for the Raspberry Pi 4.:
    
    ```sh
    $ rustup target add aarch64-unknown-linux-gnu
    ```
    

To see all supporting targets, look at the [rustc documentation](https://doc.rust-lang.org/rustc/platform-support.html)

3. **Install the GCC Toolchain**: You need the GCC toolchain for cross-compilation. On Ubuntu, you can install it with:
    
    ```sh
    $ sudo apt-get install gcc-aarch64-linux-gnu
    ```
    

#### Creating a Simple Rust Project

1. **Create a New Rust Project**: Use Cargo to create a new Rust project:
    
    ```sh
    $ cargo new hello_rust_pi
    $ cd hello_rust_pi
    ```
    
2. **Write Your Rust Code**: Open `src/main.rs` and write a simple "Hello, world!" program:
    
    ```rust
    fn main() {
        println!("Hello, Raspberry Pi!");
    }
    ```
    

#### Cross-Compiling the Project

1. **Configure Cargo for Cross-Compilation**: Create a `.cargo/config.toml` file in your project directory with the following content:
    
    ```toml
    [target.aarch64-unknown-linux-gnu]
    linker = "aarch64-linux-gnu-gcc"
    ```
    
2. **Build the Project**: Cross-compile your project using Cargo:
    
    ```sh
    $ cargo build --target=aarch64-unknown-linux-gnu
    ```
    
3. **Transfer the Binary**: After a successful build, transfer the binary to your Raspberry Pi using SCP:
    
    ```sh
    scp target/aarch64-unknown-linux-gnu/debug/hello_rust_pi pi@<your_pi_ip>:/home/pi/
    ```
    

#### Running the Project on Raspberry Pi

1. **Connect to Your Raspberry Pi**: SSH into your Raspberry Pi:
    
    ```sh
    ssh pi@<your_pi_ip>
    ```
    
2. **Run the Binary**: Make the binary executable and run it:
    
    ```sh
    $ chmod +x hello_rust_pi
    $ ./hello_rust_pi
    ```
    
    You should see the output:
    
    ```sh
    Hello, Raspberry Pi!
    ```
    

#### Conclusion

Congratulations! You've successfully created and cross-compiled a Rust project for the Raspberry Pi 4. This guide covered the basics, and there's so much more to explore in the world of Rust. Stay tuned for more tutorials and projects as I continue my Rust programming journey.