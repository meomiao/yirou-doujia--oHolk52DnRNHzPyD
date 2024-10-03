[合集 \- rCore(29\)](https://github.com)[1\.\[rCore学习笔记 00]总览07\-09](https://github.com/chenhan-winddevil/p/18292624)[2\.\[rCore学习笔记 01]安装VMwareWorkStationPro08\-15](https://github.com/chenhan-winddevil/p/18361426)[3\.\[rCore学习笔记 02]Ubuntu 22虚拟机安装07\-09](https://github.com/chenhan-winddevil/p/18292628)[4\.\[rCore学习笔记 03]配置rCore开发环境07\-09](https://github.com/chenhan-winddevil/p/18292632)[5\.\[rCore学习笔记 04]安装SSH07\-09](https://github.com/chenhan-winddevil/p/18292646)[6\.\[rCore学习笔记 05]第0章作业题07\-09](https://github.com/chenhan-winddevil/p/18292647)[7\.\[rCore学习笔记 06]运行Lib\-OS07\-09](https://github.com/chenhan-winddevil/p/18292649)[8\.\[rCore学习笔记 07]移除标准库依赖07\-09](https://github.com/chenhan-winddevil/p/18292655)[9\.\[rCore学习笔记 08]内核第一条指令07\-09](https://github.com/chenhan-winddevil/p/18292657)[10\.\[rCore学习笔记 09]为内核支持函数调用07\-09](https://github.com/chenhan-winddevil/p/18292659)[11\.\[rCore学习笔记 010]基于 SBI 服务完成输出和关机07\-09](https://github.com/chenhan-winddevil/p/18292660)[12\.\[rCore学习笔记 011]第一章作业题07\-09](https://github.com/chenhan-winddevil/p/18292661)[13\.\[rCore学习笔记 012]彩色化LOG07\-09](https://github.com/chenhan-winddevil/p/18292662)[14\.\[rCore学习笔记 013]GDB跟踪程序07\-09](https://github.com/chenhan-winddevil/p/18292663)[15\.\[rCore学习笔记 014]批处理系统07\-10](https://github.com/chenhan-winddevil/p/18293037)[16\.\[rCore学习笔记 015]特权级机制07\-14](https://github.com/chenhan-winddevil/p/18301570)[17\.\[rCore学习笔记 016]实现应用程序07\-20](https://github.com/chenhan-winddevil/p/18312966):[FlowerCloud机场](https://hushicha.org)[18\.\[rCore学习笔记 017]实现批处理操作系统07\-23](https://github.com/chenhan-winddevil/p/18319595)[19\.\[rCore学习笔记 018]实现特权级的切换07\-27](https://github.com/chenhan-winddevil/p/18327630)[20\.\[rCore学习笔记 019]在main中测试本章实现07\-30](https://github.com/chenhan-winddevil/p/18332741)[21\.\[rCore学习笔记 020]第二章作业08\-01](https://github.com/chenhan-winddevil/p/18336793)[22\.\[rCore学习笔记 021]多道程序与分时任务08\-04](https://github.com/chenhan-winddevil/p/18341331)[23\.\[rCore学习笔记 022]多道程序与分时任务08\-06](https://github.com/chenhan-winddevil/p/18345564)[24\.\[rCore学习笔记 023]任务切换08\-08](https://github.com/chenhan-winddevil/p/18348681)[25\.\[rCore学习笔记 024]多道程序与协作式调度08\-10](https://github.com/chenhan-winddevil/p/18352689)[26\.\[rCore学习笔记 025]分时多任务系统与抢占式调度08\-21](https://github.com/chenhan-winddevil/p/18370723)[27\.\[rCore学习笔记 026]第三章作业09\-14](https://github.com/chenhan-winddevil/p/18414400)[28\.\[rCore学习笔记 027]地址空间09\-18](https://github.com/chenhan-winddevil/p/18417761)29\.\[rCore学习笔记 028] Rust 中的动态内存分配10\-01收起
# 引言


想起我们之前在学习C的时候,总是提到`malloc`,总是提起,使用`malloc`现场申请的内存是属于**堆**,而直接定义的变量内存属于**栈**.


还记得当初学习STM32的时候CubeIDE要设置`stack` 和`heap`的大小.


但是我们要记得,这么好用的功能,实际上是**操作系统在负重前行**.


那么为了实现动态内存分配功能,操作系统需要有如下功能:


* 初始时能提供一块大内存空间作为初始的“堆”。在没有分页机制情况下，这块空间是物理内存空间，否则就是虚拟内存空间。
* 提供在堆上分配和释放内存的函数接口。这样函数调用方通过分配内存函数接口得到地址连续的空闲内存块进行读写，也能通过释放内存函数接口回收内存，以备后续的内存分配请求。
* 提供空闲空间管理的连续内存分配算法。相关算法能动态地维护一系列空闲和已分配的内存块，从而有效地管理空闲块。
* （可选）提供建立在堆上的数据结构和操作。有了上述基本的内存分配与释放


# 动态内存分配


## 实现方法


[动态内存分配的实现方法](https://github.com):



> 应用另外放置了一个大小可以随着应用的运行动态增减的内存空间 – 堆（Heap）。同时，应用还要能够将这个堆管理起来，即支持在运行的时候从里面分配一块空间来存放变量，而在变量的生命周期结束之后，这块空间需要被回收以待后面的使用。如果堆的大小固定，那么这其实就是一个连续内存分配问题，同学们可以使用操作系统课上所介绍到的**各种连续内存分配算法**。


## 内存碎片


[动态内存分配的弊端\-\-\-内存碎片](https://github.com):



> 应用进行多次不同大小的内存分配和释放操作后，会产生内存空间的浪费，即存在无法被应用使用的空闲内存碎片。


内存碎片是指无法被分配和使用的空闲内存空间。可进一步细分为内碎片和外碎片：


* 内碎片：已被分配出去（属于某个在运行的应用）内存区域，占有这些区域的应用并不使用这块区域，操作系统也无法利用这块区域。
* 外碎片：还没被分配出去（不属于任何在运行的应用）内存空闲区域，由于太小而无法分配给提出申请内存空间的应用。


## STD库中的动态内存分配


[这里](https://github.com)首先提到了在**STD**库中的堆相关的数据结构.可以自行阅读并且大概理解下图.


![](https://img2024.cnblogs.com/blog/3071041/202410/3071041-20241001133558797-252701878.png)


但是这一部分向我们传达的信息是:


1. rust编程可以很优雅地实现动态内存管理
2. std库提供了动态内存管理的方法
3. 但是我们的操作系统内核只能使用rust的core库来实现,因此需要重视和借用这些std库里的方法


## 在内核中支持动态内存分配


如上部分所说:



> 上述与堆相关的智能指针或容器都可以在 Rust 自带的 `alloc` crate 中找到。当我们使用 Rust 标准库 `std` 的时候可以不用关心这个 crate ，因为标准库内已经已经实现了一套堆管理算法，并将 `alloc` 的内容包含在 `std` 名字空间之下让开发者可以直接使用。然而操作系统内核运行在禁用标准库（即 `no_std` ）的裸机平台上，核心库 `core` 也并没有动态内存分配的功能，这个时候就要考虑利用 `alloc` 库定义的接口来实现基本的动态内存分配器。


具体实现这个**动态内存分配器**,是为自己实现的这个**结构体**,实现`GlobalAlloc`的`Trait`.



> `alloc` 库需要我们提供给它一个 `全局的动态内存分配器` ，它会利用该分配器来管理堆空间，从而使得与堆相关的智能指针或容器数据结构可以正常工作。具体而言，我们的动态内存分配器需要实现它提供的 `GlobalAlloc` Trait


`GlobalAlloc`的抽象接口:



```
// alloc::alloc::GlobalAlloc

pub unsafe fn alloc(&self, layout: Layout) -> *mut u8;
pub unsafe fn dealloc(&self, ptr: *mut u8, layout: Layout);

```


> 可以看到，它们类似 C 语言中的 `malloc/free` ，分别代表堆空间的分配和回收，也同样使用一个裸指针（也就是地址）作为分配的返回值和回收的参数。两个接口中都有一个 `alloc::alloc::Layout` 类型的参数， 它指出了分配的需求，分为两部分，分别是所需空间的大小 `size` ，以及返回地址的对齐要求 `align` 。这个对齐要求必须是一个 2 的幂次，单位为字节数，限制返回的地址必须是 `align` 的倍数。


### 具体编程实现


#### 引入已有的内存分配器库


在`os/Cargo.toml`中引入:



```
buddy_system_allocator = "0.6"

```

#### 引入`alloc`库


在`os/src/main.rs`中引入.



```
// os/src/main.rs

extern crate alloc;

```

#### 实例化全局动态内存分配器


创建`os/src/mm/heap_allocator.rs`.



```
// os/src/mm/heap_allocator.rs

use buddy_system_allocator::LockedHeap;
use crate::config::KERNEL_HEAP_SIZE;

#[global_allocator]
static HEAP_ALLOCATOR: LockedHeap = LockedHeap::empty();

static mut HEAP_SPACE: [u8; KERNEL_HEAP_SIZE] = [0; KERNEL_HEAP_SIZE];

pub fn init_heap() {
    unsafe {
        HEAP_ALLOCATOR
            .lock()
            .init(HEAP_SPACE.as_ptr() as usize, KERNEL_HEAP_SIZE);
    }
}

```

可以看到**实例化**了一个静态变量`HEAP_ALLOCATOR`.并且**实例化**了一个数组`HEAP_SPACE`来作为它的**堆**.


其中.`HEAP_SPACE`的大小为`KERNEL_HEAP_SIZE`.


那么这个`KERNEL_HEAP_SIZE`是取自`config`这个包的.


这里根据[代码仓库里的代码](https://github.com)来设置`KERNEL_HEAP_SIZE`的大小.



```
// os/src/config.rs
pub const KERNEL_HEAP_SIZE: usize = 0x30_0000;

```

大小为`3145728`.


#### 标注全局动态内存分配器的语义项


注意上一段的代码,要标注`#[global_allocator]`这样这里的内存分配器才能被识别为全局动态内存分配器.



```
#[global_allocator]

```

#### 处理动态内存分配失败的情形


需要**开启条件编译**,所以需要在`main.rs`里声明:



```
#![feature(alloc_error_handler)]

```

这时候就可以在`os/src/mm/heap_allocator.rs`里创建处理函数了:



```
// os/src/mm/heap_allocator.rs

#[alloc_error_handler]
pub fn handle_alloc_error(layout: core::alloc::Layout) -> ! {
    panic!("Heap allocation error, layout = {:?}", layout);
}

```

### 测试实现效果


#### 创建测试函数


在`os/src/mm/heap_allocator.rs`里创建测试函数.



```
#[allow(unused)]
pub fn heap_test() {
    use alloc::boxed::Box;
    use alloc::vec::Vec;
    extern "C" {
        fn sbss();
        fn ebss();
    }
    let bss_range = sbss as usize..ebss as usize;
    let a = Box::new(5);
    assert_eq!(*a, 5);
    assert!(bss_range.contains(&(a.as_ref() as *const _ as usize)));
    drop(a);
    let mut v: Vec<usize> = Vec::new();
    for i in 0..500 {
        v.push(i);
    }
    for i in 0..500 {
        assert_eq!(v[i], i);
    }
    assert!(bss_range.contains(&(v.as_ptr() as usize)));
    drop(v);
    println!("heap_test passed!");
}

```

这里的`#[allow(unused)]`很有意思,可以**阻止编译器对你因为调试暂时不调用的函数报错**.


这里注意使用了`println`,在文件最上边加一句`use crate::println`.


这里的测试程序先获取了`sbss`的到`ebss`的范围.


这里回顾清零`bss`段的代码:



> 1. `bss`段本身是一个储存未初始化的全局变量的内存区域
> 2. `sbss`是`bss`的开头`ebss`是`bss`的结尾


那么`bss_range`实际上是`bss`的范围.


根据[这里](https://github.com),理解`Box::new(5)`是尝试在堆上储存`a`的值,且这个值为`5`.


那么下边的断言语句:



```
assert_eq!(*a, 5);
assert!(bss_range.contains(&(a.as_ref() as *const _ as usize)));

```

就很好理解了:



> 1. 判断`a`的值是否为`5`
> 2. 判断`a`的指针是否在`bss`的范围内


随后的操作则是创建了一个`Vec`容器,然后储存了`0..500`的值进去,并且分别执行上述对`a`的断言判断.


如果断言没有报错,那么最后自然会输出`heap_test passed!`.


(最后注意`drop`是在堆(动态内存)里释放掉某个变量)


#### 使mm包可调用


在`os/src/mm`下创建`mod.rs`使得`mm`可以被识别为一个包.


为了使用`heap_allocator`里的`init_heap`和`heap_test`,需要公开声明这个`mod`:



```
// os/src/mm/mod.rs
pub mod heap_allocator;

```

#### 编辑`main`函数,实现测试



```
// os/src/main.rs

/// the rust entry-point of os
#[no_mangle]
pub fn rust_main() -> ! {
    clear_bss();
    println!("[kernel] Hello, world!");
    logging::init();
    println!("[kernel] logging init end");
    mm::heap_allocator::init_heap();
    println!("[kernel] heap init end");
    mm::heap_allocator::heap_test();
    println!("heap test passed");
    trap::init();
    println!("[kernel] trap init end");
    loader::load_apps();
    trap::enable_timer_interrupt();
    timer::set_next_trigger();
    task::run_first_task();
    panic!("Unreachable in rust_main!");
}

```

#### 运行测试



```
cd os
make run

```

得到运行结果:



```
[rustsbi] RustSBI version 0.3.1, adapting to RISC-V SBI v1.0.0
.______       __    __      _______.___________.  _______..______   __
|   _  \     |  |  |  |    /       |           | /       ||   _  \ |  |
|  |_)  |    |  |  |  |   |   (----`---|  |----`|   (----`|  |_)  ||  |
|      /     |  |  |  |    \   \       |  |      \   \    |   _  < |  |
|  |\  \----.|  `--'  |.----)   |      |  |  .----)   |   |  |_)  ||  |
| _| `._____| \______/ |_______/       |__|  |_______/    |______/ |__|
[rustsbi] Implementation     : RustSBI-QEMU Version 0.2.0-alpha.2
[rustsbi] Platform Name      : riscv-virtio,qemu
[rustsbi] Platform SMP       : 1
[rustsbi] Platform Memory    : 0x80000000..0x88000000
[rustsbi] Boot HART          : 0
[rustsbi] Device Tree Region : 0x87000000..0x87000f02
[rustsbi] Firmware Address   : 0x80000000
[rustsbi] Supervisor Address : 0x80200000
[rustsbi] pmp01: 0x00000000..0x80000000 (-wr)
[rustsbi] pmp02: 0x80000000..0x80200000 (---)
[rustsbi] pmp03: 0x80200000..0x88000000 (xwr)
[rustsbi] pmp04: 0x88000000..0x00000000 (-wr)
[kernel] Hello, world!
heap_test passed!
[kernel] IllegalInstruction in application, kernel killed it.
All applications completed!

```

这里我为了log比较简短,把`user`里需要编译的app只保留了一个`user/src/bin/00hello_world.rs`.


这里看log,`heap_test passed!`,说明测试成功了.


