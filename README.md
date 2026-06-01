# AppInfo UAC Bypass - Standalone (Modified)

A refined and obfuscated version of the **AppInfo RPC + Debug Object** UAC bypass technique.

**Original Author**: [redcivet](https://github.com/redcivet/appinfo-standalone)  
**Modified & Obfuscated by**: Harjinder04

---

## Overview

This tool uses the AppInfo RPC interface to launch `ComputerDefaults.exe` elevated, steals its debug object, and spawns a new process (default: `cmd.exe`) with high integrity level **without triggering the UAC prompt**.

### Key Improvements Over Original
- **String Obfuscation**: All sensitive strings (paths, RPC protocol, executable names) are XOR encrypted at compile time.
- **Better OPSEC**: Reduces static detection surface.
- **Cleaner debugging output** with clear phases.
- Maintains full compatibility with the original technique.

---

## Features
- Bypasses UAC without user interaction (on vulnerable Windows versions)
- Runs as x64 only
- Uses legitimate Windows binaries (`winver.exe` + `ComputerDefaults.exe`)
- Parent process spoofing via `PROC_THREAD_ATTRIBUTE_PARENT_PROCESS`
- Built with Visual Studio / MinGW

---

## Supported Windows Versions
- Windows 10 (most builds)
- Windows 11 (up to certain builds — technique can be patched by Microsoft)

> **Note**: This is a living technique. Microsoft may patch it in future updates.

---

## Build Instructions

### Requirements
- Visual Studio 2022 (or newer) with C++ Desktop Development workload
- OR MinGW-w64

### 1. Using cl.exe (Visual Studio Command Line)

1. Open **x64 Native Tools Command Prompt for VS 2022** (important: use x64 version)
2. Navigate to the folder containing `UAC.c`
3. Compile with:

```cmd
cl.exe UAC.c /Fe:UAC.exe /O2 /MT /link rpcrt4.lib ntdll.lib advapi32.lib shlwapi.lib
```
> I just use the simple `cl.exe UAC.c` and done
**Recommended flags explained:**
- `/Fe:UAC.exe` → Output executable name
- `/O2` → Maximum optimization
- `/MT` → Static runtime (smaller & more portable binary)

For a cleaner debug build (if you want symbols):

```cmd
cl.exe UAC.c /Fe:UAC.exe /Zi /link rpcrt4.lib ntdll.lib advapi32.lib shlwapi.lib
```

### 2. Using Visual Studio IDE
1. Create a new **Win32 Console Application** (Empty Project)
2. Add `UAC.c` to the project
3. Set to **x64 Release**
4. In Project Properties → Linker → Input → Additional Dependencies:
   ```
   rpcrt4.lib;ntdll.lib;advapi32.lib;shlwapi.lib
   ```
5. Build Solution

### 3. Using MinGW-w64

```bash
x86_64-w64-mingw32-gcc UAC.c -o UAC.exe -l rpcrt4 -l ntdll -l advapi32 -l shlwapi -static -O2
```

---


## Usage

Simply run the compiled executable as a **medium integrity** process (normal user):

```cmd
UAC.exe
```

It will:
1. Launch `winver.exe` hidden with debug flag
2. Steal debug object
3. Launch `ComputerDefaults.exe` elevated
4. Spawn `cmd.exe` with high integrity from the elevated parent

---

## Customization

To change the payload, edit this line in `main()`:

```c
// Currently points to cmd.exe (XOR encrypted)
WCHAR payload_enc[] = { ... };
```

Replace with your desired executable path and recompile.

---

## Credits
- **Original Implementation**: [redcivet/appinfo-standalone](https://github.com/redcivet/appinfo-standalone)
- Technique based on AppInfo RPC abuse (publicly known method)

---

## Disclaimer
This tool is for **educational and research purposes only**.  
Use responsibly and only on systems you own or have explicit permission to test.

**I am not responsible** for any misuse or damage caused by this software.

---

## Legal
This project does not contain any malicious code. It demonstrates a known UAC bypass technique.
