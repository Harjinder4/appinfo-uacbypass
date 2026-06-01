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
- Visual Studio 2022 (recommended) or MinGW-w64
- Target: **x64 Release**

### Steps (Visual Studio)
1. Create a new **Win32 Console Application** (Empty Project)
2. Add the file `UAC.c` to the project
3. Set project to **x64**
4. In Project Properties:
   - **C/C++ → General → Additional Include Directories**: Add `%ProgramFiles(x86)%\Windows Kits\10\Include\10.0.22621.0\um`
   - **Linker → Input → Additional Dependencies**: `rpcrt4.lib;ntdll.lib;advapi32.lib;shlwapi.lib`
5. Build → Build Solution

### Alternative (MinGW)
```bash
x86_64-w64-mingw32-gcc UAC.c -o UAC.exe -l rpcrt4 -l ntdll -l advapi32 -l shlwapi -static
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
