## 指令格式
![[20-RV32I-instruction-format.png]]
- 指令格式将 32 位分为不同的“域”
- RISC-V 定义了六种指令的格式：
	- R-格式：寄存器指令，指定指令中的 3 个寄存器 
	- I-格式：指令中包含立即数，用于带一个常数的算术指令以及加载指令
	- S-格式：store 指令
	- B-格式：分支指令
	- U-格式：大立即数，两个指令 lui (load upper imm), auipc (add upper imm to PC)
	- J-格式：唯一指令 jal

### R 型指令
![[22-Datapath-R-type.png]]
- opcode (7): 操作码，但未指明确切操作
	- R 类型指令操作码都为 0110011
- funct3 (3)， funct7 (7): 与操作码 opcode 一起指定了指令的具体功能
- 计算一下能够编码多少 R 类型指令功能？ - opcode 是固定的, 依据 funct: 2^10 = 1024
- rs1 (5): 第一个操作数 ，(“source register”) 
- rs2 (5): 第二个操作数 ，(“ second source register”) 
- rd (5): 目标寄存器 ，(“ destination register”) 
- 每一个寄存器5位进行编码，可以指定32个寄存器，编码的是寄存器编号

![[22-Datapath-R-type-field.png]]
- `add x8,x9,x10` 则翻译成指令为：
- binary: 0000000 01010 01001 000 01000 0110011
- hex: 0x00a48433

### I 型指令
对于含有立即数的指令来说，5位的域能够表达的范围太小
- 理想来说，RISC-V 指令最好只有一种格式。
- 但是实际情况下需要折衷——在实际中可以依据类似于 R 指令格式的方式定义新的指令格式
- 如果一条指令使用了立即数，这条指令最多使用两个寄存器

![[22-Datapath-I-type.png]]
- opcode (7): 指定操作类型
- rs1 (5): 指定一个寄存器操作数
- rd (5): 指定目标寄存器 (“destination register”)
- immediate (12): 12 位立即数
	- 所有的计算是用字进行计算，即 32 位，必须要对立即数进行扩展
	- 采用符号扩展方式
- 12 位表示范围 `[-2048, +2047]`
	- 如果立即数超过 12 位，怎么办？采用 U 型指令——大立即数，20 位

![[22-Datapath-I-type-field.png]]
- `addi x15,x1,-50` 则翻译成指令为：
- binary: 111111001110 00001 000 01111 0010011
- hex:  0xfce08793

#### load 指令

没错，访存用的 load 指令也是 I 型指令：
- ![[22-Datapath-load.png]]
- rd (5): 结果放置的寄存器 
- rs1 (5): 基地址寄存器 
- immediate (12) 
- 基地址寄存器的值 + 立即数 -> load 内存地址

![[22-Datapath-load-field.png]]
- `lw x14,8(x2)` 可翻译为：
- binary：000000001000 00010 010 01110 0000011
- hex:  0x00812703

### S 型指令

Store 指令：
![[22-Datapath-S-type-instruction.png]]
- rs1 (5): 基地址寄存器
- rs2 (5): 数据寄存器
- immediate (11:5) + immediate (4:0) ：地址偏移

>[! question] 为什么不把 rs2 用作 imm 的低位？
>RISC-V 设计让 rs1 和 rs2 的位置保持固定。
>
>对于所有的操作来说，关键路径包括获得寄存器的值
>- 把读的寄存器都放到相同的位置，那么每次都可以立即去读寄存器文件，不需要做判断
>	- 如果读出来的数据其实是不必要的（例如 I 类型），后续丢弃即可
>- 其它的 RISC 体系结构的设计有些许的不同
>	- 在译码阶段需要通过指令来判断需要读哪些寄存器
>- 寄存器位置保持不变是 RISC-V 的体系结构上一系列微小的调整之一，可以让整个处理器工作流程更好

![[22-Datapath-S-type-area.png]]
- `sw x14,8(x2)`
- binary: 0000000 01110 00010 010 01000 0100011
- hex:  0x00e12423

### B 型指令
分支指令：
![[22-Datapath-B-type.png]]
- beq and bne: 需要指定目标地址，也需要两个寄存器间比较
- opcode 指定是 beq (4) 还是 bne (5)
- rs1 ， rs2 用来指定两个寄存器
- 与 S 类型指令非常相似——两个寄存器（rs1, rs2），一个立即数（12 位） 
- Imm 表示了 `[-4096, +4094]` 范围的 **2 字节对齐**的偏移 
- Immediate 用 12 比特 表示了 13 比特的范围
	- 因为 2 字节对齐，所以最低位总是为 0，因而省略了
	- B 类型中 `imm[12:1]` 与 S 类型中 `imm[11:0]` 不同

