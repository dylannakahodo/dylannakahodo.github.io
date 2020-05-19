---
layout: post
title: "Notes from Intro to x86 (OpenSecurityTraining)"
permalink: /posts/intro-x86-notes
category: Notes
tags: [reverse-engineering, assembly, course-work, self-study]
---

Rough notes from Introductory Intel x86 on OpenSecurityTraining by Xeno Kovah.

### Data Types 

Intel / C Naming
- 8 bits: Byte / char
- 16 bits: Word / short
- 32 bits: Doubleword / int or long
- 64 bits: Quadword / double or long long
- 128 bits: Double Quadword / ?


### Architecture - Endian

- Little Endian 
	- 0x12345678 stored in 'little end' first. The least significant byte of a word or larger is stored in the lowest address.
	- 0x78563412
	- Intel is Little endian
- Big Endian
	- 0x1234578 is stored as is.
	- Network traffic is Big endian.
	- Most everything else is big endian, PowerPC, ARM, SPARC, MIPS
	- Or Bi-Endian

### Architecture - Registers

- Small memory storage areas built into the processor
- 8 "general purpose" registers + the instruction pointer
	- Two of the 8 are not that general
- x86-32, registers are 32 bits long
- x86-64, registers are 64 bits long


### Architecture - Registers Conventions 1

- EAX - Accumulator register. Stores function return values
- EBX - Base pointer to the data section
- ECX - Counter for string and loop operations
- EDX - I/O Pointer
- ESI - Source pointer for string operations
- EDI - Destination pointer for string operations
- ESP - Stack pointer
- EBP - Stack frame base pointer
- EIP - Pointer to the next instruction to execute ("instruction pointer")

### Architecture - Registers Convetions 2

- Caller-save registers - EAX, EDX, ECX
	- If the function caller has anything in these registers it needs, it is responsible for saving the value before calling a subroutine, then it needs to restore the value after the call returns.
- Callee-save registers - EBP, EBX, ESI, EDI
	- The caller can assume, by convention, that the callee will not destroy the values stored in these registers. Therefore, if the callee needs to use these registers, it is the callee's responsibility to save and restore these values.

### Architecture - EFLAGS

- EFLAGS register holds single bit flags (the following will be used in this course)
	- Zero Flag (ZF) - Set if the result of some instruction is zero; cleared otherwise
	- Sign Flag (SF) - Set equal to the most-significant bit of the result, which is the sign bit of a signed integer.

### The Stack

- Conceptual area of main memory (RAM) which is designated by the OS when a program is started.
	- Different OS's start it at different addresses
- Last-In-First-Out data structure. Data is "pushed" on to the top of the stack and "popped" off the top
- The stack grows toward lower memory addresses.
- ESP points to the top of the stack, the lowest address being used
	- Any data below ESP is considered undefined
- The Stack keeps track of which functions were called before the current one, it holds local variables, and is frequently used to pass arguments to the next function to be called

### PUSH - Push Word, Doubleword or Quadword onto the Stack

- Can either be an immediate (a numeric constant) or the value in a register
- The PUSH instruction automatically decrements the stack pointer, ESP, by 4


### POP - Pop a value from the Stack

- Take a value off the stack, put it in a register, and increment ESP by 4


### Calling Conventions - cdecl

- "C declaration" - most common calling convention
- Function parameters are pushed to the stack right to left
- Saves the old stack frame pointer and sets up a new stack frame
- EAX or EDX:EAX returns the result for primitive data types
- **Caller is responsible for cleaning up the stack**

### Calling Conventions - stdcall

- Typically Microsoft C++ code - e.g. Win32 API
- Function parameters pushed right to left
- Saves old stack frame pointer and sets up new stack frame
- EAX or EDX:EAX returns the result for primitive data types
- **Callee responsible for cleaning up any stack parameters it takes**

### CALL - Call Procedure

- Transfers control to a different function, in a way that the control can be resumed where it left off
- Steps
	1. Pushes the address of the next instruction onto the stack
		- For use by RET when the procedure is done
	2. Changes EIP to the address given in the instruction
- Destination address can be specified in multiple ways
	- Absolute address
	- Relative address (relative to the end of the instruction)

### RET - Return from Procedure

- Two Forms
	- Pop the top of the stack into EIP
		- Instruction is just written as 'ret'
		- Typically used by cdecl functions
	- Pop the top of the stack into EIP and add a constant number of bytes to ESP. (needs to clean up function parameters)
		- Instruction is written as "ret 0x8" or "ret 0x20", etc
		- Typically used by stdcall functions

### MOV - Move

- Can move:
	- register to register
	- memory to register, register to memory
	- immediate to register, immediate to memory
- Never memory to memory!
- Memory addresses are given in r/m32 form

### General Stack Frame Operation

- Assumes main has some registers it wants saved and that the callee wants to use all registers, so the callee-save and caller-save registers are pushed to the stack


## r/m32 Addressing Forms

