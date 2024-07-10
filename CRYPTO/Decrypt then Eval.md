# Decrypt then Eval

Category: Crypto (Easy)

Points: 118

Solves: 197

>This server decrypts user input and evaluates it. Please use your magical malleability mastership to retrieve the flag!


### Script

Solved by qpwoeirut, idk what black magic trickery he did but it worked:

https://github.com/qpwoeirut/CTFs/blob/master/DownUnderCTF/2024/crypto/decrypt_then_eval/solve_decrypt_then_eval.py

Kinda an inefficient solution, realized after that we could first leak `key` and `iv` instead of brute forcing stuff and only leaking `flag`


### Flag

```DUCTF{should_have_used_authenticated_encryption!}```
