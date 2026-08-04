picoCTF 2026 – ClusterRSA

Category: Cryptography

Difficulty: Medium

Points: 400
________________________________________
Challenge Description : 
A message has been encrypted using RSA, but this time something feels more crowded than usual. Instead of the traditional two-prime RSA modulus, the modulus n is composed of four prime numbers. Our task is to recover the plaintext by factoring n, reconstructing the private key, and decrypting the ciphertext.
________________________________________
Files Provided
message.txt

Contents:
n = 8749002899132047699790752490331099938058737706735201354674975134719667510377522805717156720453193651
e = 65537
ct = 2630159242114455882250729812770100011736485763047361297871782218963814793905003742546116295910618429
________________________________________
Step 1 – Download the Challenge File
cd /tmp

wget https://challenge-files.picoctf.net/c_plain_mesa/f600b573fbbb4688eaa37b6d000c580d15b8f059ed1663980772b2e62f8222b8/message.txt

cat message.txt
Output:
n = 8749002899132047699790752490331099938058737706735201354674975134719667510377522805717156720453193651
e = 65537
ct = 2630159242114455882250729812770100011736485763047361297871782218963814793905003742546116295910618429
________________________________________
Step 2 – Analyze the Hints

Hint 1:
RSA usually means two primes... but what if someone got greedy?

Hint 2:
Prime factors decomposition

These hints suggest that the modulus n is a product of more than two prime numbers.
________________________________________
Step 3 – Factor the Modulus
Use FactorDB to factor the modulus.
n =
8749002899132047699790752490331099938058737706735201354674975134719667510377522805717156720453193651
FactorDB returns:
9671406556917033397931773
9671406556917033398314601
9671406556917033398439721
9671406556917033398454847
Thus,
n = p1 × p2 × p3 × p4
________________________________________
Step 4 – Write the Decryption Script
Create the solver.
nano solve.py
Paste:
import math

def egcd(a, b):
    if a == 0:
        return b, 0, 1
    g, y, x = egcd(b % a, a)
    return g, x - (b // a) * y, y

def modinv(a, m):
    g, x, y = egcd(a, m)
    return x % m if g == 1 else None

def decrypt_multi_prime_rsa(primes, ciphertext, e=65537):
    n = math.prod(primes)
    phi = math.prod(p - 1 for p in primes)
    d = modinv(e, phi)
    return pow(ciphertext, d, n)

if __name__ == "__main__":

    primes = [
        9671406556917033397931773,
        9671406556917033398314601,
        9671406556917033398439721,
        9671406556917033398454847
    ]

    e = 65537

    ciphertext = 2630159242114455882250729812770100011736485763047361297871782218963814793905003742546116295910618429

    plaintext = decrypt_multi_prime_rsa(primes, ciphertext, e)

    print("Plaintext Integer:")
    print(plaintext)

    text = plaintext.to_bytes((plaintext.bit_length() + 7) // 8, "big").decode("utf-8")

    print("\nFlag:")
    print(text)
________________________________________
Step 5 – Run the Script
python3 solve.py
Output:
Plaintext Integer:
46243480260873703236831836812097399487664424587269161399893845117

Flag:
picoCTF{mul71_rsa_8c9fb77d}
________________________________________
Explanation
The RSA modulus is composed of four prime numbers instead of two.
First, compute:
n = p1 × p2 × p3 × p4
Next, compute Euler's Totient:
φ(n) = (p1−1)(p2−1)(p3−1)(p4−1)
Then calculate the private exponent:
d = e⁻¹ mod φ(n)
Finally, decrypt the ciphertext:
m = cᵈ mod n
Convert the resulting integer into bytes to recover the original plaintext (flag).
________________________________________
Commands Used
cd /tmp

wget https://challenge-files.picoctf.net/c_plain_mesa/f600b573fbbb4688eaa37b6d000c580d15b8f059ed1663980772b2e62f8222b8/message.txt

cat message.txt

nano solve.py

python3 solve.py
________________________________________

Flag
picoCTF{mul71_rsa_8c9fb77d}
________________________________________
Key Takeaways
•	Standard RSA uses two prime factors, while Multi-Prime RSA uses three or more.
•	If all prime factors of n are known, recovering the private key is straightforward.
•	Euler's Totient for Multi-Prime RSA is:
•	φ(n) = ∏ (pi − 1)
•	The private exponent is computed using the modular inverse:
•	d = e⁻¹ mod φ(n)
•	Once d is obtained, decryption is performed with:
•	m = cᵈ mod n
•	The decrypted integer is converted into bytes to reveal the original message.
This challenge demonstrates that the security of RSA—whether using two primes or multiple primes—depends entirely on the difficulty of factoring the modulus. Once the prime decomposition is known, the private key and plaintext can be recovered efficiently.
________________________________________

