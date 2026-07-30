picoCTF Challenge Write-Up: AutoRev 1

Challenge Overview

•	Challenge Name: AutoRev 1

•	Category: Reverse Engineering

•	Difficulty/Level: Medium / Advanced (Requires Scripting Automation)

•	Objective: Extract a hidden 32-bit integer constant ("the secret") embedded within a dynamic binary executable, submit it within a strict 1-second time window, and repeat this process across 20 consecutive automated rounds.
________________________________________
Methodology & Analysis
Step 1: The Initial Core Obstacle
When connecting to the challenge via Netcat (nc), the server dynamically streams an entire compiled Linux ELF binary encoded as a massive chunk of hexadecimal ASCII text. It then prompts: What's the secret?:
You are allotted precisely 1 second to analyze the binary, extract the correct key, and return the answer. Because the binary changes entirely every round for 20 rounds, manual extraction via standard CLI interactive tools is impossible over the network.
Step 2: Manual Reconstruction and Local Verification (./binary)
To understand how the binary worked, the raw hex stream from Round 1 was intercepted, copied, and validated locally using the following manual steps:
1.	Reconstructing the Hex Block with xxd:
The plain text hex dump was pasted into a file called binary.raw and converted back into an actual Linux executable using:
Bash
xxd -r -p binary.raw binary
o	-r (Reverse): Converts hexadecimal ASCII strings back into standard raw binary bytes instead of printing a hex dump out of a binary.
o	-p (Plain): Tells xxd to read a plain continuous stream of hex characters without standard column offsets or headers.
2.	Making the File Executable and Running It:
Before the generated binary could be run on the Linux target shell, execution rights were assigned:
Bash
chmod +x binary
./binary
3.	The Result:
Running ./binary locally prompt with What's the secret?. Supplying the key parsed from the session successfully printed Correct!. This confirmed that the key was statically compiled straight into each individual binary.
Step 3: Finding the Machine Code Signature Pattern
By inspecting the disassembly of the validated local binary, we can locate the exact x86 assembly sequence where the target number is loaded into memory right before the comparison check:
Code snippet
mov DWORD PTR [rbp-0x4], <32-bit_secret_integer>
In raw machine code opcodes, this specific mov instruction maps directly to a unique static 3-byte signature:
\xc7\x45\xfc
The 4 bytes immediately following this operational signature hold the secret value formatted in a standard raw little-endian integer structure.
Automated Solution Script (solve.py)
Because you cannot repeat the manual steps inside the 1-second network limit for the remaining 19 rounds, this script acts as an automated connection client. It captures the raw stream, acts as an automated xxd to convert the hex layout to bytes, scans for the \xc7\x45\xfc pattern, unpacks the hidden integer, and replies instantly.
Python
from pwn import *
import struct

# Set to 'debug' to watch the hex streams fly by
context.log_level = 'debug'

HOST = "mysterious-sea.picoctf.net"
PORT = 52205  # Always check and update this port to match your active instance!

p = remote(HOST, PORT)

# The opcode signature for: mov DWORD PTR [rbp-0x4], <SECRET>
SIGNATURE = b"\xc7\x45\xfc"

# Clear out introductory text
p.recvuntil(b"Good luck >:)\n")
session_id = p.recvline().strip()
print(f"[*] Session ID: {session_id}")

for i in range(1, 21):
    print(f"\n[*] Solving Round [{i}/20] ...")
    
    # Read up to the hex dump announcement
    p.recvuntil(b"next binary in bytes:\n")
    
    # Read cleanly up to the colon without relying on trailing spaces
    hex_blob = p.recvuntil(b"What's the secret?:", drop=True).strip()
    
    # Strip layout fragments/newlines out of the stream 
    hex_blob = hex_blob.replace(b'\n', b'').replace(b'\r', b'').replace(b' ', b'')
    
    # Convert text data to raw object code bytes
    binary_bytes = unhex(hex_blob.decode())
    
    # Search for our instruction sequence signature
    idx = binary_bytes.find(SIGNATURE)
    if idx == -1:
        print("[-] Signature pattern not found in this round's binary!")
        break
        
    # Isolate key chunk and unpack integer value
    secret_bytes = binary_bytes[idx + len(SIGNATURE) : idx + len(SIGNATURE) + 4]
    secret_code = struct.unpack("<I", secret_bytes)[0]
    print(f"[+] Found Secret Code for Round {i}: {secret_code}")
    
    # Send the payload straight back instantly
    p.sendline(str(secret_code).encode())

# Drop to shell interface to extract the flag text
p.interactive()
Flag Capture
Upon running python3 solve.py, the script cleanly completes all 20 rounds automatically in under two seconds and opens interactive mode to deliver the flag:
Plaintext
Correct!
Woah, how'd you do that??

Here's your flag: picoCTF{4u7o_r3v_g0_brrr_78c345aa}

Flag: picoCTF{4u7o_r3v_g0_brrr_78c345aa}

________________________________________


