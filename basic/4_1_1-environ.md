# 4.1.1: environ

## Description

A hidden flag is stored somewhere on the system. No direct path given.

## Solution

Poked around the filesystem first looking for anything obvious:

```bash
find / -name "*flag*" 2>/dev/null
find / -name "*.txt" 2>/dev/null | head -20
```

Nothing. Checked environment variables for the current shell:

```bash
env | grep -i flag
printenv
```

Also empty. That meant the flag probably wasn't in my process's environment.

Then I noticed a `ls` process running constantly in the background. If a process was launched with a flag as an environment variable, that flag lives in `/proc/[PID]/environ` for the lifetime of the process.

The challenge was timing: `ls` exits almost instantly, so I needed to catch its PID while it was still alive. I wrote a loop to continuously poll for it:

```bash
while true; do
    for pid in $(pgrep ls 2>/dev/null); do
        result=$(cat /proc/$pid/environ 2>/dev/null | tr '\0' '\n' | grep -i flag)
        if [ -n "$result" ]; then
            echo "$result"
            exit
        fi
    done
done
```

Ran it and within a few seconds it caught the process mid-execution and printed:

```
FLAG=<flag value>
```

The key insight is that environment variables passed to a process don't disappear when the environment is cleared inside the program. They stay accessible via `/proc/[PID]/environ` until the process exits.

Flag: `57bca57ff4c67582ba0a32917db4a266`