- Anywhere you see an r/m32 it means it could be taking a value either from a register or a memory address
- In Intel syntax, most of the time square brackets [] means to treat the value within as a memory address, and fetch the value at that address. (Like dereferencing a pointer)
	- mov EAX, EBX
	- mov EAX, [EBX]
	- mov EAX, [EBX+ECX*X] (X=1,2,4,8)
	- mov EAX, [EBX+ECX*X+Y] (Y=one byte, 0-255 or 4 bytes, 0-2^32-1)
- Most complicated form: [base + index*scale + disp]
	- Base - starting point of array
	- index - index into the array
	- scale - how big each element is, 1 byte, 4 bytes, etc.
	- disp - possibly used in multidimensional arrays

## LEA - Load Effective Address

- Frequently used with pointer arithmetic
- Uses the r/m32 form but **is the exception to the rule** that the square brackets [] syntax means dereference ("value at")
- Example: EBX = 0x2, EDX = 0x1000
	- lea EAX, [EDX + EBX*2]
	- EAX = 0x1004, not the value at 0x1004

## ADD and SUB

- Adds or subtracts
- Destination can be r/m32 or register
- Source can be immediate, r/m32, or register
- No source **and** destination as r/m32s as that could allow for memory to memory transfer which is not permitted on x86
- Evalutes the operation as if it were on signed **and** unsigned data, and sets flags as appropriate.
	- Modify OF, SF, ZF, AF, PF, and CF flags

## Control Flow

- Two forms of control flow
	- Conditional - go somewhere if some condition is met, if specific flags are set
	- Unconditional - go somewhere no matter what. Procedure call, goto, exceptions, interrupts

## JMP - Jump

- Change EIP to the given address
- Different Forms
	- Short relative (1 Byte displacement from end of the instruction)
		- "JMP 00401023" doesn't actually have "00401023" stored in it, it could really just mean "jmp 0x0E bytes forward"
	- Near relative (4 byte displacement from current EIP)
	- Absolute (hardcoded address in instruction)
	- Absolute indirect (address calculated with r/m32)

## JCC - Jump if condition is met

- JZ/JE : if ZF == 1
- JNZ/JNE : if ZF == 0
- JLE/JNG : if ZF == 1 or SF != OF
- JGE/JNL : if SF == OF
- JBE : if CF == 1 or ZF == 1
- JB : if CF == 1

## CMP - Compare Two Operands

- Comparison is performed by subtracting the second operand from the first and setting flags, similar to the SUB instruction
- Difference with SUB, is that CMP doesn't store the result anywhere
- Modifies CF, OF, SF, ZF, AF, and PF

## TEST - Logical Compare

- Performs a bit-wise AND of the first and second operand, sets SF, ZF, and PF flags, then discards the results

## AND - Logical AND

- Destination operand can be r/m32 or register
- Source operand can be r/m32 or register or immediate
- No source AND destination as r/m32


## OR - Logical Inclusive OR

- Destination operand can be r/m32 or register
- Source operand can be r/m32 or register or immediate
- No source AND destination as r/m32


## XOR - Logical Exclusive OR

- Destination operand can be r/m32 or register
- Source operand can be r/m32 or register or immediate
- No source AND destination as r/m32


## NOT - One's complement negation

- Single source/destination operand can be r/m32

## SHL - Shift Logical Left

- Can be explicitly used with the C "<<" operator
- First operand (source and destination) operand is an r/m32
- Second operand is either cl (lowest byte of ECX), or a 1 byte immediate. 
	- The second operand is the number of places to shift.
- **Multiples** the register by **2** for each place the value is shifted.
- Bits that are shifted off the left hand side are "shifted into" (set) the carry flag (CF)


## SHR - Shift Logical Right

- Can be explicitly used with the C ">>" operator
- First operand (source and destination) operand is an r/m32
- Second operand is either cl (lowest byte of ECX), or a 1 byte immediate.
	- The second operand is the number of places to shift.
- **Divides** the register by **2** for each plcae the value is shifted.
- Bits that are shifted off the right hand side are "shifted into" (set) the carry flag (CF)


## IMUL - Signed Multiply

- Three Forms: One, Two or Three Operands
	- IMUL r/m32
		- **edx:eax = eax * r/m32**
	- IMUL reg, r/m32 
		- **reg = reg * r/m32**
	- IMUL reg, r/m32, immediate 
		- **reg = r/m32 * immediate**



## DIV - Unsigned Divide

- Two Forms
	- Unsigned divide ax by r/m8.
		- al = quotient, ah = remainder
	- Unsigned divide edx:eax by r/m32
		- eax = quotient, edx = remainder



## REP STOS - Repeat Store String

- All rep operations use ecx register as a counter to determine how many times to loop through the instruction. Each time it executes, it decrements ecx until ecx == 0
- Either moves one byte at a time or one dword at a time
- Moves the EDI register forward one byte or one dword at a time, so that the repeated store operation is storing into consecutive locations
- Three things that must happen before REP STOS
	- Set EDI to the start destination
	- set EAX/AL to the value to store
	- Set ECX to the number of times to store

