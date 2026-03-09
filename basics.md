# Grunnleggende – Beginner Tasks

These are the introductory challenges from the E 2025 CTF. Each task introduces a basic concept.

### 1.1 Scoreboard
Learn how to use the `scoreboard` command to view unlocked challenges and submit flags.

Okay, first task. They want us to use the scoreboard command. The LESMEG.md file says:

    Gjør deg kjent med scoreboard-kommandoen. Denne bruker du for å se oversikt over oppgaver du har låst opp og kan løse, sammen med oppgaver du allerede har løst.

The flag is literally... right there in the text. Like, just sitting there. ETJ{e4d8fc322417e82764c82923b9eb4f80}. I feel like this is a trap? But no, it's just the welcome mat.
bash

login@corax ~/1_grunnleggende/1.1_Scoreboard $ scoreboard e4d8fc322417e82764c82923b9eb4f80

And it worked. 
Flagg: ETJ{e4d8fc322417e82764c82923b9eb4f80}
---

### 1.2 Username
Learn how to use the `username` command to view or change your display name on the scoreboard website.

Same energy. The LESMEG.md file just hands me the flag:

    Når du føler deg fornøyd kan du levere dette flagget for å fullføre oppgaven: 80d32d56199a49d90fd489d139defd0c

bash

login@corax ~/1_grunnleggende/1.2_Username $ scoreboard 80d32d56199a49d90fd489d139defd0c

The response: "En hacker med et tøft brukernavn er bedre enn en uten." 

Flagg: ETJ{80d32d56199a49d90fd489d139defd0c}
---
### 1.3 FAQ
Find a flag hidden on the FAQ page of the CTF website.

### 1.4 Discord
Join the official Discord server and locate the flag in the channel topic of the `#cybertalent` channel.

### 1.5 Chiffermelding
Connect to a service that gives you an encrypted message. Decode it to find the original text and identify who wrote it.

### 1.6 Unchained
SSH into a machine with a given password. There is a `secret.txt` file that only root can read. Find a way to read it.

### 1.7 Innslippsord
Reverse engineer a binary that checks a password. The password is the flag.

### 1.8 Kryptogram
Connect to a service that provides a cryptogram. Decrypt it using substitution cipher techniques and extract the code word.

### 1.9 NoSQL
Explore a web‑based flag checker that claims to be protected against SQL injection. Find a way to retrieve the flag without using classic SQLi.

### 1.10 Breakout
SSH into a machine as `user1` and escalate through several users (`user2` → `user6`). Each user’s home directory contains a file with credentials for the next user. The final user has a restricted shell; bypass it to read the flag.

### 1.11 SolveMe
Analyze a C++ binary that asks for four passwords. Each correct password decrypts and prints a part of the flag. There is also a hidden fifth flag somewhere in the binary.

### 1.12 Ubalansert
Connect to an RSA service that gives you `n`, `e`, and the ciphertext. The primes `p` and `q` are poorly chosen; factor `n` to recover the plaintext.

### 1.13 RSA7777
SSH access to a server only allows login with a specific SSH key. You are given the public key. Derive the private key and log in to read the flag.

### 1.14 Apollo
Connect to a server that lets you encrypt arbitrary messages and decrypt ciphertexts, but blocks decryption of the flag. Use a padding oracle attack to recover the flag.
