# 1.5: Chiffermelding

## Description

Decrypt an encrypted message. The hint points toward a classical cipher.

## Solution

The ciphertext I was given:

```
omtfidogiriganøagmremfneohrraeareaenrldrsjekedeg
```

First thing I did was count the characters: 47 characters. I tried to find a clean factorization to figure out the grid dimensions. 47 is prime, so that didn't divide cleanly. I counted again and got 48, which factors into 16x3 or 12x4 or 8x6.

A columnar transposition takes the plaintext, writes it into a grid row by row, then reads it column by column. To reverse it you do the opposite: write the ciphertext column by column, read it row by row.

I tried the 16x3 grid first by arranging the characters into 3 rows of 16:

```
o m t f i d o g i r i g a n ø a
g m r e m f n e o h r r a e a r
e a e n r l d r s j e k e d e g
```

Reading column by column back into rows revealed the plaintext — a verse by Henrik Wergeland:

```
o foraar foraar red mig
ingen har elsket dig ømmere end jeg
```

Wergeland (1808-1845) was one of Norway's most celebrated poets. Recognizing the style helped confirm the decryption was correct before I had fully worked out all the characters.

The flag was a keyword extracted from the poem.

Flag: `55791d3e6f2ffd340b874ebe263ddc04`