## REP MOVS - Repeat Move Data String to String

- Either move byte at [ESI] to byte at [EDI] or move dword at [ESI] to dword at [EDI]
- Moves the esi and edi registers forward one byte or one dword at a
time, so that the repeated store operation is storing into
consecutive locations.
- Three things to happen before REP MOVS
	- Set ESI to start source 
	- Set EDI to start destination
	- Set ECX to the number of times to move

## LEAVE - High level procedure exit

- Set ESP to EBP, then POP EBP


## Intel vs AT&T Syntax

- Intel: Destination <- Source(s)
	- Windows. Think algebra or C: y = 2x + 1
	- mov ebp, esp
	- add esp, 0x14; (esp = esp + 0x14)
- AT&T: Source(s) -> Destination
	- *nix/GNU. Think elementary school: 1 + 2 = 3
	- mov %esp, %ebp
	- add $0x14, %esp
	- Registers get a % prefix and immediates get a $

## Intel vs AT&T Syntax 2

- r/m32 format differences
- Intel
	- [base + index * scale + disp]
- AT&T
	- disp(base, index, scale)
- **Examples**
	- call DWORD PTR[ebx + esi*4 - 0xe8]
	- call *-0xe8(%ebx, %esi, 4)
	- mov  eax, DWORD PTR[ebx + 0x8]
	- mov 0x8(%ebx), %eax
	- lea  eax, [ebx-0xe8]
	- lea  -0xe8(%ebx, %eax)

## Intel vs AT&T Syntax 3

- Instructions that operate on different sizes, the mnemonic will have an indicator of the size.
	- movb - operates on bytes
	- mov/movw - operates on word (2 bytes)
	- movl - operates on "long" (dword) (4 bytes)
- Intel also indicates sizes with things like "mov dword ptr [eax]", just not in the actual mnemonic of the instruction

## gcc usage

- `gcc -ggdb -o <output filename> <input filename>`
	- ggdb builds debug symbols

## objdump - display information from object files

- Where "object file" can be an intermediate file created during compilation but before linking, or a fully linked executable
	- `objdump -d helloworld`
	- `objdump -d -M intel helloworld`
		- For intel syntax

##	hexdump & xxd

- hexdump or hd - ASCII, decimal, hex, octal dump
	- `hexdump -C` for 'canonical' hex and ASCII view
- xxd - make a hexdump or do the reverse
	- Quick and dirty hex editor
	- xxd hello > hello.dump
	- edit hello.dump
	- xxd -r hello.dump > hello


## GDB Commands
### Display
- `help display`
- `display` prints out a statement every time the debugger stops
- `display/FMT EXP`
	- FMT can be a combination of the following:
		- i - display as an ASM instruction
		- x or d - display as hex or decimal
		- b or h or w - display as byte, halfword(2 bytes), word (4 bytes)
		- s - character string (keeps reading until hits a null character)
		- <number> - display <number> worth of things (instructions, bytes, words, strings)
- `info display` - to see all outstanding display statements and their numbers
- `undisplay <num>` - remove a display statement by number

### Breakpoint Commands
- `help breakpoints` - show all breakpoint-related commands
- `break` or `b` - sets a breakpoint
	- With debugging symbols enabled, you can do `b main`. Without, you will need to do `b *<address>` to break at a given memory address.
- `info breakpoints` or `info b` - show currently set breakpoints
- `delete <num>` - deletes breakpoint number <num>, where <num> came from `info breakpoints`

### GDB 7 Commands 
- `reverse-step` or `rs` - Step program backward until it reaches the beginning of the previous source 
- `reverse-stepi` - Step backward exactly one instruction
- `reverse-continue` or `rc` - Continue program being debuged but run it in reverse
- `reverse-finish` - Execute backward until just before the selected stack frame is called
- `reverse-next` or `rn` - Step program backward, proceeding through subroutine calls
- `reverse-nexti` or `rni` - Step backward one instruction, but proceed through called subroutines
- `set exec-direction` (forward/reverse) - Set direction of execution. All subsequent execution commands (continue, step, until etc.) will run the program being debugged in the selected direction

### Stepping
- `stepi` or `si` - Steps one ASM instruction at a time
	- Will always "step into" subroutines
- `step` or `s` - Steps one source line at a time (if no source is available, works like stepi)
- `until` or `u` - Steps until the next source line, not stepping into subroutines

### GDB Misc Commands
- `set disassembly-flavor intel` - use intel syntax
- `continue` or `c` - run until you hit another breakpoint or the program ends
- `backtrace` or `bt` - print a trace of the call stack, showing all the functions which were called before the current function

## SAR - Shift Arithmetic Right

- Can be explicitly used with the C ">>" operator, if the operands are signed
- First operand (source and destination) is an r/m32
- Second operand is either cl (lowest byte of ECX), or a 1 byte immediate.
	- The 2nd operand is the number of bits to shift.
- It divides the register by 2 for each plcae the value is shifted. More efficient than a multiply instruction.
- Each bit shifted off the right side is plcaed in CF.

