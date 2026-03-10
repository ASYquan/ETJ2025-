# C++minus (cplusminus)

## Description

A C++ binary that manages USB and mouse devices through a menu interface. Partial RELRO, stack canaries, NX enabled. Get a shell.

## Files

- `cplusminus_exploit.py` — full working exploit with inline comments

## Solution

### Recon

Ran the binary to understand the interface:

```
1. Add USB device
2. Add Mouse device
3. Configure device
4. Display device info
5. Remove device
6. Exit
```

Checked the protections:

```bash
checksec --file=cplusminus
# Partial RELRO, Canary found, NX enabled, No PIE
```

No PIE means the binary itself is at a fixed address. Partial RELRO means the GOT is writable. Those two together are a classic setup for GOT overwrite.

Opened the binary in Ghidra and looked at how devices are stored. Each device object has a vtable pointer, a config string, and is heap-allocated. The menu actions call methods through the vtable.

### Finding the Bugs

Looking at the remove logic — when you remove a device, the object is freed but the pointer in the device list might still be accessible. Tested this by:

1. Adding a device
2. Removing it
3. Calling "Display device info" on it again

The binary printed garbage — it was using the freed memory. That's a use-after-free on the vtable pointer.

Also found a double-free: adding two devices, removing both in a certain order caused the same chunk to be freed twice without going through an intermediate allocation.

### Exploitation Plan

With a heap UAF and double-free, and a writable GOT at known addresses, the plan was:

1. Leak a heap address via UAF on freed device vtable
2. Leak libc via GOT read
3. Poison the tcache to write `system` address over `free@GOT`
4. Trigger `free()` on a device whose config is `/bin/sh`

### Step 1: Heap Leak

Allocated a device, freed it, then allocated a new device of the same size. The new allocation reused the freed chunk. Displaying the old device's info printed what was now the new object's data — including a heap pointer from the vtable area.

```python
add_usb()         # chunk A
remove(0)         # free chunk A
add_mouse()       # reuse chunk A
display(0)        # read old pointer -> heap leak
heap_base = leaked_ptr - known_offset
```

### Step 2: Libc Leak

With the heap address known I could place a fake device object with a controlled vtable. The vtable pointed into the GOT region. When `display()` was called, it dereferenced a GOT entry as a function pointer — but before calling it, it printed the config string which was set to the address of `free@GOT`.

Reading `free@GOT` gave the libc address of `free`. Subtract the known offset to get libc base:

```python
libc_base = free_got_value - libc.symbols['free']
system_addr = libc_base + libc.symbols['system']
```

### Step 3: Tcache Poisoning

In glibc's tcache, freed chunks store a `next` pointer at the start of their data. If I can write a controlled value there, the next allocation from that tcache bin will return a pointer to an arbitrary address.

Used the double-free to get two entries pointing to the same chunk in the tcache. Then allocated once to get that chunk back, and wrote `free@GOT` as the fake `next` pointer:

```python
add_usb()   # chunk B
add_usb()   # chunk C (same size as B)
remove(B)   # free B -> tcache[bin] = B
remove(C)   # free C -> tcache[bin] = C -> B
remove(B)   # free B again -> tcache[bin] = B -> C -> B (cycle)

alloc()     # get B back, write free@GOT as next pointer
# tcache[bin] = free@GOT
```

### Step 4: GOT Overwrite

Allocated again from the same bin. Got a pointer to `free@GOT`. Used the config setter to write `system`'s address there:

```python
alloc()                     # returns free@GOT
set_config(system_addr)     # write system over free@GOT
```

### Step 5: Shell

Added a device and set its config to `/bin/sh\x00`. Removed it, which called `free(config)`. Since `free@GOT` now points to `system`, this executed `system("/bin/sh")`:

```python
add_usb()
set_config(b"/bin/sh\x00")
remove()   # free(config) -> system("/bin/sh")
```

Shell spawned. Read the flag from the home directory.

The full exploit is in `cplusminus_exploit.py`. The heap state has to be kept consistent throughout — wrong allocation sizes or ordering breaks the tcache assumptions and you get a crash instead of a shell.

Run it with:

```bash
python3 cplusminus_exploit.py HOST=cplusminus PORT=1337
# or locally:
python3 cplusminus_exploit.py LOCAL EXE=./c+-
```
