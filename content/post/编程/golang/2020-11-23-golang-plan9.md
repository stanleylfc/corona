+++
title="go 实战（十）| go plan9"
tags=["golang"]
categories=["golang"]
date="2020-11-23T11:00:00+08:00"
+++

## 1.go 汇编简介
### 1.1 虚拟寄存器
- FP(Frame pointer): arguments and locals
- PC(Program counter): jumps and branches.
- SB(Static base pointer): global symbols.
- SP(Stack pointer): top of stack.
- 所有用户空间的数据都可以通过FP(局部数据)或SB(全局数据)访问

#### 1. SB
- SB抽象了内存空间，foo(SB)的意思是用foo来代表内存里面的一个地址
- foo(SB)可以用来定义全局的function和数据
- foo<>(SB)和C里面的static一个效果，声明只在当前源码文件中可见
- 可以在引用上加上偏移量，如前面说的foo(SB)，如果foo+4(SB)的意思是foo + 4 bytes的地址
```
1. TEXT runtime·_divu(SB), NOSPLIT, $16-0 在这种情况下，TEXT、·、SB共同作用声明了一个函数 runtime._divu，
这种情况下，不能对SB进行解引用。

2. GLOBL fast_udiv_tab<>(SB), RODATA, $64 在这种情况下，GLOBL、fast_udiv_tab、SB共同作用， 
在RODATA段声明了一个私有全局变量fast_udiv_tab，大小为64byte，此时可以对SB进行偏移、解引用。

3. CALL runtime·callbackasm1(SB) 在这种情况下，CALL、runtime·callbackasm1、SB共同标识， 
标识调用了一个函数runtime·callbackasm1。

4. MOVW $fast_udiv_tab<>-64(SB), RM 在这种情况下，与2类似，但不是声明，是解引用全局变量 fast_udiv_tab
```

#### 2. FP 
- FP用来访问函数的参数和函数返回值
- 编译器维护了栈上的参数指针
- 0(FP)就是function的第一个参数
- 64位系统上8(FP)就是第二个参数，后面加上偏移量就可以访问更多的参数
- 要访问具体function的参数，需要加上name，比如foo+0(FP)获取foo的第一个参数,foo+8(FP)获取第二个参数
- 编译器中强制要求必须用name来访问FP

#### 3. SP 
- SP是栈指针，和NASM中的RSP类似
- SP指向当前local stack frame的栈顶，使用时foo-8(SP)代表foo的栈第8byte
- 如果硬件支持SP寄存器，那么不加name的时候就是访问硬件寄存器.
- 因此 x-8(SP)和-8(SP)访问的会是不同的内存空间
- 对SP和PC的访问都应该带上name，如果要访问对应的硬件寄存器，可以用RSP

#### 4. pc
- 程序跳转和流控制是通过跳转PC的偏移量或者label实现的， label只对当前的function可见：
```go
next:
  MOVW $0, R1
  JMP  next
```
### 1.1.2 通用寄存器
- CALLER BP 由编辑器“透明”插入，因此，不算在当前函数的栈空间内,在编译后，栈空间会被+8用于存储BP寄存器，这步骤由编译器自动添加
- 虽然rbp和rsp也可以用，不过bp和sp会被用来管 理栈顶和栈底，最好不要拿来进行运算
- 通用寄存器的名字在 IA64 和 plan9 中的对应关系
|X86_64|rax|rbx|rcx|rdx|rdi|rsi|rbp|rsp|r8|r9|r10|r11|r12|r13|r14|rip|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|Plan9|	AX|	BX|	CX|	DX|	DI|	SI|	BP|	SP|	R8|	R9|	R10|	R11|	R12	|R13|	R14|PC|

## 1.2 一个真实的GO plan9汇编的function定义
```
TEXT funtime·profileloop(SB), NOSPLIT, $8
  MOVQ $runtime·profileloop1(SB), CX
  MOVQ CX, 0(SP)
  CALL runtime·externalthreadhandler(SB)
  RET

TEXT ·add(SB),$0-24
  MOVQ x+0(FP), BX
  MOVQ y+8(FP), BP
  ADDQ BP, BX
  MOVQ BX, ret+16(FP)
  RET
```
说明：
1. 定义参数通常需要指定size，如$24-8, 如果NOSPLIT没有指定，参数的size是必须指定的
- $24 表示该行数的栈大小为24byte,计算栈大小时，需要考虑函数局部变量和本函数内调用其他函数时，需要传参空间，不含函数返回地址和CALLER BP
- $8 表示该函数入参和返回值一共有8byte，当有NOSPLIT标识时，可以不写输入参数、返回值占用的大小
2. Go汇编还可以声明调用.go声明的代码，但需要通过·来声明
3. Global指定后面跟着DATA指令可以定义全局标签，没有赋值的label全部被清零.
4. 上面的语句初始化symbol+offset(SB)的数据中width bytes，赋值为value
5. 相对于栈操作，SB的操作都是增地址，栈是减地址 

## 1.3 全局变量声明
- 全局变量名声明，以及数据所在的段"RODATA"，数据的长度64byte
```
GLOBL divtab<>(SB), RODATA, $64
```
## 1.4 局部变量声明
```
  局部变量存储在函数栈上，因此不需要额外进行声明，在函数栈上预留出空间，使用命令操作这些内存即可。
因此这些 局部变量没有标识，操作时，牢记局部变量的分布、内存偏移即可。
```