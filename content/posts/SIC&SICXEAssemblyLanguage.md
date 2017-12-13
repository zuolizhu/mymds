---
title: "SIC&SIC/XE Assembly Language"
date: 2017-12-12T19:04:45-05:00
draft: true
---

A java programmer won't like this.

But sometimes people want to find the truth behind the scene.

---

#### SIC and SIC/XE Assembly Languages

**SIC (Simplified Instructional Computer)**

- Two version:
  - standard model (SIC)
  - XE model (SIC/XE)
    - SIC program can run on a SIC/XE machine





**I/O in SIC**

- Performed by transferring 1 byte at a time to/from the rightmost 8 bits of a register

- Each I/O device is assigned a unique 8-bit id

- I/O instructions

  - TD (test device)

    - Checks if devise is ready to send/receive data

    - Sets CC to indicate the result

      - < means ready
      - = indicates not ready

    - RD (read data)

    - WD (write data)

      ​

---

#### Comparision btween SIC and SIC/XE

- ##### SIC Architecture

  - Allows only integers and characters

    - Characters: 1 byte (ASCII)
    - Integers use 3 bytes (2’s complement for negative values)
    - No floating point hardware on the standard version

    ​

  - Memory

    - Consists of 8-bit bytes
    - Memory Size = 2^15 = 32,768 bytes
    - 3 consecutive bytes for a word (24 bits)
    - All addresses are byte addresses
    - Words are addressed by the location of their lowest numbered byte

    ​

  - Five registers (each is 24 bits = 1 word in length)

    - A (Accumulator; used for arithmetic operations)
    - X (Index register; used for addressing)
    - L (Linkage register; used by the Jump to subroutine instruction to store the return address)
    - PC (Program counter; used to store the address of the next instruction to be fetched for execution)
    - SW (status word)

    ​

  - Instruction Format:

    - Direct Mode: Target Address = Address
    - Indirect Mode: Target Address = Address + contents of register X
      - (x bit indicates index-addressing mode)

  ​

  - SIC Instruction Set
    - Basic Set of Instructions
      - Load/Store Instructions (LDA, LDX, STA, STX,…)
      - Arithmetic Instructions (ADD, SUB, MUL, DIV)
      - Comparison Instruction (COMP)
        - Compares the contents of register A with the contents of the specified memory location
        - Sets the value of CC (Condition Code) stored in register SW to indicate >, = or <
      - Conditional Jumps (JLT, JEQ, JGT) and subroutine jumps (JSUB, RSUB)
        - Test for CC and jump accordingly

##### 





- ##### SIC/XE Architecture

  - Allows only integers and characters

    - Characters: 1 byte (ASCII)
    - Integers use 3 bytes (2’s complement for negative values)
    - **48-bit floating point data type**

    ​

  - Memory

    - Consists of 8-bit bytes
    - **Memory Size = 2^20= 1 megabyte**
      - (Cause to change in instruction formats and addressing modes)
      - 3 consecutive bytes for a word (24 bits)
      - All addresses are byte addresses
      - Words are addressed by the location of their lowest numbered byte

    ​

  - Five registers of SIC + **four additional registers**

    - B (Base register; used for addressing)
    - S and T (general registers)
    - F (floating point accumulator)

    ​

  - **4 different Instruction Formats**

    - SIC/XE instructions may be 1, 2, 3 or 4 bytes long

  ​

  - Instruction Set
    - **Includes all SIC Instructions**
    - New Load/Store instructions to account for the new registers (e.g., LDB/STB)
    - Floating-point arithmetic operations (e.g., ADDF SUBF)
    - Register-to-register arithmetic operations (e.g., ADDR, SUBR)
    - Supervisor call instruction (SC)
      - Generates an interrupt for communication with the OS

---

SIC Programming Example

- Data movement

  - No memory-to-memory movement

  - All data movement must be performed through registers

    ```assembly
    LDA		FIVE
    STA		ALHPA
    LACH	CHARZ
    STCH 	C1
    ```

    ​	Above can be accomplished using LDX, STX and register X



- Define storage for data items

  ```assembly
  ALPHA	RESW 1
  FIVE	WORD 5
  CHARZ	BYTE C'Z'
  C1		RESB
  ```

  Reserves 1 or more words to be used by the program

  Reserves 1 word and initializes to the value indicated by the operand

  Similar to WORD and RESW

  but for chars



- Arithmetic Operations

  ```assembly
  LDA		ALPHA
  ADD		INCR
  SUB		ONE
  STA		BETA
  LDA		GAMMA
  ADD 	INCR
  SUB 	ONE
  STA 	DELTA
  .
  .
  .
  ONE		WORD	1
  ALPHA	RESW	1
  BETA	RESW	1
  GAMMA	RESW	1
  DELTA	RESW	1
  INCR	RESW	1
  ```

  ​

---

SIC vs SIC/XE

```assembly
#SIC Version:
LDA 	FIVE
STA 	ALPHA
LDCH 	CHARZ
STCH 	C1
.
.
ALPHA 	RESW 	1 		# Uninitialized word.
FIVE 	WORD 	5 		# Word initialized to 5.
CHARZ 	BYTE 	C’Z’ 	# Byte initialized to ’Z’.
C1 		RESB 	1 		# Uninitialized byte.
```

```assembly
#SIC/XE Version:
		LDA #5 			# Immediate operand.
		STA ALPHA
		LDA #90 		# ASCII code for ’Z’.
		STCH C1
.
.
ALPHA 	RESW 1 			# Uninitialized word.
C1 		RESB 1 			# Uninitialized byte.
```

