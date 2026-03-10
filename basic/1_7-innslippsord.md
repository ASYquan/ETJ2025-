# 1.7: Innslippsord

## Description

A binary checks a password. Figure out what it expects.

## Solution

Started by running the binary to understand the interface:

```bash
./innslippsord
# Enter password: test
# Wrong password.
```

Ran `strings` on it first to see if anything was hardcoded:

```bash
strings innslippsord
```

Nothing obvious. Opened it in Ghidra and found the password check function. The logic looked roughly like this:

```c
int check_password(char *input) {
    char target[] = { 0x..., 0x..., ... };
    char transformed[32];

    for (int i = 0; i < len; i++) {
        transformed[i] = rotate_left(input[i], 3) ^ 0xAB;
    }

    return memcmp(transformed, target, len) == 0;
}
```

The binary applies a left rotation by 3 bits and XORs with a key, then compares to a stored value. To recover the original password I needed to reverse both operations in order: XOR first, then right rotate.

Wrote a quick Python script:

```python
target = [0x..., ...]  # bytes from Ghidra
key = 0xAB

def rotate_right(byte, n):
    return ((byte >> n) | (byte << (8 - n))) & 0xFF

password = ""
for b in target:
    password += chr(rotate_right(b ^ key, 3))

print(password)
```

Output:

```
168065a0236e2e64c9c6cdd086c55f63
```

Plugged that into the binary, got the flag.

Flag: `168065a0236e2e64c9c6cdd086c55f63`
