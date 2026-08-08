picoCTF 2021 – ARMssembly 1

Category: Reverse Engineering

Difficulty: Medium

Points: 70
________________________________________

Challenge Description : 
We are given an ARM64 assembly file (chall_1.S). The objective is to determine the integer argument that makes the program print "You win!".
Hint: Shifts
________________________________________

Solution
Step 1: Analyze main()
The main function reads the command-line argument using atoi() and passes it to func().
bl atoi
bl func
cmp w0, 0
bne .L4
The program prints "You win!" only if the return value of func() is 0.
Therefore, our goal is to find an input such that:
func(input) = 0
________________________________________

Step 2: Identify the Constants
From the challenge variables:
a = 86
b = 3
c = 3
These values are used inside the function.
________________________________________

Step 3: Reverse the Function Logic
The assembly performs the following operations:
temp = a << b;
temp = temp / c;
return temp - input;
Substituting the given values:
temp = 86 << 3
Since a left shift by 3 is equivalent to multiplying by (2^3 = 8):
86 × 8 = 688
________________________________________

Step 4: Perform Integer Division
The function divides the result by 3 using integer division:
688 / 3 = 229
The remainder is discarded.
Now:
temp = 229
________________________________________

Step 5: Determine the Winning Input
The function returns:
229 - input
To print "You win!", the return value must be zero:
229 - input = 0
Therefore:
input = 229
________________________________________

Step 6: Convert to Hexadecimal
Convert 229 to hexadecimal:
229 = 0xE5
The flag format requires an 8-digit lowercase hexadecimal value:
000000e5
________________________________________

Flag : 
picoCTF{000000e5}

________________________________________

Key Takeaways

•	Understood the ARM64 calling convention and function flow.

•	Recognized the LSL (Logical Shift Left) instruction as multiplication by powers of two.

•	Interpreted SDIV as signed integer division.

•	Reconstructed the assembly into equivalent C-style logic.

•	Solved the equation to determine the correct input that causes the program to print "You win!".

________________________________________




