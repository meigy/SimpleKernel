# 内存段页机制

我们知道，对CPU来讲，系统中的所有储存器中的储存单元都处于一个统一的逻辑储存器中，它的容量受CPU寻址能力的限制。这个逻辑储存器就是我们所说的线性地址空间。8086有20位地址线，拥有1MB的线性地址空间。而80386有32位地址线，拥有4GB的线性地址空间。但是80386依旧保留了8086采用的地址分段的方式，只是增加了一个折中的方案，即只分一个段，段基址0×00000000，段长0xFFFFFFFF（4GB），这样的话整个线性空间可以看作就一个段，这就是所谓的平坦模型（Flat Mode）。

分段策略：

首先是对内存分段中每一个段的描述，实模式对于内存段并没有访问控制，任意的程序可以修改任意地址的变量，而保护模式需要对内存段的性质和允许的操作给出定义，以实现对特定内存段的访问检测和数据保护。考虑到各种属性和需要设置的操作，32位保护模式下对一个内存段的描述需要8个字节，其称之为段描述符（Segment Descriptor）。段描述符分为数据段描述符、指令段描述符和系统段描述符三种。



#### **全局描述符表GDT（Global Descriptor Table）**

在整个系统中，全局描述符表GDT只有一张(一个处理器对应一个GDT)，GDT可以被放在内存的任何位置，但CPU必须知道GDT的入口，也就是基地址放在哪里，Intel的设计者门提供了一个**寄存器GDTR**用来存放GDT的入口地址，程序员将GDT设定在内存中某个位置之后，可以通过**LGDT指令**将GDT的入口地址装入此寄存器，从此以后，CPU就根据此寄存器中的内容作为GDT的入口来访问GDT了。GDTR中存放的是GDT在内存中的基地址和其表长界限。



通过将GDT告诉给CPU后，CPU就知道了操作系统中段的设置，从而可以通过段选择子得到线性地址，在后面实现分页管理后，可进一步将线性地址转换为物理地址（不过当前连物理 址有多大都没法知道呢，在后面会解决）。