>[!note] 使用 immediate 指定地址? 
>PC 相对跳转，循环的跳转一般在一个比较小的范围。
>- PC-Relative Addressing: 使用立即数作为补码，用作在 PC 上的偏移 
>- 类似于 lw/sw 的基址偏移寻址，源寄存器 rs1 为 PC
>- 这样可以指定从 PC 开始的 ± 2^11 的地址范围
>
>实际地址范围的计算：
>- 实际上 RISC-V 使用的是固定长度的指令字长，**内存是按字节地址寻址**的
>- **指令是按照字对齐**的，指令的地址总是 2 的倍数（最后一位为 0）（与 Compact 的指令格式兼容，需要最后一位为 0 即可，但是在 RV32I 中实际是 4 字节对齐的）
>- PC 总是指向一个指令地址，就可以做类型指针的操作
>- 在指令地址范围计算的时候，可以指定 ± 2^12 范围的地址

分支的计算：
- 不需要分支：PC = PC + 4
- 需要进行分支：PC = PC + immediate 
	- immediate 是跳转字节数 (注意 `imm[12:1]` 需要加上最末尾隐含的 0 位) 
	- 为向前（+），或者向后（-）的偏移地址（范围为 $\pm 4KB$）

分支举例：
![[22-Datapath-B-type-instance.png]]
![[22-Datapath-B-type-field.png]]

> [! note] PC 相对寻址的特性
> 
> PC 相对寻址的情况下，如果整块代码进行移动，则代码中的相对寻址值无需更改，如果只移动其中的几行代码，则相对寻址值可能需要更改
> 
> 如果需要移动的范围超过表达的指令范围 > 2^10
> - 要么选用其它指令
> - 要么分步：
> ```
> beq x10, x0, far
> # next instr
> 
> # 可转换为：
> 
> bne x10, x0, next
> j far
> next: # next instr

### U 型指令
处理大立即数——imm 长度有 20 位：
![[22-Datapath-U-type.png]]
- lui: Load Upper Immediate 
	- lui reg, imm : 将 20 位的立即数装入到寄存器 reg 的高 20位
	- 将低 12 位清零
- auipc: Add Upper Immediate to PC

使用 lui 指令的例子：
- lui 设置了高 20 位，通过 addi 设置低12位
- 例 1：设置 0x87654321 
	- `lui x10,0x87654 # x10=0x87654000`
	- `addi x10,x10,0x321 # x10=0x87654321`
- 例 2：设置 0xDEADBEEF
	- `lui x10,0xDEADB # x10=0xDEADB000`
	- `addi x10,x10,0xEEF # x10=0xDEADAEEF`
	- 解决办法：lui 高 20 位 加 1 
	- 伪指令 li x10,0xDEADBEEF: 自动生成两条指令

![[22-Datapath-sign-extension.png]]

### J 型指令
跳转指令：
![[22-Datapath-J-type.png]]
- Rd： 返回地址（PC+4）
- Imm: 偏移 offset, 设置跳转 PC = PC + offset
- 跳转范围：`[-2^19 ，+2^19]` 个位置，2 字节为单位
- 实际跳转范围 `[-2^18 ，+2^18]` 个 32-bit 指令
- J 伪指令
	- `j Label` = `jal x0, Label` # Discard return address n  
	- `jal ra, FuncNam` # Call function within 2^18 instructions of PC

#### Jalr 指令
属于 I 类型而不是 J 类型
![[22-Datapath-Jalr.png]]
- `Jalr rd, rs, immediate` 
- Rd： 返回地址（PC+4）
- Imm: 偏移 offset, 设置跳转 PC = rs1 + offset
- Immediate 与 I 类型指令中的算术和加载指令一样
	- 注意，不需要 ✖2

```
# Jalr用法例子

# ret and jr psuedo-instructions
ret == jr ra == jalr x0, 0(ra) 

# Call function at any 32-bit absolute address
lui x1,<hi20bits>
jalr ra, x1, <lo12bits> 

# Jump PC-relative with 32-bit offset
auipc x1,<hi20bits> # Adds upper immediate value to 
                    # and places result in x1 
                    
jalr x0, x1,<lo12bits> # Same sign extension trick needed 
                       # as LUI
```

## 单周期数据通路
![[22-Datapath-singlecycle-datapath.png]]
### 支持 R 型指令的数据通路设计

- R 型指令有：add, sub 等
- 执行过程：
	- 取指令->
	- 分析指令->
	- 执行指令: 取操作数, 运算, 结果写回->
	- 计算下一条指令的地址->回到取指
- 指令的内部域含义：
	- ![[22-Datapath-R-type.png]]

由此，设计能够实现 R 型指令的数据通路如下：
![[22-Datapath-datapath-1.png]]
