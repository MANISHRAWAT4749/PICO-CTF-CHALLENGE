picoCTF 2019 – VaultDoor1

Category: Reverse Engineering

Difficulty: Medium

Points: 100

________________________________________

Challenge Description : 
The vault checks the password one character at a time instead of storing it directly. Our objective is to reconstruct the correct password by analyzing the Java source code.

________________________________________
Files Provided

•	VaultDoor1.java
________________________________________
Step 1: Download the Challenge File

cd /tmp

wget https://challenge-files.picoctf.net/c_fickle_tempest/eb2eaca69cb975c96a289b4db182ed439cf7f6bc542b135b8a9a1e9834c068c1/VaultDoor1.java
________________________________________

Step 2: View the Source Code
cat VaultDoor1.java
The important function is:
public boolean checkPassword(String password) {
    return password.length() == 32 &&
           password.charAt(0)  == 'd' &&
           password.charAt(29) == '8' &&
           password.charAt(4)  == 'r' &&
           ...
           password.charAt(31) == '8';
}
The program verifies each character of the password individually using password.charAt(index).
________________________________________

Step 3: Reconstruct the Password
Each statement tells us which character belongs at a specific index.
Index	Character
0	d
1	3
2	5
3	c
4	r
5	4
6	m
7	b
8	l
9	3
10	_
11	t
12	H
13	3
14	_
15	c
16	H
17	4
18	r
19	4
20	c
21	T
22	3
23	r
24	5
25	_
26	2
27	9
28	e
29	8
30	d
31	8
Combining all the characters gives:
d35cr4mbl3_tH3_cH4r4cT3r5_29e8d8
________________________________________

Step 4: Form the Flag
The program strips the picoCTF{} wrapper before checking the password, so the reconstructed password must be placed inside the flag format.
picoCTF{d35cr4mbl3_tH3_cH4r4cT3r5_29e8d8}
________________________________________

Verification (Optional)
Run the Java program:
javac VaultDoor1.java
java VaultDoor1
Enter:
picoCTF{d35cr4mbl3_tH3_cH4r4cT3r5_29e8d8}
Expected output:
Access granted.
________________________________________

Flag : 
picoCTF{d35cr4mbl3_tH3_cH4r4cT3r5_29e8d8}

________________________________________

Key Takeaways

•	Read source code carefully before attempting dynamic analysis.

•	charAt(index) accesses a character at a specific position in a string.

•	Reconstructing a password from indexed character checks is a common reverse engineering technique.

•	Sometimes the simplest solution is manually rebuilding the expected input from the source code rather than reversing the compiled binary.
________________________________________




