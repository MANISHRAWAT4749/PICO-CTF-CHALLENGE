picoCTF Challenge Write-Up: GDB Baby Step 4

Challenge Information
•	Challenge Name: GDB Baby Step 4
•	Category: Reverse Engineering
•	Difficulty: Medium
•	Points: 100
•	Flag: picoCTF{12905}
________________________________________
Challenge Description
Main calls a function that multiplies eax by a constant. The flag for this challenge is that constant in decimal.
Example: If the constant is 0x1000, the flag would be picoCTF{4096}.
________________________________________
Objective
Use GDB to determine the constant used in the multiplication instruction inside the function called by main.
________________________________________
Step 1: Download the Binary
cd /tmp
wget https://artifacts.picoctf.net/c/532/debugger0_d
________________________________________
Step 2: Make the Binary Executable
chmod +x debugger0_d
________________________________________
Step 3: Open the Binary in GDB
gdb debugger0_d
Output:
Reading symbols from debugger0_d...
(No debugging symbols found in debugger0_d)
This tells us the binary is stripped of debugging symbols, but GDB can still disassemble its functions.
________________________________________
Step 4: Disassemble main
Inside GDB:
disassemble main
Output:
Dump of assembler code for function main:
...
0x40113d <+33>: mov    -0x4(%rbp),%eax
0x401140 <+36>: mov    %eax,%edi
0x401142 <+38>: call   0x401106 <func1>
0x401147 <+43>: mov    %eax,-0x8(%rbp)
...
Analysis
The important instruction is:
call 0x401106 <func1>
This shows that main calls a function named func1, which is likely where the multiplication occurs.
________________________________________
Step 5: Disassemble func1
Inside GDB:
disassemble func1
Output:
Dump of assembler code for function func1:
0x401106 <+0>:  endbr64
0x40110a <+4>:  push   %rbp
0x40110b <+5>:  mov    %rsp,%rbp
0x40110e <+8>:  mov    %edi,-0x4(%rbp)
0x401111 <+11>: mov    -0x4(%rbp),%eax
0x401114 <+14>: imul   $0x3269,%eax,%eax
0x40111a <+20>: pop    %rbp
0x40111b <+21>: ret
________________________________________
Step 6: Identify the Constant
The key instruction is:
imul   $0x3269,%eax,%eax
This instruction means:
eax = eax * 0x3269;
Therefore, the multiplication constant is:
0x3269
________________________________________
Step 7: Convert the Constant to Decimal
Convert hexadecimal 0x3269 to decimal.
0x3269 = 12905
(You can verify this using GDB with p/d 0x3269, Python, or an online hexadecimal converter.)
________________________________________
Step 8: Submit the Flag
Since the challenge asks for the constant in decimal:
picoCTF{12905}
________________________________________
Key Takeaways
•	Use disassemble main to inspect the program's entry function.
•	Identify any functions called by main.
•	Disassemble the called function to inspect its instructions.
•	The imul instruction reveals the multiplication constant.
•	Convert the hexadecimal constant to decimal to obtain the flag.
________________________________________
Commands Used
chmod +x debugger0_d
gdb debugger0_d
disassemble main
disassemble func1
________________________________________

Final Flag
picoCTF{12905}
________________________________________

