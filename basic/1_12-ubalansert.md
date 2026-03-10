# 1.12: Ubalansert

## Description

An RSA-encrypted message and its public key. Decrypt it.

## Solution

Downloaded the public key and ciphertext. First I looked at the key:

```bash
openssl rsa -pubin -in key.pub -text -noout
```

The modulus was large but I noticed something: the exponent and key size suggested it might be vulnerable. Threw it straight at `RsaCtfTool` with a range of factorization methods:

```bash
python3 RsaCtfTool.py --publickey key.pub --private
```

It cycled through algorithms and hit on Pollard's rho fairly quickly. The two primes were close together or poorly chosen, which is exactly what Pollard's rho exploits. It spit out the private key:

```
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
```

Used it to decrypt the ciphertext:

```bash
python3 RsaCtfTool.py --publickey key.pub --private --uncipherfile ciphertext.bin
```

Got the plaintext with the flag inside.

Flag: `73be942a1cd6a5a62fd78e6f443d87ed`

The lesson here is that RSA is only as strong as the primes. If p and q are close in value, the modulus can be factored by searching around its square root. Proper key generation should never produce primes that are close to each other.
