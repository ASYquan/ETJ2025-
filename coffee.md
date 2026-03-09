# Coffee Machine – Medium Difficulty Tasks

A coffee machine is exposed over the internet. Your goal is to gain access, escalate to root, and retrieve all flags.

### Part 1 – User Flag
- Explore the web interface and its source code.
- Find a way to log in as `admin`.
- Discover an internal maintenance server and exploit a path traversal vulnerability to obtain a private key.
- Use the key to sign your own maintenance script and gain a reverse shell as the `coffee` user.
- Retrieve the user flag from `/home/coffee/user_flag.txt`.

### Part 2 – Root Flag
- Once inside as `coffee`, investigate the system.
- Find a SUID binary `/usr/local/bin/sync-etc-wrapper`.
- Understand how it synchronizes files with `/etc`.
- Abuse a symlink and a PATH hijack to make the wrapper set the SUID bit on `su‑exec`.
- Use `su‑exec` to become `root` and read the root flag.

### Part 3 – Server Flag
- The maintenance server (running on a different pod) is also vulnerable to path traversal.
- Use the same technique to fetch the server’s `entrypoint.sh` and discover that it writes a flag from an environment variable.
- Retrieve the server flag from `/server_flag.txt`.