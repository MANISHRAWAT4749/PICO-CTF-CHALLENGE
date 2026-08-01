picoCTF Write-up: GDB Baby Step 3

Challenge Name: GDB Baby Step 3

Category: Reverse Engineering

Difficulty: Medium

Points: 100 pts
________________________________________
Challenge Description
A constant 0x2262c96b is loaded into memory in the main function. The goal is to examine how the value is stored in memory using GDB and determine the four bytes in memory order. Since the system uses little-endian architecture, the bytes are stored in reverse order.
________________________________________
Steps to Solve
1. Download the challenge binary
cd /tmp
wget https://artifacts.picoctf.net/c/531/debugger0_c
________________________________________
2. Give execute permission
Initially, the binary could not be executed and produced a Permission denied error.
chmod +x debugger0_c
________________________________________
3. Start GDB
gdb debugger0_c
________________________________________
4. Disassemble the main function
disas main
Output:
0x0000000000401106 <+0>:     endbr64
0x000000000040110a <+4>:     push   %rbp
0x000000000040110b <+5>:     mov    %rsp,%rbp
0x000000000040110e <+8>:     mov    %edi,-0x14(%rbp)
0x0000000000401111 <+11>:    mov    %rsi,-0x20(%rbp)
0x0000000000401115 <+15>:    movl   $0x2262c96b,-0x4(%rbp)
0x000000000040111c <+22>:    mov    -0x4(%rbp),%eax
0x000000000040111f <+25>:    pop    %rbp
0x0000000000401120 <+26>:    ret
The constant is written to memory at:
movl $0x2262c96b,-0x4(%rbp)
________________________________________
5. Set a breakpoint after the value is written
The next instruction is at address 0x40111c.
b *0x40111c
________________________________________
6. Run the program
run
Execution stops at the breakpoint.
________________________________________
7. Examine the memory
Display the four bytes stored at -0x4(%rbp):
x/4xb $rbp-4
Output:
0x7fff501dfdac: 0x6b    0xc9    0x62    0x22
________________________________________
8. Understand Little-Endian Storage
The original value loaded into memory was:
0x2262c96b
On x86-64 (little-endian), it is stored byte-by-byte as:
Memory Order	Byte
1	0x6b
2	0xc9
3	0x62
4	0x22
The challenge asks for the bytes exactly as they appear in memory.
________________________________________
Flag
picoCTF{0x6bc96222}
________________________________________
Commands Used
cd /tmp
wget https://artifacts.picoctf.net/c/531/debugger0_c
chmod +x debugger0_c
gdb debugger0_c
disas main
b *0x40111c
run
x/4xb $rbp-4
________________________________________
Key Learning
•	Use disas main to inspect assembly instructions.
•	Set a breakpoint after the memory write instruction.
•	Use x/4xb to examine four bytes in memory.
•	x86-64 systems use little-endian, meaning the least significant byte is stored at the lowest memory address.
•	The flag is constructed from the bytes in the order they are stored in memory, not the original hexadecimal constant.
________________________________________



