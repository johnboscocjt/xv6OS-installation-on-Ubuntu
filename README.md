# xv6OS-installation-on-Ubuntu
# xv6 Installation & Configuration Guide (Ubuntu 2026)

## 1. Prerequisites & Toolchain
This guide documents the successful setup of the xv6-public (x86) operating system on a native Ubuntu environment, resolving common 2026-specific compilation and emulation errors.
Modern Ubuntu versions (Noble Tahr and later) have deprecated the generic qemu package. You must install the specific system emulators and 32-bit compatibility layers.

```markdown
```bash
sudo apt update
sudo apt install git build-essential qemu-system-x86 gcc-multilib libc6-dev-i386
```

## 2. Source Code Setup

Clone the official MIT xv6-public repository:

```bash
git clone https://github.com/mit-pdos/xv6-public.git
cd xv6-public
```

## 3. Resolving Compilation Errors (GCC 14+ Fixes)

Modern compilers treat old code warnings as fatal errors. We applied these modifications to the Makefile to allow a successful build.

### A. Disable "Warnings-as-Errors"

Remove the `-Werror` flag which causes the "recursive call" and "array bounds" errors to stop the build:

```bash
sed -i 's/-Werror//g' Makefile
```

### B. Disable Array Bounds Safety Checks

Add a specific flag to bypass modern pointer-safety checks required by 2026 compilers:

```bash
sed -i 's/CFLAGS = /CFLAGS = -Wno-error=array-bounds /g' Makefile
```

### C. Update QEMU Binary Path

The Makefile defaults to `qemu-system-i386`. On modern 64-bit Ubuntu, update this to `qemu-system-x86_64`:

```bash
sed -i 's/qemu-system-i386/qemu-system-x86_64/g' Makefile
```

## 4. Building the OS

After modifying the Makefile, compile the system:

```bash
make clean
make
```

## 5. Running xv6

You can run xv6 in two modes. Since xv6 is a command-line OS, Terminal Mode is the most stable.

### Mode A: Terminal Mode (Recommended)

This runs xv6 directly inside your Ubuntu terminal.

```bash
make qemu-nox
```

- **To Exit**: Press `Ctrl + A`, then release and press `X`.

### Mode B: Windowed Mode (GUI)

This opens a separate VGA window.

```bash
make qemu
```

## 6. Global Access (Run from Anywhere)

To run xv6 without navigating to its specific folder every time, create a terminal alias.

1. Open your bash config: `nano ~/.bashrc`
2. Add this line to the bottom (update the path to your actual xv6 folder):
   ```bash
   alias xv6='cd ~/xv6-public && make qemu-nox'
   ```
3. Save and reload: `source ~/.bashrc`
4. Usage: Simply type `xv6` in any terminal window.

## Troubleshooting Summary

| Error | Solution |
|-------|----------|
| Package 'qemu' has no installation candidate | Install `qemu-system-x86` instead. |
| sh.c: error: recursive call [-Werror] | Remove `-Werror` from Makefile. |
| mp.c: error: array subscript is outside array bounds | Add `-Wno-error=array-bounds` to CFLAGS. |
| make: *** No rule to make target 'qemu-nox' | Ensure you are inside the `xv6-public` directory. |
| qemu-system-i386: Command not found | Update Makefile to use `qemu-system-x86_64`. |
```
