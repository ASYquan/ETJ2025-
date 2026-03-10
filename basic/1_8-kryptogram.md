# 1.8: Kryptogram

## Description

A ciphertext tied to a 1935 Norwegian newspaper article. Break it and find the codeword.

## Solution

The task referenced **A-Magasinet, lørdag 23. november 1935** — a supplement to Aftenposten. The cryptogram was authored by **Egil Mørk**, who later documented early Norwegian signals intelligence in his work *Svartkammeret*. The plaintext describes suspicious espionage activity and bombing raids, in line with the political tension of the era.

The ciphertext itself was a monoalphabetic substitution cipher: same letter always maps to the same cipher letter, no transposition.

My first instinct was to check letter frequency manually:

```
most frequent cipher symbol -> likely E or N
next tier -> likely T, A, R, I
```

In Norwegian text the most common letters are E, N, T, A, R. I mapped the top frequency cipher symbols to those and started filling in partial words. Some word shapes started looking Scandinavian after four or five substitutions.

Rather than grind through the rest by hand I threw the ciphertext into [boxentriq.com](https://www.boxentriq.com/code-breaking/cryptogram) with Norwegian as the target language. The solver uses hill-climbing to maximize digraph scores — essentially it tries random substitutions and keeps any swap that makes the text look more like the target language.

It came back with a mostly clean plaintext in a few seconds. A couple of letters were still off, but reading the context made the correct substitutions obvious. The decrypted text matched the historical A-Magasinet article.

The codeword hidden in the plaintext:

```
atterloom
```

Flag:

```
c200dab665dcb92560f03d863fa04ccb
```
