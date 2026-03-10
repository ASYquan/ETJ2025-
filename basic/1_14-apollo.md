# 1.14: Apollo

## Description

An AES-CBC encrypted service. Decrypt the ciphertext and recover the flag.

## Solution

The service accepted ciphertext and returned different responses depending on whether the decrypted padding was valid or not. That's a padding oracle.

First I confirmed the oracle behavior manually:

```bash
# Valid ciphertext -> returns decrypted data or "OK"
# Modified last byte -> returns "Invalid padding"
```

Two distinct responses based on padding validity is all you need. The padding oracle attack works by flipping bits in the ciphertext byte by byte and observing the response. When the padding is valid, you've found a byte that when XORed with the original produces `\x01` (or `\x02\x02`, etc. depending on position). From that you can derive the plaintext byte.

Rather than implement it from scratch I used the `padding_oracle` Python library:

```python
from padding_oracle import padding_oracle
import requests

def oracle(ciphertext):
    r = requests.post("http://target/decrypt", data={"ct": ciphertext.hex()})
    return "Invalid padding" not in r.text

iv = bytes.fromhex("...")
ciphertext = bytes.fromhex("...")

plaintext = padding_oracle(iv + ciphertext, block_size=16, oracle=oracle)
print(plaintext)
```

Ran it and it worked through the blocks one byte at a time. Took a few minutes since it's making a request per byte guess. The output had the flag in the plaintext.

Flag: `4eac5e94b9b76f3e6901fb6f22881c2e`

Padding oracle attacks are a good example of how a small information leak — just knowing if padding is valid or not — is enough to fully break encryption.
