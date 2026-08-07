picoCTF 2021 – ARMssembly 0
Category: Reverse Engineering
Difficulty: Medium
Points: 40
________________________________________
Challenge Description
Analyze the provided ARM64 assembly program to determine the integer printed by the program. Convert the printed integer to an 8-digit lowercase hexadecimal value and submit it in the format:
picoCTF{xxxxxxxx}
For this challenge, the provided arguments were:
a = 2593949075
b = 2233560849
________________________________________
Solution
Step 1 – Examine the Assembly
The main function converts the two command-line arguments from strings to integers using atoi():
bl atoi
mov w19, w0

...

bl atoi
mov w1, w0
mov w0, w19
bl func1
The converted integers are then passed to func1().
________________________________________
Step 2 – Analyze func1()
The important instructions are:
ldr w1, [sp, 12]
ldr w0, [sp, 8]
cmp w1, w0
bls .L2
ldr w0, [sp, 12]
b .L3
.L2:
ldr w0, [sp, 8]
Understanding the Logic
•	cmp w1, w0 compares a and b.
•	bls (Branch if Lower or Same) branches when a <= b (unsigned comparison).
•	If a > b, the function returns a.
•	Otherwise, it returns b.
Equivalent C code:
unsigned func1(unsigned a, unsigned b) {
    if (a > b)
        return a;
    else
        return b;
}
Thus, the function simply returns the larger of the two integers.
________________________________________
Step 3 – Compare the Given Values
a = 2593949075
b = 2233560849
Since:
2593949075 > 2233560849
the program prints:
2593949075
________________________________________
Step 4 – Convert to Hexadecimal
Convert the decimal result into an 8-digit lowercase hexadecimal value:
python3 -c "print('{:08x}'.format(2593949075))"
Output:
9a9c8593
________________________________________
Flag
picoCTF{9a9c8593}
________________________________________
Key Takeaways
•	Learned how ARM64 passes function arguments using registers (w0, w1).
•	Understood the use of the cmp instruction for comparisons.
•	Learned that bls performs an unsigned "lower or same" comparison.
•	Converted the final decimal output into the hexadecimal format required by picoCTF.
________________________________________

