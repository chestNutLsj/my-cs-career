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