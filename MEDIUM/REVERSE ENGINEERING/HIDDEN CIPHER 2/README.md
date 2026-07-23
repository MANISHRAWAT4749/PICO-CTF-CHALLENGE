Hidden Cipher 2 Writeup

Challenge Information

•	Challenge Name: Hidden Cipher 2
•	Category: Reverse Engineering
•	Difficulty: Medium
•	Points: 100
________________________________________
Description
The challenge provides a ZIP file containing an executable (hiddencipher2) and a flag.txt file. The flag file contains a fake flag, suggesting that the real flag must be recovered by analyzing the program's behavior.
________________________________________
Files Provided
hiddencipher2
flag.txt
Contents of flag.txt:
picoCTF{fake_flag}
Since this is a Reverse Engineering challenge, the fake flag is a decoy and the executable must be analyzed.
________________________________________
Hints
Hint 1
Focus on what the program does with your correct answer. Is it reused later?
This hint suggests that the answer to the math problem is not only used for verification but also plays a role in the flag encoding process.
Hint 2
Disassembling or decompiling tools (Ghidra, IDA) can help reveal the exact transformation on the flag.
This hint indicates that the executable applies a mathematical transformation to the flag, which can be identified through reverse engineering or by observing the program's output.
________________________________________
Solution Steps
Step 1: Extract the ZIP File
unzip hiddencipher2.zip
This extracts:
hiddencipher2
flag.txt
________________________________________
Step 2: Connect to the Remote Service
nc crystal-peak.picoctf.net 56115
Example interaction:
What is 10 * 9?
90

Encoded flag values:
10080, 9450, 8910, 9990, 6030, 7560, ...
________________________________________
Step 3: Analyze the Output
The answer to the math question was:
90
Notice that:
10080 ÷ 90 = 112
ASCII 112 = p
Similarly:
9450 ÷ 90 = 105 = i
8910 ÷ 90 = 99 = c
9990 ÷ 90 = 111 = o
This reveals that every encoded value is:
encoded_value = ASCII_character × math_answer
Therefore:
ASCII_character = encoded_value ÷ math_answer
________________________________________
Step 4: Decode the Flag
Python script:
x = [10080, 9450, 8910, 9990, 6030, 7560, 6300, 11070,
     9810, 4680, 10440, 9360, 8550, 8820, 4590, 9360,
     4410, 9900, 9000, 8550, 8910, 4410, 10080, 9360,
     4590, 10260, 8550, 4410, 8820, 9090, 9090, 4860,
     8910, 9090, 9180, 11250]

key = 90

flag = ''.join(chr(n // key) for n in x)

print(flag)
Output:
picoCTF{m4th_b3h1nd_c1ph3r_1bee6cef}
________________________________________
Flag
picoCTF{m4th_b3h1nd_c1ph3r_1bee6cef}
________________________________________
Key Takeaway
The challenge disguises a simple encoding scheme behind a math problem. The correct answer is reused as a multiplication key for each ASCII character of the flag. By dividing each encoded value by the math answer and converting the resulting ASCII values back to characters, the original flag is recovered.
________________________________________


