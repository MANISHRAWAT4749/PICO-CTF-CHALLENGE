picoCTF Write-up: GDB baby step 2

Challenge Information : 
Field	Value

Challenge Name	GDB baby step 2

Category	Reverse Engineering

Difficulty	Medium

Points	100
________________________________________

Objective
Determine the value stored in the EAX register at the end of the main() function and submit it in the format : picoCTF{value}

________________________________________
Step 1: Download the Binary

cd /tmp

wget https://artifacts.picoctf.net/c/520/debugger0_b

chmod +x debugger0_b
________________________________________

Step 2: Open the Binary in GDB
gdb debugger0_b
________________________________________

Step 3: Disassemble the main() Function

Inside GDB:
disas main

Output:
0x401115 <+15>: movl   $0x1e0da,-0x4(%rbp)
0x40111c <+22>: movl   $0x25f,-0xc(%rbp)
0x40113e <+56>: mov    -0x4(%rbp),%eax
0x401141 <+59>: pop    %rbp
0x401142 <+60>: ret
The final value is moved into the EAX register just before the function returns. 

________________________________________

Step 4: Set a Breakpoint
Set a breakpoint immediately after the value is loaded into eax.
b *0x401141
Output:
Breakpoint 1 at 0x401141
________________________________________

Step 5: Run the Program
run
Output:
Breakpoint 1, 0x0000000000401141 in main ()
Execution stops just before returning from main().

________________________________________

Step 6: Inspect the EAX Register
Display the value stored in eax:
info registers eax
or
i r eax
Output:
eax            0x4af4b             307019
Convert or print it in decimal if necessary:
p/d 0x4af4b
Output:
$1 = 307019
________________________________________

Flag
picoCTF{307019}
________________________________________

Commands Used
cd /tmp

wget https://artifacts.picoctf.net/c/520/debugger0_b

chmod +x debugger0_b 

gdb debugger0_b 

disas main

b *0x401141
run

info registers eax

p/d 0x4af4b
________________________________________

Key Learning
•	Use disas main to inspect the program's assembly.
•	Set a breakpoint just before the ret instruction to inspect register values at the end of execution.
•	Use info registers eax (or i r eax) to read the contents of the eax register.
•	The challenge can also be solved by analyzing the loop mathematically, but GDB provides the value directly.
Final Flag:
picoCTF{307019}

________________________________________
