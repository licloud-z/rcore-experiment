# rcore

## **<center>**第一章</center>

### 构建用户态执行环境

#### 1. #![feature(llvm_asm)] 报错

具体报错为

```
error: an inner attribute is not permitted in this context
  --> src/main.rs:12:1
   |
12 | #![feature(llvm_asm)]
   | ^^^^^^^^^^^^^^^^^^^^^
13 | 
14 | const SYSCALL_EXIT: usize = 93;
   | ------------------------------- the inner attribute doesn't annotate this constant item
   |
   = note: inner attributes, like `#![no_std]`, annotate the item enclosing them, and are usually found at the beginning of source files
help: to annotate the constant item, change the attribute from inner to outer style
   |
12 - #![feature(llvm_asm)]
12 + #[feature(llvm_asm)]
   | 

error[E0658]: use of unstable library feature 'llvm_asm': prefer using the new asm! syntax instead
  --> src/main.rs:19:9
   |
19 |         llvm_asm!("ecall"
   |         ^^^^^^^^
   |
   = note: see issue #70173 <https://github.com/rust-lang/rust/issues/70173> for more information
   = help: add `#![feature(llvm_asm)]` to the crate attributes to enable
```

开始没太看明白报错信息，还以为是RUST停止对llvm_asm!的支持了，搞了很久也没搞好，后来发现问题在于要把#![feature(llvm_asm)]和#![no_std]放在一起，这样第二个error也就转为warning了。部分代码如下：

```
#![no_std]
#![no_main]
#![feature(llvm_asm)]

const SYSCALL_EXIT: usize = 93;
......// rest code
```

#### 2. failed to resolve: use of undeclared crate or module `fmt`

添加输出字符串的宏之后，编译代码，发现上述报错，原因为没有添加core的某些库，所以需要再增加代码：

```
use core::fmt;
use core::fmt::Write;
```

