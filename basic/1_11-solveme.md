# 1.11: SolveMe

## Description

A C++ binary with four flags hidden inside. Each one uses a different protection mechanism.

## Solution

Started by running the binary:

```bash
./solveme
# Enter password for flag 1: test
# Wrong.
```

Ran `strings` to get a quick lay of the land:

```bash
strings solveme | grep -i pass
# SuperSecretPass!
```

**Flag 1** was that easy. Typed `SuperSecretPass!` into the first prompt, got the flag.

For the rest I opened the binary in Ghidra and worked through each check.

**Flag 2:** The function was comparing the input against a string pulled from a specific offset in memory. In Ghidra I found the reference and read the value directly: `a beautiful pass`. Entered it, got the flag.

**Flag 3:** More interesting. The check was:

```c
for (int i = 0; i < len; i++) {
    if (rotate_left(input[i] ^ key, 5) != target[i]) return 0;
}
```

Reversed it in Python:

```python
def rotate_right(b, n):
    return ((b >> n) | (b << (8 - n))) & 0xFF

result = "".join(chr(rotate_right(b, 5) ^ key) for b in target_bytes)
print(result)
```

Gave me the password. Flag 3 done.

**Flag 4:** The binary was decrypting something with AES using a 4-character key. The keyspace for printable 4-character strings is small enough to brute force:

```python
from itertools import product
from Crypto.Cipher import AES
import string

chars = string.printable.strip()
ciphertext = bytes.fromhex("...")
iv = bytes.fromhex("...")

for combo in product(chars, repeat=4):
    key = "".join(combo).encode().ljust(16, b'\x00')
    try:
        cipher = AES.new(key, AES.MODE_CBC, iv)
        pt = cipher.decrypt(ciphertext)
        if b"flag" in pt or pt.isascii():
            print(combo, pt)
            break
    except:
        pass
```

Ran for a couple of minutes and printed the plaintext with the flag inside.

Flags:
- Flag 1: `FLAG{Strings_are_all_you_need}`
- Flag 2: `FLAG{Start_the_day_on_the_right_offset}`
- Flag 3: `FLAG{Don't_get_lost_on_your_way_home}`
- Flag 4: `FLAG{Brutus_doesnt_remember_passwords}`
- Flag 5: `FLAG{A_sparkling_hidden_gem}`
