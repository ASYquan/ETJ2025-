# 1.10: Breakout

## Description

Chain privilege escalation across six user accounts to reach the final flag.

## Solution

Started as `user1`. Each step was finding the credentials for the next user.

```bash
ls
# creds.txt
cat creds.txt
# user2:somepassword
su user2
```

Repeated this through users 2, 3, 4, and 5. Each one had credentials stashed somewhere accessible to the previous user — text files, environment variables, bash history, that kind of thing.

User 6 was different. After switching to `user6` I had a restricted shell that blocked any command containing a space:

```bash
cat /home/user6/flag.txt
# Restricted: illegal character
```

The restriction was checking for whitespace in the command string. The shell's `IFS` variable controls what counts as a word separator. By default it's a space, tab, and newline. If I change it, I can use something else as a separator instead of a space:

```bash
IFS=,
cat,/home/user6/flag.txt
```

The shell split on the comma and ran `cat` with `/home/user6/flag.txt` as the argument. Flag printed.

The `IFS` trick is one of those things that's easy to miss if you only ever sanitize for spaces without considering that the shell's parsing is configurable.

Flag: `9cc6d458db858f24fa935ec8651d795f`
