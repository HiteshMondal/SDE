# Windows OS — Complete Reference

A comprehensive guide to Windows for IT Support Engineers, System Administrators, and Software Engineers: concepts, built-in tools, CMD and basic PowerShell commands, troubleshooting, and real-world examples.

> Scope: CMD and basic PowerShell **commands** (one-liners, cmdlets, pipelines). PowerShell scripting (`.ps1` logic, functions, modules) is intentionally excluded — use Bash/Shell for scripting, and WSL when a Linux-style script must run on a Windows machine.

## Table of Contents

- [Core Concepts](#core-concepts)
- [Shell and Terminal Basics](#shell-and-terminal-basics)
- [File and Directory Commands](#file-and-directory-commands)
- [Permissions and Ownership](#permissions-and-ownership)
- [Users, Groups and Access](#users-groups-and-access)
- [Searching and Text Processing](#searching-and-text-processing)
- [Networking](#networking)
- [LAN and Workgroup Networking](#lan-and-workgroup-networking)
- [Archives and Compression](#archives-and-compression)
- [Package and Software Management](#package-and-software-management)
- [Disk, Partitioning and Storage](#disk-partitioning-and-storage)
- [System Monitoring and Resources](#system-monitoring-and-resources)
- [Process and Service Management](#process-and-service-management)
- [Scheduling with Task Scheduler](#scheduling-with-task-scheduler)
- [Logs and Event Viewer](#logs-and-event-viewer)
- [Registry](#registry)
- [Windows Update and Servicing](#windows-update-and-servicing)
- [Repair and Troubleshooting](#repair-and-troubleshooting)
- [Common Windows Issues and Fixes](#common-windows-issues-and-fixes)
- [Boot Process](#boot-process)
- [Security Fundamentals](#security-fundamentals)
- [Active Directory and Enterprise Management](#active-directory-and-enterprise-management)
- [Windows Server Roles Quick Reference](#windows-server-roles-quick-reference)
- [Remote Access and Administration](#remote-access-and-administration)
- [Windows for Developers](#windows-for-developers)
- [Virtualization and Containers](#virtualization-and-containers)
- [Power, Printers and Peripherals](#power-printers-and-peripherals)
- [Backup and Recovery](#backup-and-recovery)
- [Installing, Deploying and Migrating Windows](#installing-deploying-and-migrating-windows)
- [Error Codes and Stop Codes](#error-codes-and-stop-codes)
- [Linux to Windows Command Mapping](#linux-to-windows-command-mapping)
- [Scenario-Based Troubleshooting](#scenario-based-troubleshooting)
- [Windows Gotchas for Linux Users](#windows-gotchas-for-linux-users)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)

---

## Core Concepts

### Windows Editions and Versions

Windows is a family of operating systems from Microsoft built on the **NT kernel**.

| Edition | Target | Notable Features |
|---|---|---|
| Home | Consumers | Basic features, no domain join, no Group Policy editor, no RDP host, no BitLocker full management |
| Pro | Power users, small business | Domain join, Group Policy, BitLocker, Hyper-V, RDP host, Windows Sandbox |
| Pro for Workstations | High-end workstations | ReFS, more RAM/CPU sockets, faster file sharing |
| Enterprise | Organizations | Advanced security, AppLocker, Credential Guard, long servicing options, volume licensing |
| Education | Schools | Enterprise feature set, education licensing |
| IoT Enterprise / LTSC | Embedded, kiosks, fixed-function | Long-Term Servicing Channel, no feature updates |
| Server | Datacenter and servers | Roles and features: AD DS, DNS, DHCP, IIS, Hyper-V, File Services |

#### Version Naming and Support

- Windows 10 reached end of support on **October 14, 2025**. Consumer **Extended Security Updates (ESU)** exist for Windows 10 22H2; the programme end date has been extended by Microsoft, so verify the current date on Microsoft's lifecycle page before advising a customer.
- Windows 11 ships one feature update per year, named by half-year (for example `24H2`, `25H2`, `26H2`). Updates for the same servicing branch are often delivered as a small **enablement package** followed by one restart.
- Home and Pro receive **24 months** of support per version; Enterprise and Education receive **36 months**.
- Windows 11 `24H2` Home/Pro support ended on **October 13, 2026**; `25H2` Home/Pro runs to October 2027; `26H2` was released on September 29, 2026.
- Build numbers: Windows 10 22H2 = `19045`, Windows 11 24H2 = `26100`, 25H2 = `26200`. `26H1` shipped only on new hardware.
- Windows Server releases are separate (Server 2019, 2022, 2025) with their own lifecycle.

```cmd
winver                      :: GUI dialog with version and build
ver                         :: short version string
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v DisplayVersion
```

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber
```

#### Windows 11 Hardware Requirements

| Component | Minimum |
|---|---|
| CPU | 64-bit, 1 GHz, 2+ cores, compatible processor list |
| RAM | 4 GB |
| Storage | 64 GB |
| Firmware | UEFI with Secure Boot capable |
| TPM | Version 2.0 |
| Graphics | DirectX 12 compatible, WDDM 2.0 driver |
| Display | 9-inch or larger, 720p |

```powershell
Get-Tpm                     # TPM status
Confirm-SecureBootUEFI      # True if Secure Boot is enabled (admin)
```

```cmd
tpm.msc                     :: TPM management console
msinfo32                    :: shows BIOS Mode (UEFI/Legacy) and Secure Boot State
```

### Kernel Architecture

Windows uses a **hybrid kernel** (the NT kernel). It combines microkernel-style design with monolithic-style performance: most core services run in kernel mode.

```text
+-----------------------------------------------------------+
| User Mode                                                 |
|  Applications  |  Services (svchost.exe)  |  Subsystems   |
|  Win32 API (kernel32.dll, user32.dll, advapi32.dll)       |
|  ntdll.dll  ->  system call transition                    |
+-----------------------------------------------------------+
| Kernel Mode                                               |
|  Executive: Object Manager, I/O Manager, Memory Manager,  |
|             Process Manager, Security Reference Monitor,  |
|             Plug and Play, Power Manager, Cache Manager   |
|  Kernel (ntoskrnl.exe)  |  Device Drivers                 |
|  HAL (Hardware Abstraction Layer)                         |
+-----------------------------------------------------------+
| Hypervisor (Hyper-V) and Hardware                         |
+-----------------------------------------------------------+
```

| Concept | Meaning |
|---|---|
| User mode | Restricted environment for applications; a crash affects only that process |
| Kernel mode | Full hardware access; a driver crash can cause a Blue Screen (BSOD) |
| `ntdll.dll` | Lowest user-mode layer; transitions into the kernel via system calls |
| HAL | Hides hardware differences from the kernel |
| Win32 API | Main programming interface for Windows applications |
| Virtualization-Based Security (VBS) | Uses the hypervisor to isolate sensitive kernel and credential data |

### Processes, Threads and Services

- A **process** is a running program with its own virtual address space; a **thread** is the execution unit inside it.
- Windows does **not** use `fork()`. Processes are created with `CreateProcess`, which makes process creation heavier than on Linux (relevant for build tools and shell scripts running under Windows).
- **Services** are background processes managed by the **Service Control Manager** (`services.exe`), similar to Linux daemons.
- Many services share a host process: `svchost.exe`. Use `tasklist /svc` to see which services run inside each one.
- Since Windows Vista, services run in **Session 0** (isolated, no desktop). User programs run in Session 1 and above.

| Key System Process | Role |
|---|---|
| `System` (PID 4) | Kernel threads and drivers |
| `smss.exe` | Session Manager, first user-mode process |
| `csrss.exe` | Client/Server Runtime, console and window handling |
| `wininit.exe` | Initializes Session 0, starts `services.exe` and `lsass.exe` |
| `services.exe` | Service Control Manager |
| `lsass.exe` | Local Security Authority: authentication, token creation, policy |
| `winlogon.exe` | Interactive logon and Secure Attention Sequence (Ctrl+Alt+Del) |
| `explorer.exe` | Desktop shell: taskbar, Start menu, File Explorer |
| `svchost.exe` | Generic host for service DLLs |
| `dwm.exe` | Desktop Window Manager (compositing) |

> Security note: a second `lsass.exe`, or one not running from `C:\Windows\System32`, is a classic malware indicator.

### File Systems

| File System | Characteristics | Typical Use |
|---|---|---|
| NTFS | Journaling, ACL permissions, compression, EFS encryption, hard links, junctions, symlinks, alternate data streams, quotas, shadow copies | System and data drives |
| ReFS | Resilient, checksums for integrity, large volumes, used by Dev Drive and Storage Spaces | Servers, Dev Drive |
| exFAT | No ACLs, large file support, cross-platform | USB drives, SD cards |
| FAT32 | Max file size 4 GB, maximum partition 2 TB (Windows GUI formats up to 32 GB) | Legacy, UEFI System Partition |

```cmd
fsutil fsinfo volumeinfo C:       :: file system type and flags
fsutil fsinfo drives              :: list drive letters
```

### Drive Letters and Paths

Windows exposes storage through **drive letters** (`C:\`), not a single root `/` tree.

```text
C:\Users\hitesh\Documents\file.txt      Absolute path
..\Documents\file.txt                   Relative path
\\server\share\folder\file.txt          UNC path (network share)
\\?\C:\very\long\path                   Extended-length path prefix (bypasses 260-char limit)
\\.\PhysicalDrive0                      Device namespace
```

| Rule | Details |
|---|---|
| Separator | Backslash `\` (forward slash `/` works in most APIs but not in `cmd` commands that use `/` for options) |
| Case | **Case-insensitive but case-preserving** (`File.txt` and `file.txt` are the same file) |
| Invalid characters | `\ / : * ? " < > |` |
| Reserved names | `CON`, `PRN`, `AUX`, `NUL`, `COM1`-`COM9`, `LPT1`-`LPT9` (even with an extension) |
| Trailing dot/space | Not allowed in names |
| `MAX_PATH` | 260 characters by default; long paths need opt-in |
| PATH separator | Semicolon `;` (Linux uses colon `:`) |

```cmd
:: Enable long path support (admin), requires app support too
reg add "HKLM\SYSTEM\CurrentControlSet\Control\FileSystem" /v LongPathsEnabled /t REG_DWORD /d 1 /f
```

### File Extensions Matter

Unlike Linux, Windows uses the **file extension** to decide which program opens a file and whether it is executable.

- Executable types: `.exe`, `.com`, `.bat`, `.cmd`, `.msi`, `.ps1`, `.vbs`, `.js`, `.scr`, `.lnk`, `.dll` (loaded, not run directly)
- Order of extensions tried when you type a name without extension is defined by `%PATHEXT%`
- Windows hides known extensions by default; enable **View > Show > File name extensions** in File Explorer. This hides tricks such as `invoice.pdf.exe`.

```cmd
assoc .txt                :: shows file type association
ftype txtfile             :: shows the command that opens it
echo %PATHEXT%            :: .COM;.EXE;.BAT;.CMD;.VBS;.JS;.WSF;.MSC
```

### Windows-Specific File Extensions Reference

These extensions are meaningful to the OS itself (loader, installer, shell, servicing, boot). Common document and media types are intentionally left out.

#### Executables, Libraries and Drivers

| Extension | What It Is | Notes |
|---|---|---|
| `.exe` | Portable Executable (PE) program | `.dll`, `.sys`, `.scr`, `.cpl`, `.ocx` use the same PE format |
| `.dll` | Dynamic Link Library | Shared code loaded at runtime; cannot be launched directly (`rundll32`, `regsvr32`) |
| `.sys` | Kernel-mode driver | Lives in `System32\drivers`; a faulty one causes a BSOD |
| `.scr` | Screen saver | An EXE in disguise; a common malware trick |
| `.cpl` | Control Panel applet | A DLL: `control appwiz.cpl` |
| `.ocx` | ActiveX / COM control | Register with `regsvr32` |
| `.ax` | DirectShow filter | Media codec or filter |
| `.msc` | MMC console | `services.msc`, `eventvwr.msc` |
| `.efi` | UEFI application | `bootmgfw.efi`, `winload.efi` in the EFI System Partition |
| `.mui` | Language resource file | In `en-US` subfolders next to the binary |
| `.mof` | WMI class definition | Compiled with `mofcomp file.mof` |
| `.manifest` | Application manifest | UAC level, DPI awareness, side-by-side dependencies |
| `.com` | Legacy DOS executable | Still runs; also appears in `PATHEXT` |
| `.drv` | Legacy driver library | 16-bit era |

#### Installers, Packages and Images

| Extension | What It Is | Notes |
|---|---|---|
| `.msi` | Windows Installer package | A database of install actions; run via `msiexec` |
| `.msp` | Installer patch | Applies on top of an installed MSI |
| `.mst` | Installer transform | Customizes an MSI (`TRANSFORMS=x.mst`) |
| `.msm` | Merge module | Shared components bundled into MSIs |
| `.msu` | Update standalone package | Installed with `wusa` |
| `.cab` | Cabinet archive | Update and driver payloads |
| `.msix`, `.msixbundle`, `.appx`, `.appxbundle` | Modern app packages | `Add-AppxPackage` |
| `.appinstaller` | MSIX update-source descriptor | Enables auto-update of sideloaded MSIX |
| `.inf` | Setup information file | Driver install (`pnputil /add-driver`) |
| `.cat` | Security catalog | Digital signature for a driver package |
| `.wim` | Windows Imaging file | `install.wim` and `boot.wim`; file-based, multi-image |
| `.esd` | Compressed Windows image | Smaller than WIM, read-only style |
| `.swm` | Split WIM | Needed to fit `install.wim` over 4 GB on FAT32 |
| `.ppkg` | Provisioning package | Built with Windows Configuration Designer |
| `.sdi` | System Deployment Image | `boot.sdi` for WinPE ramdisk boot |
| `.iso` | Optical disc image | Double-click to mount |
| `.vhd` / `.vhdx` | Virtual hard disk | Hyper-V, Disk Management, WSL (`ext4.vhdx`) |
| `.avhdx` | Hyper-V checkpoint (differencing) disk | Do not delete manually |
| `.vmcx` / `.vmrs` | Hyper-V VM configuration / runtime state | |
| `.wsb` | Windows Sandbox configuration | Defines mapped folders, networking |

#### Scripts and Shortcuts

| Extension | What It Is | Notes |
|---|---|---|
| `.bat` / `.cmd` | Batch scripts | `.cmd` handles `ERRORLEVEL` slightly differently |
| `.ps1` | PowerShell script | Blocked by default execution policy on clients |
| `.psm1` / `.psd1` | PowerShell module / manifest | |
| `.vbs` / `.vbe` / `.wsf` / `.js` | Windows Script Host scripts | Run by `wscript` / `cscript`; VBScript is deprecated |
| `.hta` | HTML Application | Run by `mshta.exe`; heavily abused by malware |
| `.reg` | Registry import file | Double-click merges it; never import unknown ones |
| `.lnk` | Shell shortcut | Can carry arguments; used in phishing |
| `.url` | Internet shortcut | |
| `.library-ms` / `.search-ms` | Library / saved search | Can be abused to open remote paths |
| `.rdp` | Remote Desktop connection file | Stores target, resolution, redirection settings |
| `.appref-ms` | ClickOnce shortcut | |

#### Configuration, Policy and Databases

| Extension | What It Is | Notes |
|---|---|---|
| `.ini` | Legacy configuration | `desktop.ini` customizes folder appearance |
| `.config` | .NET configuration | `web.config`, `app.exe.config`, `machine.config` |
| `.pol` | Group Policy registry policy | `Registry.pol` in SYSVOL / local policy folder |
| `.admx` / `.adml` | Group Policy templates / language | `C:\Windows\PolicyDefinitions` or the AD Central Store |
| `.sdb` | App compatibility shim database | Managed with Compatibility Administrator |
| `.dit` | Active Directory database | `C:\Windows\NTDS\ntds.dit` on domain controllers |
| `.edb` | Extensible Storage Engine DB | `Windows.edb` is the Search index |
| `.dat` | Registry hive or generic data | `NTUSER.DAT`, `UsrClass.dat` |
| `.LOG1` / `.LOG2` / `.blf` / `.regtrans-ms` | Registry transaction logs | Next to hive files |
| (no extension) | Registry hives | `SAM`, `SYSTEM`, `SOFTWARE`, `SECURITY`, `DEFAULT`; BCD store is `BCD` |

#### Logs, Dumps and Diagnostics

| Extension | What It Is | Notes |
|---|---|---|
| `.evtx` | Event Viewer log | Legacy `.evt`; export with `wevtutil epl` |
| `.etl` | Event Trace Log | WPR, `netsh trace`, `pktmon`, Windows Update traces |
| `.dmp` / `.mdmp` / `.hdmp` | Memory / crash dump | `C:\Windows\Minidump`, `MEMORY.DMP`, ProcDump |
| `.wer` | Windows Error Reporting report | `C:\ProgramData\Microsoft\Windows\WER` |
| `.pf` | Prefetch file | Evidence that a program ran (forensics) |
| `.blg` | Performance Monitor log | Binary counter log |
| `.pml` | Process Monitor log | Sysinternals capture |

#### Certificates and Security

| Extension | What It Is | Notes |
|---|---|---|
| `.cer` / `.crt` | Public certificate | DER or Base64 encoded |
| `.pfx` / `.p12` | Certificate **with private key** | Password protected; treat as a secret |
| `.p7b` | Certificate chain (no private key) | |
| `.csr` | Certificate signing request | |
| `.crl` | Certificate revocation list | |

#### Printing, Help and UI

| Extension | What It Is | Notes |
|---|---|---|
| `.spl` / `.shd` | Print spool data / shadow file | `spool\PRINTERS`; delete when clearing a stuck queue |
| `.xps` / `.oxps` | XML Paper Specification | Windows-native print format |
| `.chm` | Compiled HTML Help | Right-click > Properties > Unblock if it will not display |
| `.ttf` / `.otf` / `.ttc` / `.fon` | Fonts | `C:\Windows\Fonts` |
| `.ico` / `.cur` / `.ani` | Icons and cursors | |
| `.theme` | Desktop theme | |

#### Developer Artifacts

| Extension | What It Is |
|---|---|
| `.sln`, `.csproj`, `.vcxproj` | Visual Studio solution and projects |
| `.pdb` | Program database (debug symbols); WinDbg needs it for readable stacks |
| `.lib` / `.obj` / `.exp` | Import libraries and compiler output |
| `.nupkg` | NuGet package |
| `.resx` / `.xaml` | .NET resources and UI markup |
| `.snk` | Strong name key |

#### Risky Extensions to Block or Inspect

```text
.exe .scr .com .pif .bat .cmd .vbs .vbe .js .jse .wsf .hta .ps1 .lnk .cpl .msi .msp .reg .dll .jar .iso .img .vhd .chm
```

- Double extensions: `invoice.pdf.exe` (Explorer hides the real one unless extensions are shown).
- Right-to-left override characters can make `exe` look like `fdp.` in a name.
- Archives (`.zip`, `.iso`) may or may not carry the **Mark of the Web**; check with `dir /r` and `Get-Item file -Stream *`.
- Block at the email gateway and with AppLocker or WDAC, not only on user training.

#### Useful File Type Commands

```cmd
assoc .ps1                                   :: extension to file type
ftype Microsoft.PowerShellScript.1           :: file type to open command
dism /Online /Export-DefaultAppAssociations:C:\apps.xml
dism /Online /Import-DefaultAppAssociations:C:\apps.xml
regsvr32 /s mylib.dll                        :: register a COM DLL silently
regsvr32 /u mylib.dll                        :: unregister
rundll32 user32.dll,LockWorkStation          :: call a DLL export
rundll32 shell32.dll,Control_RunDLL desk.cpl
tasklist /m kernel32.dll                     :: processes that loaded a DLL
tasklist /m /fi "IMAGENAME eq chrome.exe"    :: DLLs loaded by a process
```

```powershell
Get-AuthenticodeSignature C:\Windows\System32\notepad.exe | Select-Object Status, SignerCertificate
(Get-Item .\app.exe).VersionInfo | Format-List *
```

Tools for inspecting binaries: Sysinternals `sigcheck -a file.exe` (signature, hashes, version) and `listdlls`, the open-source **Dependencies** tool (modern Dependency Walker), and `dumpbin /dependents file.exe` (Visual Studio Developer Prompt).

#### DLL Essentials

A **DLL** is code shared between programs. Most "missing DLL" errors mean a **runtime is missing**, not that the single named file is broken.

| Error | Meaning | Typical Fix |
|---|---|---|
| Win32 `126` "The specified module could not be found" | The DLL, **or one of its dependencies**, was not found | Check the PATH and install folder; inspect with Dependencies or Process Monitor (`NAME NOT FOUND`) |
| Win32 `127` "The specified procedure could not be found" | Wrong DLL version (the function does not exist in it) | Install the correct app or runtime version; look for an old copy earlier in the search order |
| Win32 `193` "not a valid Win32 application" | 32-bit and 64-bit mismatch, or a corrupt file | Use the matching architecture |
| Win32 `14001` "side-by-side configuration is incorrect" | Missing or bad manifest or runtime | `sxstrace trace -logfile:C:\sxs.etl`, then `sxstrace parse -logfile:C:\sxs.etl -outfile:C:\sxs.txt`; install the VC++ runtime |
| `0xC000007B` | Bad image (architecture mix) | Install both x86 and x64 runtimes |
| `0xC0000142` | DLL initialization failed | Reinstall app, check antivirus, check dependent DLLs |

| Missing File | Install |
|---|---|
| `VCRUNTIME140.dll`, `MSVCP140.dll`, `vcruntime140_1.dll` | Visual C++ Redistributable 2015-2022 (the x64 **and** x86 packages) |
| `MSVCR100.dll` / `MSVCP100.dll` | Visual C++ 2010 Redistributable |
| `MSVCR120.dll` | Visual C++ 2013 Redistributable |
| `api-ms-win-crt-*.dll` | Universal CRT (included with the VC++ 2015-2022 redistributable and current Windows) |
| `d3dx9_43.dll`, `xinput1_3.dll` | DirectX End-User Runtime (legacy games and tools) |
| `mscoree.dll` / .NET errors | Required .NET Framework or .NET runtime |

```cmd
:: .NET Framework 4.x release key (528040 or higher = 4.8 or later)
reg query "HKLM\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release
:: Modern .NET runtimes install side by side
dotnet --list-runtimes
dotnet --list-sdks
:: Legacy .NET 3.5 (includes 2.0 and 3.0) is a Windows feature
DISM /Online /Enable-Feature /FeatureName:NetFx3 /All
```

**DLL search order (simplified, for unpackaged desktop apps):** loaded modules and Known DLLs, then the **application's directory**, then `System32`, then the Windows directory, then the current directory, then `PATH`. The KnownDLLs list is under `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs`.

> **Security: DLL hijacking.** An attacker (or a buggy installer) drops a malicious DLL with the expected name where the app looks first. Writable folders in `PATH` and apps installed in user-writable folders are classic risks. Use Process Monitor with `Result is NAME NOT FOUND` and `Path ends with .dll` to see what an app searches for.

| Concept | Meaning |
|---|---|
| DLL Hell | Several apps need different versions of the same DLL; solved by side-by-side assemblies (WinSxS) and private copies |
| Side-by-side (SxS) | Manifest-driven loading of the exact required version from `C:\Windows\WinSxS` |
| `System32` vs `SysWOW64` | 64-bit vs 32-bit system DLLs (see WOW64 below) |
| Do not copy DLLs from random sites | Install the official runtime instead; downloaded DLLs are frequently malware |

### Line Endings and Encoding

| Item | Windows | Linux/macOS |
|---|---|---|
| Line ending | `CRLF` (`\r\n`) | `LF` (`\n`) |
| Default text encoding (modern Notepad) | UTF-8 | UTF-8 |
| Legacy console code page | OEM (e.g. 437), changeable with `chcp 65001` (UTF-8) | UTF-8 |

> A shell script saved with CRLF fails on Linux with `bad interpreter: /bin/bash^M`. Configure Git (`core.autocrlf`, `.gitattributes`) and the editor to use LF for scripts.

### Environment Variables

```cmd
set                       :: list all variables
set USERNAME              :: variables beginning with USERNAME
echo %USERPROFILE%        :: use a variable in CMD
set MYVAR=hello           :: current CMD session only
setx MYVAR "hello"        :: persistent for the user (new processes only)
setx MYVAR "hello" /M     :: persistent for the machine (admin)
```

```powershell
$env:MYVAR = "hello"                          # current PowerShell session
$env:Path -split ';'                          # PATH as a list
[Environment]::SetEnvironmentVariable("MYVAR","hello","User")   # persistent
```

| Variable | Typical Value |
|---|---|
| `%SystemRoot%` / `%WINDIR%` | `C:\Windows` |
| `%ProgramFiles%` | `C:\Program Files` |
| `%ProgramFiles(x86)%` | `C:\Program Files (x86)` |
| `%ProgramData%` | `C:\ProgramData` |
| `%USERPROFILE%` | `C:\Users\<name>` |
| `%APPDATA%` | `C:\Users\<name>\AppData\Roaming` |
| `%LOCALAPPDATA%` | `C:\Users\<name>\AppData\Local` |
| `%TEMP%` / `%TMP%` | `C:\Users\<name>\AppData\Local\Temp` |
| `%COMPUTERNAME%` | Machine name |
| `%USERNAME%` | Current user |
| `%COMSPEC%` | `C:\Windows\System32\cmd.exe` |
| `%ERRORLEVEL%` | Exit code of last command |
| `%PATH%` | Executable search path (semicolon separated) |

> `setx` does not change the current window; open a new terminal. `setx` also truncates values over 1024 characters, so edit long `PATH` values through **System Properties > Environment Variables** (`sysdm.cpl`).

### Windows Directory Structure

```text
C:\
├── Windows\                 Operating system files
│   ├── System32\            64-bit system binaries, DLLs, drivers, config
│   │   ├── config\          Registry hive files (SAM, SYSTEM, SOFTWARE, SECURITY)
│   │   ├── drivers\         Device drivers; drivers\etc\hosts
│   │   └── winevt\Logs\     Event logs (.evtx)
│   ├── SysWOW64\            32-bit binaries on 64-bit Windows (confusing name)
│   ├── WinSxS\              Component store (side-by-side assemblies, servicing)
│   ├── SoftwareDistribution\  Windows Update download cache
│   ├── Temp\                System temporary files
│   ├── Logs\                CBS, DISM, Windows Update logs
│   ├── Minidump\            Crash dumps after a BSOD
│   └── Prefetch\            Application launch optimization data
├── Program Files\           64-bit applications
├── Program Files (x86)\     32-bit applications
├── ProgramData\             Shared application data for all users (hidden)
├── Users\                   User profiles
│   ├── Public\              Shared files
│   ├── Default\             Template for new profiles
│   └── <username>\          Desktop, Documents, Downloads, AppData, NTUSER.DAT
├── pagefile.sys             Virtual memory (hidden)
├── hiberfil.sys             Hibernation / Fast Startup data (hidden)
├── swapfile.sys             Modern app swap (hidden)
├── $Recycle.Bin\            Recycle Bin (hidden)
├── System Volume Information\   Restore points / shadow copies (hidden)
└── Recovery\                Windows Recovery Environment data
```

> The 64-bit binaries live in `System32` and 32-bit binaries in `SysWOW64`. The names are historical and counterintuitive.

#### User Profile Folders

| Path | Purpose |
|---|---|
| `AppData\Roaming` | Settings that follow the user across domain machines |
| `AppData\Local` | Machine-specific data, caches |
| `AppData\LocalLow` | Low-integrity (sandboxed) application data |
| `NTUSER.DAT` | User registry hive (`HKEY_CURRENT_USER`) |
| `Desktop`, `Documents`, `Downloads` | Known folders (may be redirected to OneDrive) |

```cmd
explorer shell:startup           :: user Startup folder
explorer shell:common startup    :: all-users Startup folder
explorer shell:appdata           :: Roaming AppData
explorer shell:sendto            :: Send To menu items
```

### WOW64: 32-bit Apps on 64-bit Windows

32-bit processes run in the **WOW64** compatibility layer. Windows silently redirects their view of files and the registry.

| Item | 64-bit Process Sees | 32-bit Process Sees |
|---|---|---|
| `C:\Windows\System32` | The real 64-bit folder | Redirected to `C:\Windows\SysWOW64` |
| `C:\Program Files` | 64-bit apps | Redirected to `C:\Program Files (x86)` |
| `HKLM\SOFTWARE\Vendor` | Native key | Redirected to `HKLM\SOFTWARE\WOW6432Node\Vendor` |

```cmd
:: A 32-bit process (old installer, 32-bit cmd, some build tools) reaches the REAL System32 through:
C:\Windows\Sysnative\
:: Run the 64-bit tool explicitly from a 32-bit shell
%windir%\Sysnative\cmd.exe
%windir%\Sysnative\WindowsPowerShell\v1.0\powershell.exe
:: Query the 32-bit and 64-bit registry views explicitly
reg query "HKLM\SOFTWARE\Vendor" /reg:64
reg query "HKLM\SOFTWARE\Vendor" /reg:32
```

**Symptom:** a script works in one shell but not another, or an installer "cannot find" a file that clearly exists in `System32`. The cause is usually a 32-bit host process. `Sysnative` only exists for 32-bit processes; it does not appear in Explorer.

### Settings, Control Panel and MMC Consoles

Windows keeps two management UIs: the modern **Settings** app and the legacy **Control Panel** plus **MMC snap-ins** (`.msc`). IT work still relies heavily on the legacy tools.

| Command | Opens |
|---|---|
| `control` | Control Panel |
| `ms-settings:` | Settings app (e.g. `ms-settings:windowsupdate`, `ms-settings:network`, `ms-settings:about`) |
| `ncpa.cpl` | Network Connections (adapters) |
| `appwiz.cpl` | Programs and Features (uninstall) |
| `sysdm.cpl` | System Properties (computer name, environment variables, performance, startup/recovery) |
| `netplwiz` / `control userpasswords2` | User accounts, auto-logon |
| `powercfg.cpl` | Power options |
| `firewall.cpl` | Windows Defender Firewall |
| `timedate.cpl` | Date and time |
| `inetcpl.cpl` | Internet options (proxy, certificates) |
| `compmgmt.msc` | Computer Management (Event Viewer, Shared Folders, Users, Disk Mgmt, Services) |
| `devmgmt.msc` | Device Manager |
| `diskmgmt.msc` | Disk Management |
| `services.msc` | Services |
| `eventvwr.msc` | Event Viewer |
| `taskschd.msc` | Task Scheduler |
| `gpedit.msc` | Local Group Policy Editor (Pro and above) |
| `lusrmgr.msc` | Local Users and Groups (Pro and above) |
| `secpol.msc` | Local Security Policy |
| `wf.msc` | Firewall with Advanced Security |
| `certmgr.msc` / `certlm.msc` | Certificates (user / machine) |
| `fsmgmt.msc` | Shared Folders |
| `perfmon` / `resmon` | Performance Monitor / Resource Monitor |
| `msconfig` | System Configuration (boot options, startup services) |
| `msinfo32` | System Information |
| `dxdiag` | DirectX and graphics diagnostics |
| `mdsched` | Windows Memory Diagnostic |
| `cleanmgr` | Disk Cleanup |
| `regedit` | Registry Editor |
| `taskmgr` | Task Manager |
| `mstsc` | Remote Desktop Connection |

### Run Dialog and Keyboard Shortcuts

Press `Win+R` to open **Run**, which launches any command, folder, or URL.

| Shortcut | Action |
|---|---|
| `Win+R` | Run dialog |
| `Win+X` | Power User menu (Terminal, Device Manager, Disk Management, Event Viewer) |
| `Win+E` | File Explorer |
| `Win+I` | Settings |
| `Win+L` | Lock workstation |
| `Win+D` | Show desktop |
| `Win+V` | Clipboard history |
| `Win+Shift+S` | Screenshot / snipping |
| `Win+Tab` | Task view and virtual desktops |
| `Win+Ctrl+D` | New virtual desktop |
| `Win+Arrow keys` | Snap windows |
| `Win+P` | Projection / display mode |
| `Win+Pause` | System properties |
| `Win+Ctrl+Shift+B` | Restart the graphics driver (fixes black screen) |
| `Ctrl+Shift+Esc` | Task Manager |
| `Ctrl+Alt+Del` | Secure Attention Sequence (lock, sign out, Task Manager) |
| `Alt+Tab` | Switch windows |
| `Alt+F4` | Close window / shutdown dialog on desktop |
| `F2` | Rename |
| `Shift+Delete` | Delete permanently, bypassing Recycle Bin |
| `Shift+F10` | Classic context menu (Windows 11) |
| `Shift` + click Restart | Boot into Windows Recovery Environment |

### Windows Licensing and Activation

| License Type | Description |
|---|---|
| OEM | Tied to the hardware it shipped with |
| Retail | Purchased separately; transferable |
| Volume (KMS / MAK) | Organization licensing; KMS clients renew against a KMS host every 180 days |
| Digital license | Linked to hardware or Microsoft account, no key typed |

```cmd
slmgr /dli          :: short license info
slmgr /dlv          :: detailed license info
slmgr /xpr          :: activation expiry
slmgr /ato          :: attempt activation
```

> `wmic` (Windows Management Instrumentation Command-line) is deprecated and not installed by default on recent Windows 11 releases. Prefer `Get-CimInstance` for hardware and OS queries.

---

## Shell and Terminal Basics

### Terminal, Console and Shell

| Component | Role |
|---|---|
| Windows Terminal (`wt`) | Modern tabbed terminal application hosting any shell; default on current Windows 11 |
| Console host (`conhost.exe`) | Legacy console window |
| `cmd.exe` | Command Prompt, the legacy shell (batch syntax) |
| Windows PowerShell 5.1 (`powershell.exe`) | Built into Windows, .NET Framework based, legacy but always present |
| PowerShell 7+ (`pwsh.exe`) | Cross-platform, modern, installed separately |
| WSL | Real Linux shells (bash, zsh) running inside Windows |

```cmd
winget install Microsoft.WindowsTerminal
winget install Microsoft.PowerShell
```

| Windows Terminal Shortcut | Action |
|---|---|
| `Ctrl+Shift+T` | New tab |
| `Ctrl+Shift+W` | Close pane/tab |
| `Alt+Shift+D` | Duplicate pane (split) |
| `Ctrl+Shift+P` | Command palette |
| `Ctrl+Shift+C` / `Ctrl+Shift+V` | Copy / Paste |

### CMD versus PowerShell

| Feature | CMD | PowerShell |
|---|---|---|
| Output | Plain text | .NET objects |
| Command style | `dir`, `copy`, `ipconfig` | `Verb-Noun` cmdlets (`Get-ChildItem`) |
| Options | `/switch` | `-Parameter` |
| Variables | `%VAR%` | `$env:VAR`, `$var` |
| Pipeline | Text streams | Objects, filtered by property |
| Registry/Certs | Via `reg`, `certutil` | Navigable drives (`HKLM:`, `Cert:`) |
| Remote | Limited | Built-in remoting (WinRM, SSH) |

Use CMD for legacy utilities and quick one-liners. Use PowerShell when output must be filtered, sorted, or exported.

### Prompt Identification

```text
C:\Users\hitesh>              CMD prompt (check title bar: "Administrator:" means elevated)
PS C:\Users\hitesh>           PowerShell prompt
```

> Elevated windows show **Administrator** in the title bar. Windows has no `#` prompt indicator; always check the title before running destructive commands.

### Navigating the File System

```cmd
cd                       :: show current directory
cd C:\Windows            :: absolute path
cd ..                    :: parent directory
cd \                     :: root of the current drive
cd /d D:\Projects        :: change drive AND directory
D:                       :: switch to drive D: (keeps its last directory)
pushd C:\Windows         :: save current dir, go there (UNC paths get a temp drive letter)
popd                     :: return to saved dir
cls                      :: clear the screen
exit                     :: close the session
whoami                   :: current user
hostname                 :: computer name
```

```powershell
Get-Location             # pwd
Set-Location C:\Windows  # cd
Push-Location / Pop-Location
Clear-Host               # cls
```

> In CMD, `cd D:\Projects` from drive `C:` changes the remembered directory of `D:` but does not switch to it. Use `cd /d`.

### Chaining Commands

```cmd
cmd1 & cmd2          :: run cmd2 after cmd1 regardless of result
cmd1 && cmd2         :: run cmd2 only if cmd1 succeeded (errorlevel 0)
cmd1 || cmd2         :: run cmd2 only if cmd1 failed
mkdir backup && cd backup
ping -n 1 google.com || echo No internet
(cmd1 & cmd2) > out.txt    :: group commands, redirect combined output
```

PowerShell 7 supports `&&` and `||`. Windows PowerShell 5.1 does not; use `;` to run sequentially.

```cmd
echo %ERRORLEVEL%    :: exit code of last command (0 = success)
```

### Redirection and Pipes

| Symbol | Meaning |
|---|---|
| `>` | Redirect stdout to a file (overwrite) |
| `>>` | Append stdout to a file |
| `2>` | Redirect stderr |
| `2>&1` | Send stderr to the same place as stdout |
| `<` | Read stdin from a file |
| `\|` | Pipe stdout to another command |
| `nul` | The null device (Linux `/dev/null`) |

```cmd
dir > list.txt
dir >> list.txt
dir C:\nonexistent 2> errors.txt
command > nul 2>&1               :: silence everything
ipconfig /all | findstr /i "IPv4"
type log.txt | more
```

> In **Windows PowerShell 5.1**, `>` writes **UTF-16** by default, which surprises Linux tools. PowerShell 7 writes UTF-8 without BOM. Use `| Out-File -Encoding utf8` when needed.

### Escaping and Special Characters in CMD

| Character | Usage |
|---|---|
| `^` | Escape character (`echo a ^& b`) |
| `%%` | Literal percent inside batch files |
| `"` | Quote paths with spaces: `"C:\Program Files"` |
| `&`, `\|`, `<`, `>` | Operators; escape with `^` to print literally |

### Getting Help

```cmd
help                     :: list internal CMD commands
help copy                :: help for a CMD command
robocopy /?              :: help for external tools (almost all support /?)
```

```powershell
Get-Help Get-Process -Examples     # examples first
Get-Help Get-Process -Online       # open docs in browser
Update-Help                        # download help files (admin)
Get-Command *service*              # discover commands by name
Get-Command -Module NetTCPIP       # commands in a module
Get-Alias                          # list aliases
Get-Process | Get-Member           # show properties and methods of the objects
$PSVersionTable                    # PowerShell version
```

### PowerShell Fundamentals

PowerShell commands are **cmdlets** named `Verb-Noun`. Pipelines pass **objects**, not text.

```powershell
Get-Process | Where-Object CPU -gt 100 | Sort-Object CPU -Descending | Select-Object -First 5 Name, CPU, Id
Get-Service | Where-Object Status -eq 'Running' | Format-Table -AutoSize
Get-ChildItem -Recurse -File | Measure-Object Length -Sum
Get-Process | Export-Csv processes.csv -NoTypeInformation
Get-Service | ConvertTo-Json
```

| Common Verb | Meaning |
|---|---|
| `Get` | Read information |
| `Set` | Change a setting |
| `New` | Create something |
| `Remove` | Delete |
| `Start` / `Stop` / `Restart` | Control processes and services |
| `Test` | Check a condition (`Test-Path`, `Test-NetConnection`) |
| `Select` / `Where` / `Sort` / `Group` / `Measure` | Pipeline shaping |
| `Out` / `Export` / `ConvertTo` | Output |

Built-in aliases for Linux users: `ls`, `cd`, `cat`, `cp`, `mv`, `rm`, `ps`, `kill`, `echo`, `pwd`, `man`, `clear`. They map to cmdlets, not to GNU tools, so flags like `ls -la` do not work.

#### PowerShell Providers

Providers expose data stores as drives.

```powershell
Get-PSDrive                          # FileSystem, Registry, Env, Cert, Function, Variable
Set-Location HKLM:\SOFTWARE          # navigate the registry like folders
Get-ChildItem Env:                   # environment variables
Get-ChildItem Cert:\LocalMachine\My  # machine certificates
```

#### Execution Policy

Windows client editions block running `.ps1` files by default (policy `Restricted`). Typing commands interactively is not affected.

```powershell
Get-ExecutionPolicy -List
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

> Execution policy is a safety feature, not a security boundary.

### Running as Administrator and UAC

**User Account Control (UAC)** runs even administrators with a **standard-user token** until they approve elevation.

```cmd
:: Open elevated terminal: Start > type "cmd" > Ctrl+Shift+Enter, or right-click > Run as administrator
runas /user:Administrator cmd                 :: run as another user
net session >nul 2>&1 && echo Elevated || echo Not elevated
```

```powershell
Start-Process powershell -Verb RunAs          # open elevated PowerShell
sudo ipconfig /all                            # "Sudo for Windows" if enabled in Settings > System > For developers
```

### Command History and Editing

```cmd
doskey /history            :: show CMD history
doskey ll=dir /a $*        :: define a macro (session only, like alias)
```

| Key | Action |
|---|---|
| `Up` / `Down` | Previous / next command |
| `F7` | History popup list |
| `F3` | Repeat last command |
| `Tab` | Auto-complete file/folder names |
| `Ctrl+C` | Stop the running command |
| `Ctrl+Z` then Enter | EOF in CMD |
| `Ctrl+R` | Reverse history search (PowerShell with PSReadLine) |

```powershell
Get-History
(Get-PSReadLineOption).HistorySavePath      # persistent history file path
```

### shutdown Command

```cmd
shutdown /s /t 0          :: shut down immediately
shutdown /r /t 0          :: restart immediately
shutdown /r /o /t 0       :: restart into Advanced Startup options (recovery)
shutdown /l               :: log off
shutdown /h               :: hibernate
shutdown /a               :: abort a pending shutdown
shutdown /r /t 300 /c "Updates will restart this PC in 5 minutes"
shutdown /r /m \\PC01 /t 0     :: restart a remote computer (needs rights)
```

---

## File and Directory Commands

### Command Equivalents

| Task | CMD | PowerShell |
|---|---|---|
| List files | `dir` | `Get-ChildItem` (`ls`, `dir`, `gci`) |
| Copy | `copy`, `xcopy`, `robocopy` | `Copy-Item` (`cp`) |
| Move / rename | `move`, `ren` | `Move-Item` (`mv`), `Rename-Item` |
| Delete file | `del` | `Remove-Item` (`rm`) |
| Make directory | `mkdir` / `md` | `New-Item -ItemType Directory` |
| Remove directory | `rmdir` / `rd` | `Remove-Item -Recurse` |
| Show contents | `type` | `Get-Content` (`cat`) |
| Create empty file | `type nul > file.txt` | `New-Item file.txt` |
| Test existence | `if exist file` | `Test-Path file` |
| Full path | `cd` | `Resolve-Path` |
| Tree view | `tree` | — |

### dir — List Files and Directories

```cmd
dir                          :: current directory
dir /a                       :: include hidden and system files
dir /ah                      :: hidden only
dir /s                       :: recursive
dir /b                       :: bare names (good for piping)
dir /s /b *.log              :: find all .log files recursively (like find -name)
dir /o:-d                    :: sort by date, newest first
dir /o:-s                    :: sort by size, largest first
dir /q                       :: show file owner
dir /t:a                     :: show last access time
dir C:\Users /ad             :: directories only
```

```powershell
Get-ChildItem -Force                          # include hidden
Get-ChildItem -Recurse -Filter *.log          # recursive search by name
Get-ChildItem -File -Recurse | Sort-Object Length -Descending | Select-Object -First 10 FullName, Length
Get-ChildItem | Where-Object LastWriteTime -gt (Get-Date).AddDays(-1)
```

### tree — Visualize Structure

```cmd
tree                    :: directories only
tree /f                 :: include files
tree /f /a              :: ASCII characters (safe for pasting into docs)
```

### mkdir, rmdir, and Deleting

```cmd
mkdir a\b\c                 :: creates intermediate directories automatically
rmdir folder                :: only removes an empty folder
rmdir /s /q folder          :: recursive, no prompt (permanent, no Recycle Bin)
del file.txt
del /f /q *.tmp             :: force, quiet
del /s /q C:\temp\*.log     :: recursive delete of matching files
```

```powershell
New-Item -ItemType Directory -Path C:\Projects\app -Force
Remove-Item C:\temp\old -Recurse -Force
Remove-Item *.tmp -WhatIf          # dry run: shows what would be deleted
```

> `rmdir /s /q` and `Remove-Item -Recurse -Force` are the Windows equivalents of `rm -rf`. They bypass the Recycle Bin.

### copy, xcopy, and robocopy

```cmd
copy file.txt backup.txt
copy /y file.txt D:\              :: overwrite without prompt
copy a.txt+b.txt combined.txt     :: concatenate text files
copy /b part1+part2 full.bin      :: binary concatenate
xcopy src dst /E /I /H /Y         :: recursive, assume dir, hidden files, no prompt
```

#### robocopy — Robust File Copy (preferred)

```cmd
robocopy C:\data D:\backup /E /R:3 /W:5 /MT:16 /LOG:C:\logs\copy.log /TEE
```

| Option | Meaning |
|---|---|
| `/E` | Copy subdirectories including empty ones |
| `/MIR` | Mirror: copy and **delete** destination files that no longer exist in source |
| `/Z` | Restartable mode for network copies |
| `/B` | Backup mode (override permissions if running with backup rights) |
| `/COPYALL` | Copy data, attributes, timestamps, ACLs, owner, auditing info |
| `/DCOPY:DAT` | Preserve directory timestamps |
| `/R:n` `/W:n` | Retry count and wait seconds (default is 1,000,000 retries, always set it) |
| `/MT:n` | Multi-threaded copy |
| `/XD dir` `/XF file` | Exclude directories / files |
| `/MOVE` | Move instead of copy |
| `/L` | List only (dry run) |
| `/NP` | No progress percentage in log |
| `/LOG:file` `/TEE` | Write log and also show on console |

```cmd
robocopy src dst /MIR /L              :: dry run first before any /MIR
```

| Robocopy Exit Code | Meaning |
|---|---|
| `0` | Nothing copied, no changes |
| `1` | Files copied successfully |
| `2` | Extra files in destination detected |
| `3` | Combination of 1 and 2 |
| `4` | Mismatched files or directories |
| `8` or higher | **Failures occurred** |

> Robocopy exit codes below 8 indicate success of some form. Do not treat non-zero as failure in automation.

```powershell
Copy-Item .\app C:\backup\app -Recurse
Copy-Item .\file.txt \\server\share\ -Force
```

### move, ren, attrib

```cmd
move old.txt new.txt                :: rename
move *.txt D:\archive\
ren oldname.txt newname.txt
attrib file.txt                     :: show attributes
attrib +h +s secret.txt             :: hidden + system
attrib -h -s /s /d C:\folder        :: unhide recursively
attrib +r file.txt                  :: read-only
```

| Attribute | Meaning |
|---|---|
| `R` | Read-only |
| `H` | Hidden |
| `S` | System |
| `A` | Archive (needs backup) |
| `I` | Not content indexed |

### type, more, and Viewing Files

```cmd
type file.txt                  :: print file
type file.txt | more           :: page through
more file.txt
type file1.txt file2.txt > merged.txt
```

```powershell
Get-Content file.txt
Get-Content file.txt -TotalCount 10    # head -n 10
Get-Content file.txt -Tail 20          # tail -n 20
Get-Content app.log -Wait -Tail 20     # tail -f
```

### Comparing Files

```cmd
fc file1.txt file2.txt              :: compare text files
fc /b a.bin b.bin                   :: binary compare
comp a.bin b.bin
certutil -hashfile file.iso SHA256  :: checksum (MD5, SHA1, SHA256, SHA512)
```

```powershell
Compare-Object (Get-Content a.txt) (Get-Content b.txt)
Get-FileHash file.iso -Algorithm SHA256
```

### where — Locate Executables

```cmd
where python
where /r C:\ notepad.exe       :: recursive search from C:\
```

> In PowerShell, `where` is an **alias of `Where-Object`**. Use `where.exe python` or `Get-Command python`.

### Links: Symbolic Links, Hard Links and Junctions

```cmd
mklink link.txt C:\data\real.txt            :: file symbolic link
mklink /D linkdir C:\data\realdir           :: directory symbolic link
mklink /H hard.txt C:\data\real.txt         :: hard link (same NTFS volume)
mklink /J junction C:\data\realdir          :: directory junction (local paths only)
dir /AL                                      :: list reparse points (links)
```

```powershell
New-Item -ItemType SymbolicLink -Path link.txt -Target C:\data\real.txt
(Get-Item link.txt).Target
```

| Link Type | Works On | Across Volumes | Requires Admin |
|---|---|---|---|
| Symbolic link | Files and folders | Yes, including network paths | Yes, unless Developer Mode is on |
| Hard link | Files only | No (same volume) | No |
| Junction | Folders only | Yes (local volumes only) | No |

### File Explorer Essentials

```cmd
explorer .                                 :: open current folder
explorer /select,"C:\Windows\notepad.exe"  :: open and highlight a file
taskkill /f /im explorer.exe & start explorer.exe   :: restart the shell (fixes frozen taskbar)
```

| Feature | Details |
|---|---|
| Quick Access / Home | Pinned and recent locations |
| Hidden items | View > Show > Hidden items |
| Properties > Security | NTFS permissions |
| Previous Versions | Restore from shadow copies or File History |
| `\\?\` prefix | Bypass `MAX_PATH` in some tools |
| `Shift+Right-click` | Extra context options such as "Open PowerShell window here" |

### Alternate Data Streams

NTFS files can hold hidden named streams, used for example by the "Mark of the Web" (`Zone.Identifier`) that flags downloaded files.

```cmd
dir /r                                  :: show streams
more < file.exe:Zone.Identifier         :: view the Zone.Identifier stream
```

```powershell
Get-Item file.exe -Stream *
Unblock-File file.exe                   # remove the downloaded-from-internet flag
```

### Compression and Encryption on NTFS

```cmd
compact /c /s:C:\logs           :: compress a folder
compact /u /s:C:\logs           :: uncompress
cipher /e /s:C:\secret          :: encrypt with EFS
cipher /w:C:\                   :: overwrite free disk space (wipe deleted data)
```

---

## Permissions and Ownership

### NTFS Permission Model

Windows uses **Access Control Lists (ACLs)**. Every file or folder has a **security descriptor** with an owner and a **DACL** containing **Access Control Entries (ACEs)** naming a user or group (by **SID**) and the rights allowed or denied.

| Permission | Allows |
|---|---|
| Full Control | Everything, including changing permissions and taking ownership |
| Modify | Read, write, delete, execute |
| Read and Execute | View and run |
| List Folder Contents | List a folder (folders only) |
| Read | View contents and attributes |
| Write | Create files, write data |
| Special permissions | Fine-grained rights (take ownership, change permissions, delete subfolders) |

Rules:

- **Explicit Deny** overrides Allow (but **inherited Deny** is overridden by explicit Allow).
- Permissions are **cumulative** across groups a user belongs to.
- Permissions are **inherited** from the parent folder by default.
- The **owner** can always change permissions.
- Effective access over the network is the **more restrictive** of Share permissions and NTFS permissions.

### Compared with Linux Permissions

| Linux | Windows |
|---|---|
| `rwx` for user/group/other | ACL with many ACEs, many rights |
| `chmod` | `icacls` |
| `chown` | `takeown`, `icacls /setowner` |
| `umask` | Inheritance from parent |
| `sudo` | UAC elevation, `runas` |
| Root bypasses everything | `SYSTEM` and Administrators (with `SeTakeOwnershipPrivilege`, `SeBackupPrivilege`) |
| Execute bit | File extension and ACL Execute right |

### icacls — View and Modify ACLs

```cmd
icacls C:\data                                  :: show ACL
icacls C:\data /grant Users:(OI)(CI)RX          :: grant read+execute, inherit to files and folders
icacls C:\data /grant "DOMAIN\jsmith":(OI)(CI)M :: grant modify
icacls C:\data /deny Guest:(OI)(CI)F
icacls C:\data /remove Users                    :: remove all ACEs for Users
icacls C:\data /inheritance:d                   :: disable inheritance, copy inherited ACEs
icacls C:\data /inheritance:r                   :: remove inherited ACEs
icacls C:\data /reset /T /C                     :: reset to inherited defaults recursively
icacls C:\data /setowner Administrators /T /C
icacls C:\data /save acl.txt /T                 :: back up ACLs
icacls C:\ /restore acl.txt                     :: restore ACLs
```

| Right Code | Meaning |
|---|---|
| `F` | Full control |
| `M` | Modify |
| `RX` | Read and execute |
| `R` | Read |
| `W` | Write |
| `D` | Delete |

| Inheritance Flag | Meaning |
|---|---|
| `(OI)` | Object inherit: files inherit |
| `(CI)` | Container inherit: subfolders inherit |
| `(IO)` | Inherit only: do not apply to this folder itself |
| `(NP)` | No propagate: only one level |
| `(I)` | Permission was inherited (shown in output) |

### takeown — Take Ownership

Use when you are locked out of a folder (for example, from a deleted user profile).

```cmd
takeown /f C:\locked /r /d y                    :: take ownership recursively
icacls C:\locked /grant Administrators:F /t     :: then grant yourself access
```

```powershell
Get-Acl C:\data | Format-List
(Get-Acl C:\data).Access | Format-Table IdentityReference, FileSystemRights, AccessControlType, IsInherited
```

### Share Permissions versus NTFS Permissions

| Level | Applies To | Granularity |
|---|---|---|
| Share permissions | Access over the network only | Full Control, Change, Read |
| NTFS permissions | Local and network access | Detailed |

Best practice: set Share permissions to broad (for example `Authenticated Users: Change`) and control access through NTFS.

```cmd
net share                                       :: list shares
net share Data=C:\Data /GRANT:Everyone,READ
net share Data /DELETE
```

```powershell
Get-SmbShare
New-SmbShare -Name Data -Path C:\Data -FullAccess "DOMAIN\Admins" -ReadAccess "DOMAIN\Staff"
```

### UAC, Integrity Levels and Tokens

| Concept | Meaning |
|---|---|
| Access token | Created at logon: user SID, group SIDs, privileges |
| Split token | Administrators get a filtered (standard) token and a full token |
| Integrity levels | Low (sandboxed apps), Medium (standard), High (elevated), System |
| Admin Approval Mode | Prompts when elevation is required |
| Secure desktop | Dims the screen so malware cannot spoof the prompt |

```cmd
whoami /groups          :: shows group membership and the integrity level
whoami /priv            :: privileges in the token
```

---

## Users, Groups and Access

### Account Types

| Type | Description |
|---|---|
| Local account | Stored in the machine's SAM database |
| Microsoft account | Online identity used on consumer devices |
| Domain account | Active Directory account, authenticated by a domain controller |
| Entra ID account | Cloud identity (formerly Azure AD) |
| Built-in `Administrator` | Disabled by default on clients |
| `Guest` | Disabled by default |
| `SYSTEM` | Highest-privilege local identity used by the OS and services |
| `LOCAL SERVICE` / `NETWORK SERVICE` | Low-privilege service identities |

### Built-in Groups

| Group | Purpose |
|---|---|
| Administrators | Full control of the machine |
| Users | Standard users |
| Guests | Limited temporary access |
| Remote Desktop Users | May sign in through RDP |
| Backup Operators | Back up and restore files regardless of permissions |
| Event Log Readers | Read logs without admin |
| Performance Monitor Users | View performance counters |
| Hyper-V Administrators | Manage Hyper-V |
| Network Configuration Operators | Change network settings |

### User Management with net

```cmd
net user                                  :: list local users
net user hitesh                           :: details (last logon, groups, password dates)
net user hitesh P@ssw0rd! /add            :: create (avoid typing real passwords in clear text)
net user hitesh *                         :: change password, prompted securely
net user hitesh /active:no                :: disable
net user hitesh /active:yes               :: enable
net user hitesh /delete
net localgroup                            :: list local groups
net localgroup Administrators hitesh /add
net localgroup Administrators hitesh /delete
net localgroup "Remote Desktop Users" hitesh /add
net accounts                              :: password and lockout policy
net user hitesh /domain                   :: query a domain user
```

```powershell
Get-LocalUser
New-LocalUser -Name "hitesh" -Password (Read-Host -AsSecureString) -FullName "Hitesh"
Disable-LocalUser -Name "hitesh"
Add-LocalGroupMember -Group "Administrators" -Member "hitesh"
Get-LocalGroupMember -Group "Administrators"
```

### Identity Inspection

```cmd
whoami                    :: current user
whoami /user              :: user and SID
whoami /groups            :: groups and integrity level
whoami /priv              :: privileges
whoami /all               :: everything
whoami /fqdn              :: domain distinguished name
query user                :: sessions on this computer (alias: quser)
logoff 2                  :: log off session ID 2
runas /user:DOMAIN\admin "cmd.exe"
net user %USERNAME%
```

### Local Policies

| Tool | Purpose |
|---|---|
| `secpol.msc` | Password policy, account lockout, user rights assignment, audit policy |
| `gpedit.msc` | Local Group Policy (Pro and above) |
| `lusrmgr.msc` | Local Users and Groups (Pro and above) |
| `netplwiz` | Auto sign-in, user list |
| `control keymgr.dll` | Credential Manager |

```cmd
gpupdate /force           :: refresh Group Policy immediately
gpresult /r               :: applied policies summary
gpresult /h report.html   :: full HTML report
net accounts /maxpwage:90 /minpwlen:12
```

### Credential Manager

```cmd
cmdkey /list                                       :: stored credentials
cmdkey /add:server01 /user:DOMAIN\user /pass:***   :: add (avoid real secrets in history)
cmdkey /delete:server01
```

### Sign-In Methods

| Method | Notes |
|---|---|
| Password | Weakest; protect with MFA elsewhere |
| PIN | Tied to the device and TPM |
| Windows Hello (face, fingerprint) | Biometric, backed by TPM |
| Security key (FIDO2) | Phishing-resistant |
| Smart card / certificate | Enterprise |
| Passkeys | Modern passwordless |

---

## Searching and Text Processing

### Tool Equivalents

| Linux | CMD | PowerShell |
|---|---|---|
| `grep` | `findstr`, `find` | `Select-String` |
| `find` | `dir /s /b`, `where /r` | `Get-ChildItem -Recurse` |
| `head` | `more` | `Select-Object -First n` |
| `tail` | — | `Get-Content -Tail n` |
| `tail -f` | — | `Get-Content -Wait` |
| `wc -l` | `find /c /v ""` | `Measure-Object -Line` |
| `sort` | `sort` | `Sort-Object` |
| `uniq` | — | `Sort-Object -Unique`, `Group-Object` |
| `cut` / `awk` | `for /f` | `Select-Object`, `-split` |
| `sed` | — | `-replace` |
| `diff` | `fc` | `Compare-Object` |

### findstr and find

```cmd
findstr "error" app.log                      :: search for a string
findstr /i "error" app.log                   :: case-insensitive
findstr /n /i "error" app.log                :: with line numbers
findstr /s /i /n "TODO" *.cs                 :: recursive in current directory tree
findstr /v "INFO" app.log                    :: lines NOT containing INFO
findstr /c:"connection refused" app.log      :: exact phrase with spaces
findstr /r "^[0-9][0-9]*$" numbers.txt       :: regular expression (limited regex)
findstr /m /s /i "password" *.config         :: only print filenames
find "error" app.log
find /i /c "error" app.log                   :: count matching lines
find /c /v "" file.txt                       :: count all lines (like wc -l)
```

| findstr Flag | Meaning |
|---|---|
| `/i` | Ignore case |
| `/s` | Search subdirectories |
| `/n` | Line numbers |
| `/v` | Invert match |
| `/c:"text"` | Literal phrase |
| `/r` | Regex (simplified engine) |
| `/m` | Filenames only |
| `/b` | Match at beginning of line |
| `/x` | Exact whole-line match |

### Select-String

```powershell
Select-String -Path *.log -Pattern "error"                   # case-insensitive by default
Select-String -Path app.log -Pattern "error" -CaseSensitive
Select-String -Path app.log -Pattern "exception" -Context 2,3   # lines before and after
Select-String -Path app.log -Pattern "INFO" -NotMatch
Get-ChildItem -Recurse -Include *.cs | Select-String "TODO" | Select-Object Path, LineNumber, Line
```

### Sorting, Counting, Filtering

```cmd
sort names.txt
sort /r names.txt                        :: reverse
sort /+5 names.txt                       :: sort starting at column 5
```

```powershell
Get-Content names.txt | Sort-Object -Unique
Get-Content access.log | Measure-Object -Line
Get-Content names.txt | Group-Object | Sort-Object Count -Descending
Get-Process | Sort-Object WS -Descending | Select-Object -First 5 Name, @{n='MB';e={[int]($_.WS/1MB)}}
"a,b,c" -split ","
(Get-Content config.txt) -replace "http:","https:" | Set-Content config.txt
```

### Real-World Examples

```cmd
:: Port owner (process listening on port 3000)
netstat -ano | findstr :3000

:: Search all config files for a string
findstr /s /i /m "ConnectionString" C:\inetpub\*.config

:: Count error lines in a log
find /c /i "error" C:\logs\app.log

:: Find largest files in current tree (PowerShell)
powershell -NoProfile -Command "Get-ChildItem -Recurse -File | Sort Length -Descending | Select -First 10 FullName,Length"
```

---

## Networking

### Core Commands

```cmd
ipconfig                      :: IP, mask, gateway for each adapter
ipconfig /all                 :: MAC, DHCP server, DNS servers, lease times
ipconfig /release             :: release DHCP lease
ipconfig /renew               :: request a new DHCP lease
ipconfig /flushdns            :: clear DNS client cache
ipconfig /displaydns          :: show DNS cache
ipconfig /registerdns         :: re-register DNS records

ping 8.8.8.8                  :: 4 packets by default
ping -t 8.8.8.8               :: continuous (Ctrl+C to stop, Ctrl+Break for stats)
ping -n 10 google.com         :: send 10 packets
ping -l 1472 -f 8.8.8.8       :: MTU test with Don't Fragment
ping -4 localhost             :: force IPv4

tracert google.com            :: path to destination
tracert -d google.com         :: skip name resolution (faster)
pathping google.com           :: tracert plus per-hop packet loss statistics

nslookup google.com
nslookup -type=MX example.com
nslookup example.com 8.8.8.8  :: query a specific DNS server

route print                   :: routing table
route add 10.10.0.0 mask 255.255.0.0 192.168.1.254 -p   :: persistent static route
arp -a                        :: ARP cache (IP to MAC)
getmac /v                     :: MAC addresses
hostname
nbtstat -n                    :: NetBIOS names
```

### netstat — Connections and Listening Ports

```cmd
netstat -ano                     :: all connections with PID
netstat -ano | findstr LISTENING :: only listening ports
netstat -ano | findstr :443
netstat -b                       :: show executable (admin)
netstat -s                       :: protocol statistics
netstat -r                       :: routing table
netstat -e                       :: Ethernet statistics
tasklist /fi "PID eq 1234"       :: identify process by PID
```

```powershell
Get-NetTCPConnection -State Listen | Sort-Object LocalPort | Format-Table LocalAddress, LocalPort, OwningProcess
Get-NetTCPConnection -LocalPort 3000 | Select-Object State, OwningProcess
Get-Process -Id (Get-NetTCPConnection -LocalPort 3000).OwningProcess
```

### PowerShell Network Commands

```powershell
Get-NetIPAddress
Get-NetIPConfiguration
Get-NetAdapter                                   # adapters, speed, status
Get-DnsClientServerAddress
Resolve-DnsName example.com -Type A
Test-NetConnection google.com                    # ICMP test
Test-NetConnection google.com -Port 443          # TCP port test (replaces telnet)
Test-NetConnection google.com -TraceRoute
Get-NetFirewallRule -Enabled True | Select-Object DisplayName, Direction, Action
Restart-NetAdapter -Name "Ethernet"
```

### netsh — Network Shell

```cmd
netsh interface ip show config                       :: adapter configuration
netsh interface ip set address "Ethernet" static 192.168.1.50 255.255.255.0 192.168.1.1
netsh interface ip set dns "Ethernet" static 8.8.8.8
netsh interface ip set address "Ethernet" dhcp       :: back to DHCP
netsh winsock reset                                  :: reset Winsock catalog (reboot after)
netsh int ip reset                                   :: reset TCP/IP stack
netsh wlan show profiles                             :: saved Wi-Fi networks
netsh wlan show profile name="SSID" key=clear        :: includes saved key (admin, own devices only)
netsh wlan show interfaces                           :: current Wi-Fi signal and channel
netsh advfirewall show allprofiles
netsh advfirewall set allprofiles state off          :: for testing only, turn back on after
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.20.0.2
netsh interface portproxy show all
```

### Network Troubleshooting Flow

```text
1. ping 127.0.0.1           loopback: TCP/IP stack works
2. ping <own IP>            adapter works
3. ping <default gateway>   local network/router works
4. ping 8.8.8.8             internet routing works (no DNS involved)
5. ping google.com          DNS resolution works
6. Test-NetConnection host -Port N   specific service/port reachable
```

| Symptom | Likely Cause | Fix |
|---|---|---|
| `169.254.x.x` address (APIPA) | DHCP server unreachable | Check cable/Wi-Fi, `ipconfig /renew`, DHCP service |
| Gateway pings but internet does not | ISP or router issue, DNS | Test `8.8.8.8`, check DNS |
| IP works, names fail | DNS problem | `ipconfig /flushdns`, change DNS server |
| Slow or random failures | Duplicate IP, bad cable, Wi-Fi interference | `arp -a`, `pathping`, check link speed |
| Only one app fails | Firewall, proxy, certificate | Check firewall rules, proxy settings, system time |
| Cannot reach shares | SMB blocked, wrong credentials, name resolution | `Test-NetConnection server -Port 445`, `net use` |

### Network Locations (Profiles)

| Profile | Meaning |
|---|---|
| Domain | Connected to the corporate domain; domain firewall rules apply |
| Private | Trusted network (home); discovery enabled |
| Public | Untrusted network; most restrictive rules |

```powershell
Get-NetConnectionProfile
Set-NetConnectionProfile -InterfaceAlias "Ethernet" -NetworkCategory Private
```

### Windows Defender Firewall

```cmd
netsh advfirewall firewall add rule name="Allow TCP 8080" dir=in action=allow protocol=TCP localport=8080
netsh advfirewall firewall delete rule name="Allow TCP 8080"
netsh advfirewall firewall show rule name=all | findstr /i "8080"
wf.msc
```

```powershell
New-NetFirewallRule -DisplayName "Allow TCP 8080" -Direction Inbound -Protocol TCP -LocalPort 8080 -Action Allow
Get-NetFirewallRule -DisplayName "Allow TCP 8080"
Remove-NetFirewallRule -DisplayName "Allow TCP 8080"
```

Default behavior: inbound blocked unless allowed; outbound allowed unless blocked. Profiles (Domain, Private, Public) each have separate rule sets.

### hosts File

Location: `C:\Windows\System32\drivers\etc\hosts` (edit as Administrator).

```text
127.0.0.1   localhost
192.168.1.10  intranet.local
```

```cmd
notepad C:\Windows\System32\drivers\etc\hosts
ipconfig /flushdns
```

### File Sharing and Mapped Drives

```cmd
net use Z: \\server\share /persistent:yes              :: map a drive
net use Z: \\server\share /user:DOMAIN\user *          :: with prompted password
net use                                                :: list connections
net use Z: /delete
net use * /delete /y                                   :: remove all mappings
net view \\server                                      :: list shares on a server
net session                                            :: who is connected to this machine
net use \\server\IPC$ /user:DOMAIN\user *              :: test credentials
```

| Default Admin Share | Purpose |
|---|---|
| `C$`, `D$` | Root of each drive (administrators only) |
| `ADMIN$` | `C:\Windows` |
| `IPC$` | Inter-process communication |

Network error `System error 53` means the path was not found (name resolution or SMB blocked); `System error 5` means access denied; `System error 1219` means conflicting credentials for the same server (use `net use * /delete`).

### Built-in Clients

```cmd
ssh user@host                     :: OpenSSH client is built in
ssh-keygen -t ed25519 -C "email@example.com"
scp file.txt user@host:/tmp/
curl.exe -I https://example.com   :: use curl.exe (PowerShell 5.1 aliases `curl` to Invoke-WebRequest)
curl.exe -L -o file.zip https://example.com/file.zip
mstsc /v:server01                 :: Remote Desktop
tar -tf archive.tar.gz            :: bsdtar is built in
```

```powershell
Invoke-WebRequest https://example.com -OutFile page.html
Invoke-RestMethod https://api.example.com/items
```

### Proxy and Certificates

```cmd
netsh winhttp show proxy
netsh winhttp set proxy proxy.corp.local:8080
netsh winhttp reset proxy
certmgr.msc                :: current user certificates
certlm.msc                 :: local machine certificates
certutil -store My         :: list certificates in the personal store
certutil -urlcache -split -f https://host/file.cer file.cer
```

> TLS errors on a single machine often come from an incorrect system clock; check `w32tm /query /status` before reinstalling certificates.

### Common Ports

| Port | Protocol | Service |
|---|---|---|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH, SFTP |
| 25 / 587 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 67/68 | UDP | DHCP |
| 80 | TCP | HTTP |
| 88 | TCP/UDP | Kerberos |
| 123 | UDP | NTP / Windows Time |
| 135 | TCP | RPC Endpoint Mapper |
| 137-139 | TCP/UDP | NetBIOS |
| 389 / 636 | TCP | LDAP / LDAPS |
| 443 | TCP | HTTPS |
| 445 | TCP | SMB file sharing |
| 1433 | TCP | SQL Server |
| 3268 / 3269 | TCP | Global Catalog |
| 3306 | TCP | MySQL |
| 3389 | TCP/UDP | Remote Desktop (RDP) |
| 5432 | TCP | PostgreSQL |
| 5985 / 5986 | TCP | WinRM HTTP / HTTPS |

---

## Archives and Compression

```cmd
tar -cvf backup.tar C:\data              :: bsdtar ships with Windows 10 and later
tar -czvf backup.tar.gz C:\data          :: gzip compressed
tar -xvf backup.tar -C D:\restore        :: extract to a directory
tar -tf backup.tar.gz                    :: list contents
tar -a -c -f archive.zip folder          :: create zip (format chosen from extension)
expand -r cabfile.cab C:\out             :: extract CAB files
makecab file.txt file.cab                :: create CAB
```

```powershell
Compress-Archive -Path C:\data\* -DestinationPath C:\backup.zip
Compress-Archive -Path C:\data\* -DestinationPath C:\backup.zip -Update
Expand-Archive -Path C:\backup.zip -DestinationPath C:\restore -Force
```

| Format | Native Support | Notes |
|---|---|---|
| `.zip` | Yes (Explorer, `tar`, `Compress-Archive`) | Most compatible |
| `.tar`, `.tar.gz` | Yes (`tar`) | Common for Linux transfers |
| `.7z`, `.rar` | Newer builds support reading many formats; otherwise 7-Zip | `winget install 7zip.7zip` |
| `.cab` | Yes | Windows Update packages |
| `.iso`, `.vhd`, `.vhdx` | Mountable (double-click) | `Mount-DiskImage` |

---

## Package and Software Management

### winget — Windows Package Manager

```cmd
winget search git
winget show Git.Git
winget install --id Git.Git -e --silent
winget install --id Microsoft.VisualStudioCode -e
winget list                                  :: installed software
winget upgrade                               :: available updates
winget upgrade --all --silent --accept-package-agreements --accept-source-agreements
winget uninstall --id Git.Git
winget source list
winget export -o apps.json                   :: export installed apps
winget import -i apps.json                   :: reinstall on a new machine
```

### Other Package Managers

| Tool | Install Scope | Example |
|---|---|---|
| Chocolatey | System-wide, community repo, business tier | `choco install git -y` / `choco upgrade all -y` / `choco list` |
| Scoop | Per-user, portable apps, no admin | `scoop install git` / `scoop bucket add extras` |
| Microsoft Store | Store apps (MSIX) | `winget install --source msstore` |

### Installer Types and Silent Switches

| Format | Installer | Silent Install |
|---|---|---|
| `.msi` | Windows Installer | `msiexec /i app.msi /qn /norestart /L*v C:\install.log` |
| `.exe` (Inno Setup) | Custom | `/VERYSILENT /SUPPRESSMSGBOXES /NORESTART` |
| `.exe` (NSIS) | Custom | `/S` |
| `.exe` (WiX bundle) | Burn | `/quiet /norestart` |
| `.msix` / `.appx` | Modern packaging | `Add-AppxPackage app.msix` |
| `.msu` | Update package | `wusa update.msu /quiet /norestart` |

```cmd
msiexec /x {PRODUCT-GUID} /qn                :: uninstall MSI by product code
msiexec /i app.msi TRANSFORMS=custom.mst /qn :: with transform
```

```powershell
Get-AppxPackage *store*                       # modern apps for current user
Get-AppxPackage -AllUsers | Select Name, Version
Remove-AppxPackage <PackageFullName>
```

> Silent switches vary per vendor. Check `app.exe /?` or the vendor docs. Avoid `wmic product` for uninstalls: it triggers an MSI consistency check on every package.

### Where Installed Software Is Recorded

```text
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall     (32-bit apps)
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall                  (per-user installs)
```

```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall /s /v DisplayName
appwiz.cpl
```

### Windows Installer (MSI) Exit Codes and Troubleshooting

```cmd
msiexec /i app.msi /qn /norestart /L*v C:\Temp\app-install.log
echo %ERRORLEVEL%
```

| Exit Code | Meaning | Action |
|---|---|---|
| `0` | Success | |
| `1601` | Windows Installer service could not be accessed | Check the `msiserver` service; `msiexec /unregister` then `msiexec /regserver` |
| `1602` | User cancelled | |
| `1603` | Fatal error during installation | Open the log and search for `Return value 3`; read the lines **above** it |
| `1605` | Product is not installed (uninstall or patch target missing) | Check the product code; clean up manually |
| `1618` | Another installation is already in progress | Wait, check for stray `msiexec.exe` processes, or reboot |
| `1619` / `1620` | Package could not be opened | Corrupt download, path or permission problem, wrong file |
| `1625` | Installation blocked by system policy | Check AppLocker, WDAC, or the Windows Installer policy |
| `1638` | Another version of this product is already installed | Uninstall it, or use an upgrade package |
| `1641` | Success, reboot initiated | |
| `3010` | Success, **reboot required** | Treat as success in deployment tools |

Common causes of `1603`: leftover files from a previous install, missing prerequisites (.NET, VC++), a locked file (antivirus, running app), a custom action running as a user without rights, or a path that is too long. Run the installer elevated, from a local path (not a network share), with antivirus temporarily excluded if the log shows blocked writes.

| Standard MSI Property | Effect |
|---|---|
| `ALLUSERS=1` | Install per-machine |
| `INSTALLDIR="C:\Apps\X"` | Custom path (property name depends on the vendor) |
| `REBOOT=ReallySuppress` | Never reboot automatically |
| `/qn` / `/qb` | No UI / basic progress UI |

> Do not delete `C:\Windows\Installer`. It holds cached MSIs and patches that the uninstall and repair paths depend on. To find a product's uninstall string without `Win32_Product`, read the `Uninstall` registry keys or use `winget list`.

### Windows Features and Capabilities

```cmd
optionalfeatures                                         :: GUI
DISM /Online /Get-Features /Format:Table
DISM /Online /Enable-Feature /FeatureName:Microsoft-Hyper-V-All /All
DISM /Online /Enable-Feature /FeatureName:VirtualMachinePlatform /All
DISM /Online /Enable-Feature /FeatureName:Microsoft-Windows-Subsystem-Linux /All
DISM /Online /Disable-Feature /FeatureName:SMB1Protocol
DISM /Online /Get-Capabilities | findstr /i openssh
```

```powershell
Get-WindowsOptionalFeature -Online | Where-Object State -eq Enabled
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

### Typical Developer Workstation Bootstrap

```cmd
winget install --id Git.Git -e
winget install --id Microsoft.VisualStudioCode -e
winget install --id Microsoft.WindowsTerminal -e
winget install --id Microsoft.PowerShell -e
winget install --id Docker.DockerDesktop -e
winget install --id OpenJS.NodeJS.LTS -e
winget install --id Python.Python.3.13 -e
winget install --id Microsoft.Sysinternals.Suite -e
winget install --id Microsoft.PowerToys -e
wsl --install
```

---

## Disk, Partitioning and Storage

### Partition Styles

| Style | Firmware | Limits | Layout |
|---|---|---|---|
| MBR | Legacy BIOS | 2 TB max disk, 4 primary partitions | System Reserved + Windows partition |
| GPT | UEFI | 9.4 ZB theoretical, 128 partitions by default | EFI System Partition (FAT32) + MSR + Windows + Recovery |

Typical Windows 11 GPT layout:

```text
Disk 0
├── EFI System Partition   100-260 MB   FAT32   boot manager files
├── Microsoft Reserved     16 MB                no filesystem
├── C: Windows             NTFS                 OS, apps, users
└── Recovery               ~1 GB        NTFS    Windows Recovery Environment (WinRE)
```

### Viewing Disks

```cmd
diskmgmt.msc                    :: GUI Disk Management
wmic logicaldisk get name,size,freespace    :: legacy, may be unavailable
fsutil volume diskfree C:
```

```powershell
Get-Disk
Get-Partition
Get-Volume
Get-PhysicalDisk | Select-Object FriendlyName, MediaType, HealthStatus, Size
```

### diskpart — Partition Management

> **Warning:** `clean` erases the partition table of the selected disk immediately. Always run `list disk`, `select disk N`, and `detail disk` to confirm the right disk before any destructive command.

```cmd
diskpart
DISKPART> list disk
DISKPART> select disk 1
DISKPART> detail disk
DISKPART> clean                              :: wipes the partition table
DISKPART> convert gpt                        :: or: convert mbr
DISKPART> create partition primary
DISKPART> format fs=ntfs quick label=Data
DISKPART> assign letter=E
DISKPART> list volume
DISKPART> extend                             :: grow a volume into free space
DISKPART> attributes disk clear readonly     :: fix a write-protected disk
DISKPART> exit
```

### Formatting and Filesystem Repair

```cmd
format E: /FS:NTFS /Q /V:Data           :: quick format
convert D: /fs:ntfs                     :: convert FAT32 to NTFS without data loss
chkdsk C:                               :: read-only scan
chkdsk C: /f                            :: fix filesystem errors (schedules at reboot for C:)
chkdsk C: /r                            :: also locate bad sectors and recover data (slow)
chkdsk C: /scan                         :: online scan (NTFS, no downtime)
chkdsk C: /spotfix                      :: quick fix at next reboot
fsutil dirty query C:                   :: shows if the volume is marked dirty
```

### Optimization and Cleanup

```cmd
defrag C: /O                  :: optimize: defrag HDD, TRIM SSD
defrag C: /L                  :: retrim SSD
cleanmgr /sageset:1           :: configure Disk Cleanup profile
cleanmgr /sagerun:1           :: run it
DISM /Online /Cleanup-Image /StartComponentCleanup /ResetBase
```

```powershell
Optimize-Volume -DriveLetter C -ReTrim -Verbose
```

Also use **Settings > System > Storage > Storage Sense** for automatic cleanup of temp files and Recycle Bin.

### Drive Letter Tricks

```cmd
subst X: C:\Projects\app          :: map a folder to a drive letter (session)
subst X: /d
mountvol                          :: list volume GUIDs and mount points
mountvol E: /d                    :: remove a drive letter
```

### BitLocker Drive Encryption

```cmd
manage-bde -status
manage-bde -protectors -get C:                 :: shows recovery key protector
manage-bde -on C: -RecoveryPassword            :: enable with recovery password
manage-bde -off C:                             :: decrypt
manage-bde -pause C:                           :: suspend protection (e.g. before firmware update)
manage-bde -resume C:
```

```powershell
Get-BitLockerVolume
```

| Item | Details |
|---|---|
| Recovery key | 48-digit numeric password. Back it up to Microsoft account, Entra ID, or Active Directory |
| TPM | Normally unlocks automatically at boot |
| Recovery screen | Appears after hardware/firmware/boot-order changes or tampering |
| Editions | Full management on Pro/Enterprise; Home has "Device encryption" on supported hardware |

### Shadow Copies and Virtual Disks

```cmd
vssadmin list shadows                           :: Volume Shadow Copy snapshots
vssadmin list shadowstorage
vssadmin resize shadowstorage /for=C: /on=C: /maxsize=10GB
```

```powershell
Mount-DiskImage -ImagePath D:\ubuntu.iso
Dismount-DiskImage -ImagePath D:\ubuntu.iso
```

Storage Spaces pools several physical disks into a resilient virtual disk (mirror, parity) on Windows 10/11 and Server.

### Linux Storage Equivalents

| Linux | Windows |
|---|---|
| `fdisk`, `parted` | `diskpart`, Disk Management |
| `mkfs` | `format` |
| `fsck` | `chkdsk` |
| `mount` | Drive letter assignment, `mountvol`, `subst` |
| `df` | `Get-Volume`, `fsutil volume diskfree` |
| `/etc/fstab` | Persistent via volume GUID and Disk Management |
| LUKS | BitLocker |
| LVM | Storage Spaces / Dynamic Disks |

---

## System Monitoring and Resources

### Task Manager

Open with `Ctrl+Shift+Esc` or `taskmgr`.

| Tab | Information |
|---|---|
| Processes | CPU, memory, disk, network per app |
| Performance | CPU, memory, disk, network, GPU graphs |
| App history | Resource use by modern apps |
| Startup apps | Enable/disable launch at sign-in, with startup impact |
| Users | Resource use per signed-in user |
| Details | PIDs, priority, affinity, user, command line |
| Services | Service status, jump to `services.msc` |

### Resource Monitor and Performance Monitor

```cmd
resmon         :: CPU, memory, disk, network with per-process detail
perfmon        :: performance counters, data collector sets
perfmon /rel   :: Reliability Monitor (stability timeline, crashes, installs)
```

Resource Monitor tricks:

- **CPU > Associated Handles**: search a file name to find which process has it open (fixes "file in use" errors).
- **CPU > Analyze Wait Chain**: identifies what a hung process is waiting on.
- **Disk tab**: shows which process causes 100% disk.

| Useful Counter | Healthy Indicator |
|---|---|
| `\Processor(_Total)\% Processor Time` | Sustained below ~80% |
| `\Memory\Available MBytes` | Not near zero |
| `\Memory\Pages/sec` | Low; constant high values suggest memory pressure |
| `\PhysicalDisk(_Total)\Avg. Disk Queue Length` | Below ~2 per spindle |
| `\Network Interface(*)\Bytes Total/sec` | Compare to link speed |

```cmd
typeperf "\Processor(_Total)\% Processor Time" -sc 5
```

### System and Hardware Information

```cmd
systeminfo                                    :: OS, boot time, RAM, hotfixes, NICs
systeminfo | findstr /B /C:"System Boot Time" /C:"Total Physical Memory" /C:"Available Physical Memory"
hostname
ver
msinfo32                                      :: GUI: hardware, BIOS mode, Secure Boot
dxdiag                                        :: graphics and sound
driverquery /v
net statistics workstation                    :: "Statistics since" shows last boot
```

```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, BuildNumber, LastBootUpTime
Get-CimInstance Win32_ComputerSystem | Select-Object Manufacturer, Model, TotalPhysicalMemory
Get-CimInstance Win32_BIOS | Select-Object SerialNumber, SMBIOSBIOSVersion
Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors
Get-CimInstance Win32_PhysicalMemory | Select-Object Capacity, Speed, Manufacturer
Get-CimInstance Win32_VideoController | Select-Object Name, DriverVersion
Get-CimInstance Win32_LogicalDisk -Filter "DriveType=3" | Select-Object DeviceID, Size, FreeSpace
(Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime     # uptime
```

### Linux Monitoring Equivalents

| Linux | Windows |
|---|---|
| `top`, `htop` | Task Manager, `Get-Process`, Process Explorer |
| `free -h` | Task Manager > Performance > Memory, `systeminfo` |
| `df -h` | `Get-Volume` |
| `du -sh` | `Get-ChildItem -Recurse \| Measure-Object Length -Sum`, WinDirStat, TreeSize |
| `uptime` | Task Manager > Performance > CPU > Up time |
| `vmstat`, `sar` | Performance Monitor, `typeperf` |
| `iostat` | Resource Monitor > Disk |
| `lsof` | Resource Monitor handles, Sysinternals `handle.exe` |
| `strace` | Sysinternals Process Monitor |
| `lspci`, `lsusb` | Device Manager, `Get-PnpDevice` |
| `dmesg` | Event Viewer, System log |

### Sysinternals Suite

Free Microsoft tools that every Windows administrator uses.

| Tool | Purpose |
|---|---|
| Process Explorer | Advanced Task Manager: handles, DLLs, signature verification, VirusTotal check |
| Process Monitor | Real-time file, registry, process, and network activity |
| Autoruns | Everything configured to start automatically |
| TCPView | Connections and listening ports per process |
| PsExec / PsTools | Run commands on remote computers, list/kill remote processes |
| Sysmon | Detailed security event logging |
| Handle | Which process has a file open |
| ProcDump | Capture a process memory dump |
| RAMMap | Detailed memory usage |
| Sigcheck | Verify file signatures and hashes |
| AccessChk | Effective permissions of a user on a resource |
| Disk2vhd | Convert a physical machine to a VHD |

```cmd
winget install Microsoft.Sysinternals.Suite
```

### Advanced Diagnostics Workflow

#### Process Monitor (Procmon)

Use it when an application fails, hangs, or cannot find something.

```text
1. Run Procmon as administrator, then Ctrl+E (stop) and Ctrl+X (clear).
2. Filter: Process Name is app.exe  (Filter > Filter... or Ctrl+L).
3. Start capture (Ctrl+E), reproduce the problem, stop capture.
4. Add filter: Result is not SUCCESS, then scan what remains.
5. Right-click a suspicious line > Properties > Stack to see the calling module.
```

| Result in Procmon | Meaning |
|---|---|
| `NAME NOT FOUND` on a `.dll` path | App searched for a file that is absent (often normal; important if it is the final attempt) |
| `ACCESS DENIED` | Permission problem; shows the exact path and user |
| `PATH NOT FOUND` | Wrong or missing directory or mapped drive |
| `SHARING VIOLATION` | Another process has the file open |
| `BUFFER OVERFLOW` | Usually benign (a size query) |

Boot-time issues: **Options > Enable Boot Logging**, reboot, then open the log.

#### Process Explorer and Autoruns

- Process Explorer: **Options > Verify Image Signatures** and **VirusTotal** check; `Ctrl+F` finds which process holds a handle or DLL; the process tree shows who launched what (for example `winword.exe` spawning `powershell.exe` is suspicious).
- Autoruns: tick **Hide Microsoft Entries** and **Verify Code Signatures**, then review Logon, Scheduled Tasks, Services, Drivers, Image Hijacks, and WMI tabs.

#### Performance Traces

```cmd
wpr -start GeneralProfile -filemode
:: ...reproduce the slowness...
wpr -stop C:\trace.etl
```

Open the `.etl` in **Windows Performance Analyzer** (Microsoft Store) to see CPU usage by stack, disk I/O, DPC/ISR time, and boot phases. WPR can also trace boot and shutdown performance.

#### WMI Health

```cmd
winmgmt /verifyrepository
winmgmt /salvagerepository
winmgmt /resetrepository          :: last resort; can break software that registered WMI classes
```

Symptoms of a broken WMI repository: `Get-CimInstance` hangs or returns `0x80041002`/`Invalid class`, high `WmiPrvSE.exe` usage, and failing management agents. The log `Microsoft-Windows-WMI-Activity/Operational` (Events 5857 to 5861) shows which provider or caller is responsible.

#### Collect a Diagnostic Bundle for a Support Case

```cmd
mkdir C:\Diag
systeminfo > C:\Diag\systeminfo.txt
ipconfig /all > C:\Diag\ipconfig.txt
driverquery /v /fo csv > C:\Diag\drivers.csv
gpresult /h C:\Diag\gpresult.html /f
msinfo32 /report C:\Diag\msinfo32.txt
dxdiag /t C:\Diag\dxdiag.txt
wevtutil epl System C:\Diag\System.evtx
wevtutil epl Application C:\Diag\Application.evtx
powercfg /energy /output C:\Diag\energy.html
```

Add the Reliability Monitor screenshot, the exact error text, and the time it happened. Check that the bundle contains no passwords or personal data before sending it outside the organization.

---

## Process and Service Management

### Process Commands

```cmd
tasklist                                     :: running processes
tasklist /svc                                :: show services in each process
tasklist /v                                  :: include user, window title, CPU time
tasklist /fi "IMAGENAME eq chrome.exe"       :: filter
tasklist /fi "STATUS eq NOT RESPONDING"
tasklist /m ntdll.dll                        :: processes that loaded a DLL

taskkill /im notepad.exe                     :: end by name (graceful request)
taskkill /im notepad.exe /f                  :: force
taskkill /pid 1234 /f
taskkill /pid 1234 /t /f                     :: also kill child processes (process tree)
taskkill /fi "STATUS eq NOT RESPONDING" /f   :: kill all hung applications
taskkill /s PC01 /u DOMAIN\admin /im app.exe /f   :: remote kill

start notepad                                :: launch a program
start /min notepad                           :: minimized
start /low app.exe                           :: low priority
start /b app.exe                             :: background without new window
start /wait installer.exe                    :: wait for completion
start "" "C:\Program Files\App\app.exe"      :: first quoted string is the window title
```

```powershell
Get-Process
Get-Process chrome
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, Id, CPU
Get-Process | Sort-Object WS -Descending | Select-Object -First 10 Name, Id, @{n='MB';e={[int]($_.WS/1MB)}}
Stop-Process -Name notepad -Force
Stop-Process -Id 1234
Start-Process notepad
Start-Process powershell -Verb RunAs
(Get-Process notepad).PriorityClass = "BelowNormal"
Wait-Process -Name installer
```

| Linux Signal | Windows Behavior |
|---|---|
| `kill` (SIGTERM) | `taskkill` without `/f` sends a window-close request |
| `kill -9` (SIGKILL) | `taskkill /f` terminates immediately, no cleanup |
| `Ctrl+C` | Same in console apps |
| `nohup`, `&` | `start /b`, Task Scheduler, or run as a service |

### Process Priority

| Class | Use |
|---|---|
| Realtime | Avoid; can starve the system |
| High | Time-critical foreground work |
| Above Normal / Normal / Below Normal | Standard range |
| Low | Background tasks |

Set from Task Manager > Details > right-click > Set priority, `start /low`, or PowerShell `PriorityClass`.

### Services

A **service** runs in the background without a user session, managed by the Service Control Manager.

```cmd
sc query                                    :: running services
sc query state= all                         :: all services (note the space after =)
sc query Spooler
sc qc Spooler                               :: configuration (path, start type, account)
sc start Spooler
sc stop Spooler
sc config Spooler start= auto               :: start= auto | demand | delayed-auto | disabled
sc config wuauserv start= disabled
sc failure Spooler reset= 86400 actions= restart/5000/restart/5000/restart/30000
sc create MyApp binPath= "C:\apps\myapp.exe" start= auto
sc delete MyApp

net start                                   :: running services
net start Spooler
net stop Spooler
net stop Spooler && net start Spooler       :: restart
```

```powershell
Get-Service
Get-Service -Name "Spooler","wuauserv"
Get-Service | Where-Object Status -eq 'Stopped' | Where-Object StartType -eq 'Automatic'
Start-Service Spooler
Restart-Service Spooler -Force
Set-Service -Name Spooler -StartupType Automatic
Get-Service Spooler | Select-Object -ExpandProperty DependentServices
```

| Startup Type | Behavior |
|---|---|
| Automatic | Starts at boot |
| Automatic (Delayed Start) | Starts shortly after boot to speed up logon |
| Manual | Starts when needed or on trigger |
| Disabled | Cannot be started |

| Service Account | Privileges |
|---|---|
| Local System | Full local privileges, acts as machine on network |
| Local Service | Minimal, anonymous on network |
| Network Service | Minimal, acts as machine on network |
| Virtual account `NT SERVICE\name` | Per-service isolated identity (recommended) |
| gMSA | Group Managed Service Account in AD, automatic password rotation |

#### Frequently Used Services

| Service Name | Display Name | Notes |
|---|---|---|
| `wuauserv` | Windows Update | Update download and install |
| `BITS` | Background Intelligent Transfer | Used by updates |
| `Spooler` | Print Spooler | Printing; restart to clear stuck jobs |
| `Dhcp` | DHCP Client | Gets IP address |
| `Dnscache` | DNS Client | Resolver cache |
| `LanmanServer` | Server | Hosts file shares |
| `LanmanWorkstation` | Workstation | Connects to shares |
| `EventLog` | Windows Event Log | Logging |
| `mpssvc` | Windows Defender Firewall | |
| `WinDefend` | Microsoft Defender Antivirus | |
| `W32Time` | Windows Time | Clock sync; Kerberos needs time skew under 5 minutes |
| `Winmgmt` | WMI | Management instrumentation |
| `TermService` | Remote Desktop Services | RDP host |
| `Schedule` | Task Scheduler | |
| `CryptSvc` | Cryptographic Services | Certificates, update signing |
| `sshd` | OpenSSH Server | If installed |

### Startup Programs

Programs launch at sign-in from several places. Check all of them when diagnosing slow logon.

| Location | Scope |
|---|---|
| Task Manager > Startup apps | Both, easy toggle |
| `shell:startup` folder | Current user |
| `shell:common startup` folder | All users |
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | Current user |
| `HKLM\Software\Microsoft\Windows\CurrentVersion\Run` | All users |
| Task Scheduler (At logon triggers) | Both |
| Services | Machine |
| Autoruns (Sysinternals) | Shows everything in one view |

```cmd
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
msconfig
```

### Time Synchronization

```cmd
w32tm /query /status
w32tm /query /configuration
w32tm /resync
w32tm /config /manualpeerlist:"time.windows.com" /syncfromflags:manual /update
w32tm /monitor                         :: domain time sources
net time \\server01                    :: time on a remote machine
```

Wrong system time breaks TLS certificate validation, Kerberos authentication, and MFA codes.

---

## Scheduling with Task Scheduler

Windows equivalent of cron and `at`. GUI: `taskschd.msc`.

| Component | Meaning |
|---|---|
| Trigger | When: time, daily/weekly, at startup, at logon, on idle, on an event |
| Action | What: start a program, send email (deprecated), display message (deprecated) |
| Conditions | Idle, on AC power, network available |
| Settings | Run as soon as possible if missed, restart on failure, stop after N hours |
| Security options | Account to run as, "Run whether user is logged on or not", "Run with highest privileges" |

```cmd
schtasks /create /tn "NightlyBackup" /tr "C:\scripts\backup.cmd" /sc daily /st 01:00 /ru SYSTEM
schtasks /create /tn "Every5Min" /tr "C:\tools\check.exe" /sc minute /mo 5
schtasks /create /tn "WeeklyClean" /tr "C:\scripts\clean.cmd" /sc weekly /d MON /st 03:00
schtasks /create /tn "OnBoot" /tr "C:\scripts\init.cmd" /sc onstart /ru SYSTEM
schtasks /create /tn "OnLogon" /tr "notepad.exe" /sc onlogon
schtasks /query /tn "NightlyBackup" /v /fo LIST
schtasks /query /fo TABLE
schtasks /run /tn "NightlyBackup"
schtasks /end /tn "NightlyBackup"
schtasks /change /tn "NightlyBackup" /disable
schtasks /delete /tn "NightlyBackup" /f
```

```powershell
Get-ScheduledTask | Where-Object State -ne Disabled
Get-ScheduledTaskInfo -TaskName "NightlyBackup"
Start-ScheduledTask -TaskName "NightlyBackup"
```

| Last Run Result | Meaning |
|---|---|
| `0x0` | Success |
| `0x1` | Incorrect function / program returned error |
| `0x2` | File not found (wrong path) |
| `0x41301` | Task is currently running |
| `0x41303` | Task has not yet run |
| `0x80070005` | Access denied |
| `0x800710E0` | Operator or administrator refused the request (rights / "run whether logged on") |

| Cron | Task Scheduler |
|---|---|
| `crontab -e` | `taskschd.msc`, `schtasks /create` |
| `*/5 * * * *` | `/sc minute /mo 5` |
| `@reboot` | `/sc onstart` |
| `at` | `schtasks /sc once /st HH:MM` |
| Logs in `/var/log/cron` | Task Scheduler Operational log (enable history) |

> Task history is disabled by default. Enable it with **Task Scheduler > Action > Enable All Tasks History**.

---

## Logs and Event Viewer

### Event Viewer

Open `eventvwr.msc`. Logs are stored as `.evtx` files in `C:\Windows\System32\winevt\Logs`.

| Log | Contents |
|---|---|
| Application | Software events and crashes |
| Security | Logon, auditing, privilege use (needs audit policy) |
| System | OS components, drivers, services, disk, boot |
| Setup | Installation and update events |
| Forwarded Events | Collected from other computers |
| Applications and Services Logs | Per-component logs, e.g. `Microsoft > Windows > PowerShell`, `TaskScheduler`, `DNS`, `Hyper-V` |

| Level | Meaning |
|---|---|
| Critical | Severe failure |
| Error | Significant problem |
| Warning | Potential problem |
| Information | Normal operation |
| Verbose | Detailed tracing |

### Command-Line Log Access

```cmd
wevtutil el                                      :: list log names
wevtutil qe System /c:20 /rd:true /f:text        :: last 20 System events, newest first
wevtutil qe Security /q:"*[System[(EventID=4625)]]" /c:10 /rd:true /f:text
wevtutil epl System C:\backup\system.evtx        :: export a log
wevtutil cl Application                          :: clear a log (careful: destroys evidence)
```

```powershell
Get-WinEvent -LogName System -MaxEvents 20
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2; StartTime=(Get-Date).AddDays(-1)}    # errors, last 24h
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625; StartTime=(Get-Date).AddHours(-6)} | Select-Object TimeCreated, Message
Get-WinEvent -ListLog * | Where-Object RecordCount -gt 0 | Sort-Object RecordCount -Descending | Select-Object -First 10
```

### Important Event IDs

| Event ID | Log | Meaning |
|---|---|---|
| 41 | System | Kernel-Power: unexpected restart or power loss |
| 1074 | System | Planned shutdown or restart (who and why) |
| 6005 / 6006 | System | Event Log service started / stopped (boot / clean shutdown markers) |
| 6008 | System | Previous shutdown was unexpected |
| 6013 | System | System uptime |
| 7000 / 7001 | System | Service failed to start / dependency failure |
| 7031 / 7034 | System | Service terminated unexpectedly |
| 7036 | System | Service entered running or stopped state |
| 7045 | System | New service installed (security relevant) |
| 7 / 11 / 15 | System | Disk bad block / controller error / device not ready |
| 55 | System | NTFS corruption detected |
| 129 / 153 | System | Disk I/O reset / retry (failing storage) |
| 1000 | Application | Application crash (faulting module) |
| 1001 | Application | Windows Error Reporting record |
| 1002 | Application | Application hang |
| 4624 | Security | Successful logon |
| 4625 | Security | **Failed** logon |
| 4634 / 4647 | Security | Logoff |
| 4648 | Security | Logon with explicit credentials |
| 4672 | Security | Special privileges assigned (admin logon) |
| 4688 | Security | New process created |
| 4698 | Security | Scheduled task created |
| 4720 | Security | User account created |
| 4722 / 4725 | Security | Account enabled / disabled |
| 4724 / 4723 | Security | Password reset / change attempt |
| 4732 | Security | Member added to local group |
| 4740 | Security | Account locked out |
| 4776 | Security | NTLM credential validation |
| 1102 | Security | Audit log cleared |
| 10016 | System | DCOM permission warning (usually harmless noise) |

| Logon Type | Meaning |
|---|---|
| 2 | Interactive (keyboard) |
| 3 | Network (file share access) |
| 4 | Batch (scheduled task) |
| 5 | Service |
| 7 | Unlock |
| 10 | RemoteInteractive (RDP) |
| 11 | CachedInteractive (offline domain logon) |

### Other Log and Diagnostic File Locations

| Path | Contents |
|---|---|
| `C:\Windows\Logs\CBS\CBS.log` | Component-based servicing and `sfc` details |
| `C:\Windows\Logs\DISM\dism.log` | DISM operations |
| `C:\Windows\Panther\` | Setup and upgrade logs |
| `C:\Windows\INF\setupapi.dev.log` | Driver installation |
| `C:\Windows\Minidump\` and `C:\Windows\MEMORY.DMP` | Crash dumps |
| `C:\ProgramData\Microsoft\Windows\WER\` | Windows Error Reporting archives |
| `C:\Windows\System32\LogFiles\` | Firewall, HTTP.sys, and other component logs |
| `%TEMP%` | Installer logs, application temp logs |

```cmd
findstr /c:"[SR]" %windir%\Logs\CBS\CBS.log > "%userprofile%\Desktop\sfclogs.txt"
```

```powershell
Get-WindowsUpdateLog                      # converts update .etl traces to a readable file on the desktop
```

### Linux Logging Equivalents

| Linux | Windows |
|---|---|
| `/var/log/syslog` | Event Viewer > System / Application |
| `/var/log/auth.log` | Event Viewer > Security |
| `journalctl` | `Get-WinEvent`, `wevtutil` |
| `journalctl -u nginx` | `Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='Service Control Manager'}` |
| `tail -f` | `Get-Content -Wait`, Event Viewer attach task |
| `dmesg` | System log, `driverquery`, Reliability Monitor |

---

## Registry

The **Registry** is the hierarchical database holding configuration for Windows, drivers, services, and applications. There is no Linux equivalent; it replaces many files in `/etc` and dotfiles in `~`.

### Hives

| Root Key | Abbreviation | Contents |
|---|---|---|
| `HKEY_LOCAL_MACHINE` | `HKLM` | Machine-wide settings (hardware, software, services, SAM, security) |
| `HKEY_CURRENT_USER` | `HKCU` | Settings of the signed-in user (loaded from `NTUSER.DAT`) |
| `HKEY_CLASSES_ROOT` | `HKCR` | File associations and COM; merged view of `HKLM\Software\Classes` and `HKCU\Software\Classes` |
| `HKEY_USERS` | `HKU` | All loaded user profiles |
| `HKEY_CURRENT_CONFIG` | `HKCC` | Current hardware profile |

Hive files on disk: `C:\Windows\System32\config\` (`SAM`, `SECURITY`, `SOFTWARE`, `SYSTEM`, `DEFAULT`) and `C:\Users\<name>\NTUSER.DAT`.

### Value Types

| Type | Use |
|---|---|
| `REG_SZ` | String |
| `REG_EXPAND_SZ` | String with environment variables (`%SystemRoot%`) |
| `REG_MULTI_SZ` | List of strings |
| `REG_DWORD` | 32-bit number (flags, 0/1) |
| `REG_QWORD` | 64-bit number |
| `REG_BINARY` | Raw bytes |

### reg Command

```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion /v ProgramFilesDir
reg query HKCU\Software /s /f "MyApp"                      :: search
reg add HKCU\Software\MyApp /v Setting /t REG_DWORD /d 1 /f
reg add HKCU\Software\MyApp /v Name /t REG_SZ /d "value" /f
reg delete HKCU\Software\MyApp /v Setting /f
reg delete HKCU\Software\MyApp /f                           :: delete the whole key
reg export HKCU\Software\MyApp C:\backup\myapp.reg /y       :: backup before changes
reg import C:\backup\myapp.reg
reg save HKLM\SOFTWARE C:\backup\software.hiv
reg load HKU\TempHive C:\Users\old\NTUSER.DAT                :: mount another user's hive
reg unload HKU\TempHive
```

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion" | Select-Object ProductName, DisplayVersion, CurrentBuild
New-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting" -Value 1 -PropertyType DWord -Force
Remove-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting"
Get-ChildItem HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall | Get-ItemProperty | Select-Object DisplayName, DisplayVersion
```

### Important Registry Locations

| Path | Purpose |
|---|---|
| `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run` | Programs started at logon (all users) |
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | Programs started at logon (current user) |
| `HKLM\SYSTEM\CurrentControlSet\Services` | Service and driver configuration |
| `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion` | OS version information |
| `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall` | Installed programs list |
| `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Environment` | System environment variables |
| `HKCU\Environment` | User environment variables |
| `HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server` | RDP settings |
| `HKLM\SOFTWARE\Policies` and `HKCU\Software\Policies` | Group Policy settings written to the registry |
| `HKLM\SYSTEM\CurrentControlSet\Control\FileSystem` | NTFS behaviors (`LongPathsEnabled`) |

> **Always export a key (`reg export`) or create a restore point before editing the registry.** Incorrect changes can prevent Windows from booting. Never download and import unknown `.reg` files.

---

## Windows Update and Servicing

### Update Types

| Type | Description | Frequency |
|---|---|---|
| Cumulative update (quality) | Security and bug fixes, includes all previous fixes | Monthly, **Patch Tuesday** (second Tuesday) |
| Preview / optional update | Non-security fixes and features ahead of the next Patch Tuesday | Monthly, optional |
| Feature update | New Windows version (for example `25H2` to `26H2`) | Yearly |
| .NET and servicing stack updates | Framework and update engine | Bundled or separate |
| Driver and firmware updates | Delivered through Windows Update or vendor tools | Varies |
| Definition updates | Defender signatures | Several times per day |

### Management Options

| Tool | Scope |
|---|---|
| Settings > Windows Update | Single device |
| Windows Update for Business (via Intune / Group Policy) | Rings, deferrals, deadlines |
| WSUS | On-premises approval and distribution |
| Microsoft Configuration Manager (SCCM) | Enterprise patching and deployment |
| Windows Autopatch | Cloud-managed update rings |
| Insider Program channels | Canary, Dev, Beta, Release Preview for testing |

### Update Commands

```cmd
control update                                   :: open Windows Update (Settings)
wmic qfe list brief                              :: installed hotfixes (legacy)
wusa /uninstall /kb:5034123 /quiet /norestart    :: remove an update
DISM /Online /Get-Packages | findstr /i "KB"
systeminfo | findstr /i "KB"
```

```powershell
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 10
Get-WindowsUpdateLog
```

### Resetting Windows Update Components

Use when updates are stuck downloading, fail repeatedly, or show errors such as `0x80070002` or `0x8024xxxx`.

```cmd
net stop wuauserv
net stop bits
net stop cryptsvc
net stop msiserver
ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
ren C:\Windows\System32\catroot2 catroot2.old
net start cryptsvc
net start bits
net start msiserver
net start wuauserv
```

Then run `DISM /Online /Cleanup-Image /RestoreHealth` and `sfc /scannow`, and retry.

### Update Troubleshooting Checklist

| Check | Command or Action |
|---|---|
| Free disk space (at least 20 GB for feature updates) | `Get-Volume` |
| Pending reboot | Restart; check `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Auto Update\RebootRequired` |
| Corrupt component store | `DISM /Online /Cleanup-Image /RestoreHealth` |
| Corrupt system files | `sfc /scannow` |
| Services running | `wuauserv`, `BITS`, `CryptSvc`, `TrustedInstaller` not disabled |
| Time and date | `w32tm /resync` |
| Proxy / firewall blocking | `netsh winhttp show proxy` |
| Third-party antivirus interference | Temporarily disable or update |
| Logs | `Get-WindowsUpdateLog`, `C:\Windows\Logs\CBS\CBS.log` |
| Error code lookup | Search `0x8024...` code, or `DISM` log |

### Rolling Back

- **Uninstall a quality update:** Settings > Windows Update > Update history > Uninstall updates, or `wusa /uninstall /kb:ID`.
- **Go back to the previous version after a feature update:** Settings > System > Recovery > Go back (available for a limited number of days).
- **Safe Mode / WinRE:** Uninstall latest quality or feature update from **Troubleshoot > Advanced options**.

---

## Repair and Troubleshooting

### Troubleshooting Methodology for IT Support

```text
Identify    -> Gather symptoms, error text, when it started, what changed
Reproduce   -> Confirm the issue, find the scope (one user, one app, all users, all machines)
Isolate     -> Hardware vs OS vs driver vs app vs network vs account
Research    -> Event Viewer, logs, knowledge base, vendor docs
Hypothesize -> Most likely cause first, least invasive fix first
Test        -> Change ONE thing at a time, record it
Fix         -> Apply and verify with the user
Document    -> Ticket notes, root cause, knowledge base article
Prevent     -> Patch, monitor, educate, automate
```

Good questions to ask the user:

- What exactly happened and what is the exact error message (screenshot)?
- When did it start? What changed (update, new software, driver, move, password change)?
- Does it happen for other users, on other networks, or in Safe Mode?
- Does it happen every time or intermittently?

### System File Repair

Run in an elevated prompt. Run DISM first, then SFC.

```cmd
DISM /Online /Cleanup-Image /CheckHealth       :: quick flag check
DISM /Online /Cleanup-Image /ScanHealth        :: deeper scan
DISM /Online /Cleanup-Image /RestoreHealth     :: repair the component store (uses Windows Update)
DISM /Online /Cleanup-Image /RestoreHealth /Source:wim:D:\sources\install.wim:1 /LimitAccess
sfc /scannow                                    :: verify and repair protected system files
sfc /verifyonly                                 :: check without repairing
sfc /scanfile=C:\Windows\System32\kernel32.dll
```

| SFC Result | Meaning |
|---|---|
| No integrity violations | Files are intact |
| Found corrupt files and repaired them | Fixed; reboot |
| Found corrupt files but unable to fix some | Run DISM `/RestoreHealth` then SFC again; check CBS.log |
| Could not perform the requested operation | Run in Safe Mode or WinRE (`sfc /scannow /offbootdir=C:\ /offwindir=C:\Windows`) |

### Disk and Memory Checks

```cmd
chkdsk C: /f /r                          :: schedule at reboot for the system drive
mdsched                                  :: Windows Memory Diagnostic (reboots to test RAM)
```

```powershell
Get-PhysicalDisk | Select-Object FriendlyName, HealthStatus, OperationalStatus
Get-Volume | Where-Object DriveType -eq 'Fixed'
```

### Windows Recovery Environment (WinRE)

Ways to enter:

- Hold `Shift` while clicking **Restart**.
- `shutdown /r /o /t 0`
- Settings > System > Recovery > Advanced startup > Restart now.
- Interrupt boot three times (hard power off during the Windows logo).
- Boot from Windows installation USB > Repair your computer.

| WinRE Option | Purpose |
|---|---|
| Startup Repair | Automated boot repair |
| System Restore | Roll back to a restore point |
| Uninstall Updates | Remove latest quality or feature update |
| Command Prompt | `bootrec`, `chkdsk`, `diskpart`, `sfc`, `bcdedit`, `dism` |
| UEFI Firmware Settings | Open firmware setup |
| Startup Settings | Safe Mode, debugging, disable driver signature enforcement |
| Reset this PC | Reinstall Windows, keep or remove files |
| System Image Recovery | Restore a full disk image |

```cmd
reagentc /info                          :: WinRE status and location
reagentc /enable
```

### Safe Mode

Safe Mode loads minimal drivers and services. If the problem disappears there, suspect a driver, startup program, or third-party service.

```cmd
msconfig                                :: Boot tab > Safe boot (revert afterwards!)
bcdedit /set {default} safeboot minimal            :: Safe Mode
bcdedit /set {default} safeboot network            :: Safe Mode with Networking
bcdedit /deletevalue {default} safeboot            :: return to normal boot
```

### Boot Repair Commands

```cmd
bootrec /scanos
bootrec /fixmbr                         :: MBR systems
bootrec /fixboot                        :: boot sector (on UEFI may need access denied workaround via bcdboot)
bootrec /rebuildbcd                     :: rebuild the Boot Configuration Data
bcdedit /enum all                       :: show BCD entries

:: UEFI repair: rebuild boot files from the Windows folder
diskpart
  list vol
  select vol 2        :: the small FAT32 EFI System Partition
  assign letter=S
  exit
bcdboot C:\Windows /s S: /f UEFI
```

### System Restore and Reset

```cmd
rstrui                                   :: open System Restore wizard
systemreset                              :: Reset this PC
vssadmin list shadows
```

```powershell
Enable-ComputerRestore -Drive "C:\"
Checkpoint-Computer -Description "Before driver install" -RestorePointType MODIFY_SETTINGS
Get-ComputerRestorePoint
```

**Reset this PC** options: keep my files or remove everything; local reinstall or cloud download. An **in-place upgrade** (run `setup.exe` from a Windows ISO and choose to keep files and apps) repairs deep OS corruption while preserving data.

### Drivers and Devices

```cmd
devmgmt.msc                              :: Device Manager (yellow triangle = problem)
driverquery /v /fo table
pnputil /enum-drivers
pnputil /add-driver C:\drivers\net.inf /install
pnputil /delete-driver oem12.inf /uninstall /force
pnputil /scan-devices
sigverif                                 :: check for unsigned drivers
```

```powershell
Get-PnpDevice -Status ERROR
Get-PnpDevice -Class Net
```

| Driver Problem | Action |
|---|---|
| Device not recognized | Update driver from OEM, not only Windows Update; check cable or port |
| Problem started after update | Device Manager > Properties > Driver > Roll Back Driver |
| Blue screen caused by a driver | Check minidump for the faulting `.sys`, uninstall or replace it |
| Code 10, 28, 43 in Device Manager | Driver or hardware failure; reinstall driver, test another port |

### Blue Screen of Death (BSOD)

A Stop error occurs when kernel mode code hits a fatal problem (usually a driver, memory, or disk issue).

Information to capture:

- Stop code name (for example `DPC_WATCHDOG_VIOLATION`) and the "What failed" driver file.
- Dump files: `C:\Windows\Minidump\*.dmp`, `C:\Windows\MEMORY.DMP`.
- Event Viewer: System log, source `BugCheck` (Event 1001), `Kernel-Power` (Event 41).
- Reliability Monitor (`perfmon /rel`).

```cmd
sysdm.cpl                                :: Advanced > Startup and Recovery > Settings > Write debugging information
wmic recoveros get DebugInfoType         :: legacy query
```

Analysis tools: **WinDbg** (Microsoft Store), **BlueScreenView**, **WhoCrashed**. In WinDbg, run `!analyze -v` after loading the dump.

| Common Cause | Typical Evidence |
|---|---|
| Faulty or outdated driver | Stop code points to a `.sys` file (GPU, storage, antivirus, network) |
| Failing RAM | `MEMORY_MANAGEMENT`, random codes; fails `mdsched` or MemTest86 |
| Failing disk | Event IDs 7, 11, 55, 129, 153; `KERNEL_DATA_INPAGE_ERROR` |
| Overheating / power issue | Event 41 with no bugcheck, `WHEA_UNCORRECTABLE_ERROR` |
| Overclocking / bad BIOS settings | Random bugchecks, instability under load |
| Corrupt system files | `sfc` / `DISM` findings |
| Malware or a rootkit | Unexpected drivers, `Autoruns` findings |

### Slow Computer Checklist

| Check | Tool |
|---|---|
| High CPU or memory process | Task Manager > Processes |
| Disk at 100% | Resource Monitor > Disk; check antivirus scan, updates, failing HDD |
| Too many startup programs | Task Manager > Startup apps, Autoruns |
| Low free disk space | `Get-Volume`, Storage Sense, Disk Cleanup |
| Malware or adware | Defender full scan, Malwarebytes, Autoruns |
| Insufficient RAM | Task Manager > Performance > Memory, committed vs physical |
| Old HDD | Replace with SSD, `Get-PhysicalDisk` health |
| Driver or Windows Update running | Settings > Windows Update |
| Power plan | `powercfg /list`, use Balanced or High performance |
| Overheating throttling | HWiNFO, check fans |

### Common Application Problems

| Symptom | Fix |
|---|---|
| App crashes at start | Event Viewer > Application (Event 1000) for faulting module; reinstall; install Visual C++ Redistributables |
| `0xC000007B` | 32/64-bit mismatch: install matching Visual C++ runtimes and DirectX |
| `0xC0000135` | Missing DLL or .NET: install required .NET Framework / runtime |
| Program will not uninstall | Run Microsoft Program Install and Uninstall troubleshooter, remove from `Uninstall` registry key |
| File is in use | Resource Monitor > Handles; `handle.exe`; close process; restart Explorer |
| Corrupt user profile | Sign in as another admin; create new profile; copy data from old profile |
| Start menu or search broken | `sfc`, restart `explorer.exe`, re-register apps, create a new profile |

### Corrupt User Profile Recovery

```text
Symptom: "We can't sign in to your account" or temporary profile (C:\Users\TEMP)
Check:   Event Viewer > Application > User Profile Service (Event 1511, 1515)
Fix:     1. Sign in as another admin
         2. Backup C:\Users\<name> (robocopy)
         3. Remove the stale SID entry in HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList (key ending in .bak)
         4. Or create a new account and copy the data back
```

---

## Common Windows Issues and Fixes

Quick playbook for problems IT support sees every week. Changes are listed least invasive first.

### Shell, Start Menu and Search

| Symptom | Fix |
|---|---|
| Start menu, taskbar, or Settings will not open | Restart `explorer.exe`; run `sfc /scannow`; re-register shell packages (below); test with a new user profile |
| Search returns nothing or is slow | `Restart-Service WSearch`; Indexing Options > Advanced > **Rebuild**; run the Search troubleshooter |
| Microsoft Store will not open or download | `wsreset.exe`; re-register the Store (below); check date and time and proxy |
| Black screen with a cursor after sign-in | `Ctrl+Shift+Esc` > Run new task > `explorer.exe`; check `Shell` and `Userinit` (below) |
| Default apps keep resetting | Remove conflicting policy; set per-user defaults; check an imported default-association XML |

```powershell
# Re-register shell components (run as the affected user or admin)
Get-AppxPackage -AllUsers Microsoft.Windows.StartMenuExperienceHost | ForEach-Object { Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml" }
Get-AppxPackage -AllUsers Microsoft.WindowsStore | ForEach-Object { Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml" }
```

```cmd
:: Correct values for a black-screen-after-login check
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell      :: explorer.exe
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit   :: C:\Windows\system32\userinit.exe,
```

### High CPU, Memory or Disk: What Is That Process?

| Process | What It Is | What to Do |
|---|---|---|
| `MsMpEng.exe` (Antimalware Service Executable) | Defender scanning | Normal during scans or after updates; check for scan loops, scope exclusions carefully for build folders |
| `TiWorker.exe` / `TrustedInstaller.exe` | Windows servicing (updates, cleanup) | Let it finish; if stuck, `DISM /RestoreHealth`, reset update components |
| `SearchIndexer.exe` | Search indexing | Reduce indexed locations, rebuild index |
| `System` (PID 4) | Kernel and drivers | Investigate with WPR/WPA or Process Monitor; suspect storage and network drivers |
| `System interrupts` | Hardware interrupt and DPC time | Sustained high CPU points to a faulty device or driver; test with LatencyMon, unplug USB devices |
| `WmiPrvSE.exe` | WMI provider host | Find the caller in WMI-Activity log; `winmgmt /verifyrepository` |
| `svchost.exe` | Service host | Identify with `tasklist /svc`; or `Get-CimInstance Win32_Service \| Where ProcessId -eq <PID>` |
| `explorer.exe` | Shell | Faulty shell extension (use ShellExView), huge network folders, OneDrive |
| `dwm.exe` | Desktop compositor | GPU driver or display issue |
| `CompatTelRunner.exe` | Compatibility telemetry | Usually brief after updates |
| `OneDrive.exe` | Sync client | Initial sync or a huge folder; pause and review excluded folders |

Memory that grows until the system slows: check **Non-paged pool** in Task Manager > Performance > Memory and RAMMap. A growing pool usually means a leaking driver (look at `poolmon`, then update or remove the driver).

### Network Quick Fixes

| Symptom | Fix |
|---|---|
| "No internet, secured" / limited connectivity | `ipconfig /release && ipconfig /renew`, `ipconfig /flushdns`, forget and reconnect the Wi-Fi, update the NIC driver |
| Everything network-related broke after a VPN or security tool | `netsh winsock reset`, `netsh int ip reset`, restart; remove stale virtual adapters |
| Network reset needed | Settings > Network and internet > Advanced network settings > **Network reset** |
| Last-resort adapter reset | `netcfg -d` then reboot (removes and reinstalls adapters and loses static IP and VPN adapter settings) |
| Only one site fails with a certificate error | Check system date and time, proxy, and TLS inspection software |
| IP conflict | `arp -a`, find the duplicate MAC; fix by DHCP reservation |

### Printing Errors

| Error | Cause | Fix |
|---|---|---|
| `0x0000011b` | RPC authentication hardening after the 2021 print security updates | Update both print server and client; prefer connecting via a TCP/IP port. Temporary, **reduces security**: set `RpcAuthnLevelPrivacyEnabled` to `0` under `HKLM\SYSTEM\CurrentControlSet\Control\Print` on the print server (see Microsoft KB5005652) |
| `0x00000709` | Cannot set the default printer | Turn off "Let Windows manage my default printer"; `reg add "HKCU\Software\Microsoft\Windows NT\CurrentVersion\Windows" /v LegacyDefaultPrinterMode /t REG_DWORD /d 1 /f` |
| `0x00000bcb` | Driver install blocked or Point-and-Print issue | Install the driver locally, then add the printer on a TCP/IP port; check Point-and-Print policies |
| `0x0000007c` | Driver mismatch or corruption | Remove the printer and driver (`printui /s /t2`), install the OEM driver |
| Job stuck in queue | Corrupt spool file | Stop `spooler`, delete `spool\PRINTERS\*`, start `spooler` |

### Services Will Not Start

| Error | Meaning | Fix |
|---|---|---|
| `1053` | Service did not respond to the start request in time | Check the application log; dependency or slow startup; increase `ServicesPipeTimeout` only if justified |
| `1058` | Service disabled or no enabled devices | `sc config svc start= demand` |
| `1067` | Process terminated unexpectedly | Application log (Event 1000, 7034), check the executable and dependencies |
| `1068` | Dependency service or group failed to start | `sc qc svc` then `sc enumdepend svc`; start dependencies first |
| `1069` | Logon failure | Service account password changed or account locked; fix in `services.msc` > Log On |

### Activation Errors

| Code | Meaning | Fix |
|---|---|---|
| `0xC004F074` | KMS host unreachable | Check DNS SRV `_vlmcs._tcp`, firewall to TCP 1688, then `slmgr /ato` |
| `0x8007232B` | DNS name does not exist (KMS discovery) | Fix the SRV record or set `slmgr /skms host:1688` |
| `0xC004F050` | Invalid product key | Verify the key and edition |
| `0xC004C003` | Key blocked | Replace the key; contact the licensing owner |
| `0x803F7001` | No valid digital license found | Link the license to the Microsoft account; reactivate after hardware changes |

### Remote Desktop Errors

| Symptom | Cause | Fix |
|---|---|---|
| "Remote Desktop can't connect" | Disabled, firewall, wrong IP/name, service stopped | `Test-NetConnection host -Port 3389`; enable RDP; start `TermService`; firewall rule |
| "Your credentials did not work" | Wrong account, not in **Remote Desktop Users**, NLA rejecting, stale saved credentials | Add user to group; `cmdkey /delete:host`; use `DOMAIN\user` |
| "The connection was denied because the user account is not authorized for remote login" | Missing "Allow log on through Remote Desktop Services" right | `secpol.msc` > User Rights Assignment; or Remote Desktop Users membership |
| CredSSP "encryption oracle remediation" | One side is unpatched | Patch both client and server (do not weaken the policy permanently) |
| Session limit or stuck session | Disconnected sessions hold the slot | `query session /server:host`, `logoff <id> /server:host` or `reset session <id>` |
| Slow, laggy RDP | Network, high colour depth, GPU/codecs | Lower experience settings, check packet loss with `pathping`, update graphics drivers |

### Disk Space Mysteries

| Item | Explanation | Action |
|---|---|---|
| `WinSxS` looks huge | Hard links make Explorer overstate its size | `DISM /Online /Cleanup-Image /AnalyzeComponentStore`, then `StartComponentCleanup`; never delete it manually |
| `System Volume Information` | Restore points and shadow copies | `vssadmin list shadowstorage`; resize with `vssadmin resize shadowstorage` |
| `hiberfil.sys` / `pagefile.sys` | Hibernation and virtual memory | `powercfg /h off` removes hibernation (also disables Fast Startup); size the pagefile in `sysdm.cpl` |
| `C:\Windows\Installer` | Cached MSI and patches | **Do not delete** |
| `C:\Windows\SoftwareDistribution\Download` | Update downloads | Safe to clear after stopping `wuauserv` and `bits` |
| Unknown usage | Hidden or per-user data | WinDirStat or TreeSize; include `AppData`, `ProgramData`, and Docker/WSL virtual disks |

### Access Problems Even as Administrator

| Symptom | Cause | Fix |
|---|---|---|
| "Access is denied" on system files | `TrustedInstaller` owns them | Do not take ownership casually; use `takeown` and `icacls` only if you must, then restore ownership |
| Elevated prompt cannot see mapped drives | Elevated token uses a different logon session | Map the drive in the elevated session, or set `EnableLinkedConnections` = `1` (DWORD) under `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System` and reboot |
| Cannot delete a file named `con`, `aux`, or one with a trailing dot or space | Reserved or illegal Win32 names | `del "\\?\C:\path\con"` or `rd /s /q "\\?\C:\path\name."` |
| Cannot write to USB or disk ("write protected") | Read-only attribute or physical switch | `diskpart` > `select disk N` > `attributes disk clear readonly` |
| Folder changes blocked unexpectedly | Defender **Controlled Folder Access** | Check Windows Security > Ransomware protection and protection history |
| Windows Hello PIN fails with `0x80090016` ("Keyset does not exist") | Corrupt NGC container | Remove and re-create the PIN. As a last resort, after `takeown` and `icacls`, clear `C:\Windows\ServiceProfiles\LocalService\AppData\Local\Microsoft\NGC` (this resets Hello PINs for **all** users on the device) |

### Audio, Bluetooth and Peripherals

| Symptom | Fix |
|---|---|
| No sound | Check default device (`mmsys.cpl`); restart `Audiosrv` and `AudioEndpointBuilder`; disable enhancements and exclusive mode; reinstall the driver |
| Bluetooth device will not pair | Remove and re-pair; restart `bthserv`; update the adapter driver; disable USB selective suspend |
| USB device keeps disconnecting | Another port, no hub, disable selective suspend, update chipset drivers |
| External monitor not detected | `Win+P`, `Win+Ctrl+Shift+B`, update GPU driver, change cable and port |

---

## Boot Process

### Complete Boot Flow

```text
Power ON
  |
UEFI firmware (or legacy BIOS) -> POST -> finds boot device
  |
Windows Boot Manager (bootmgfw.efi / bootmgr) -> reads BCD (Boot Configuration Data)
  |
Windows OS Loader (winload.efi / winload.exe) -> loads kernel, HAL, boot drivers, SYSTEM hive
  |
Kernel (ntoskrnl.exe) -> initializes executive, drivers, starts Session Manager
  |
smss.exe -> creates sessions, starts csrss.exe, wininit.exe, winlogon.exe
  |
wininit.exe -> services.exe (Service Control Manager), lsass.exe
winlogon.exe -> LogonUI -> credentials validated by lsass.exe
  |
userinit.exe -> explorer.exe -> desktop, Startup apps
```

### Firmware: BIOS versus UEFI

| BIOS (Legacy) | UEFI |
|---|---|
| MBR partition table | GPT partition table |
| 2 TB boot disk limit | Larger disks |
| Slower, text-mode setup | Faster boot, graphical setup |
| No Secure Boot | Secure Boot verifies signed boot components |
| Boot code in MBR | Boot files in EFI System Partition (`\EFI\Microsoft\Boot\`) |

### Supporting Technologies

| Technology | Purpose |
|---|---|
| Secure Boot | Prevents unsigned bootloaders and rootkits from loading |
| TPM 2.0 | Stores keys, measures boot (Measured Boot), unlocks BitLocker |
| Fast Startup | Hybrid shutdown: logs off users and hibernates the kernel session for fast boot |
| Hibernation (`hiberfil.sys`) | Saves RAM to disk |
| Boot Configuration Data (BCD) | Boot entries store (replaces `boot.ini`) |

> **Fast Startup gotcha:** "Shut down" is a hybrid shutdown, so some issues (driver state, uptime counters, pending updates) survive it. **Restart** performs a full kernel reload, which is why restarting fixes problems that shutting down does not. Hold `Shift` while clicking Shut down for a full shutdown, or run `powercfg /h off` to disable Fast Startup.

```cmd
bcdedit                                  :: view boot entries
bcdedit /set {current} description "Windows 11 Main"
msconfig                                 :: Boot tab: timeout, safe boot, no GUI boot, boot log
systeminfo | findstr /C:"BIOS" /C:"Boot"
```

### Linux Comparison

| Linux | Windows |
|---|---|
| GRUB | Windows Boot Manager |
| `/boot/grub/grub.cfg` | BCD store |
| initramfs | Boot-start drivers loaded by `winload` |
| systemd (PID 1) | `smss.exe` / `wininit.exe` / `services.exe` |
| Targets / runlevels | Safe Mode, normal, services startup types |
| `dmesg` | Event Viewer System log, boot log (`ntbtlog.txt`) |

---

## Security Fundamentals

### Security Layers

| Layer | Controls |
|---|---|
| Identity | Strong passwords, MFA, Windows Hello, least privilege, LAPS |
| Device | TPM, Secure Boot, BitLocker, patching, Defender |
| Network | Windows Firewall, segmentation, VPN, disabling SMBv1 and unused services |
| Application | UAC, AppLocker / WDAC, SmartScreen, signed software |
| Data | NTFS ACLs, EFS, BitLocker, backups, DLP, sensitivity labels |
| Monitoring | Event logs, Sysmon, Defender for Endpoint, SIEM |

### Microsoft Defender

```cmd
"%ProgramFiles%\Windows Defender\MpCmdRun.exe" -Scan -ScanType 1       :: quick scan
"%ProgramFiles%\Windows Defender\MpCmdRun.exe" -SignatureUpdate
```

```powershell
Get-MpComputerStatus | Select-Object AMServiceEnabled, RealTimeProtectionEnabled, AntivirusSignatureLastUpdated
Update-MpSignature
Start-MpScan -ScanType QuickScan
Get-MpThreatDetection
Add-MpPreference -ExclusionPath "C:\dev\large-build-cache"     # use sparingly, understand the risk
```

Components: real-time protection, cloud-delivered protection, SmartScreen, tamper protection, controlled folder access (anti-ransomware), attack surface reduction rules, Windows Security app (`windowsdefender:`).

### Account Protection

| Control | Details |
|---|---|
| Least privilege | Daily work with a standard account; admin account only for administration |
| Windows LAPS | Automatically rotates and stores the local Administrator password in AD / Entra ID |
| Account lockout policy | Lock after N failed attempts (`net accounts`, `secpol.msc`) |
| MFA / Windows Hello for Business | Phishing-resistant sign-in |
| Disable unused accounts | `net user guest /active:no` |
| Credential Guard | VBS isolates NTLM hashes and Kerberos tickets from `lsass.exe` |

### Authentication Protocols

| Protocol | Notes |
|---|---|
| Kerberos | Default in domains; ticket-based; needs DNS and time sync (5-minute skew) |
| NTLM | Legacy challenge-response; used for workgroups and IP-based access; being phased out |
| NTLMv1 | Insecure; disable |
| Certificate / smart card | Strong, PKI-based |
| OAuth / OpenID Connect / SAML | Cloud and web identity (Entra ID) |

```cmd
klist                          :: list Kerberos tickets
klist purge                    :: clear tickets (then re-authenticate)
```

### Hardening Checklist

```text
- Keep Windows and applications patched (automate with WUfB, Intune, WSUS)
- Enable BitLocker on all laptops, store recovery keys centrally
- Enable Secure Boot and TPM; require them for deployment
- Disable SMBv1:   DISM /Online /Disable-Feature /FeatureName:SMB1Protocol
- Disable RDP unless required; never expose TCP 3389 to the internet (use VPN / RD Gateway / Bastion)
- Enforce MFA for administrative and remote access
- Use LAPS for local admin passwords; avoid shared admin accounts
- Remove users from local Administrators unless required
- Restrict PowerShell logging gaps: enable Script Block and Module logging
- Use AppLocker or WDAC to allow only trusted software
- Turn on Windows Firewall for all profiles
- Audit logon failures and privilege use; forward logs to a central SIEM
- Follow CIS Benchmarks or Microsoft Security Baselines
- Maintain tested backups (3-2-1 rule), including offline or immutable copies
```

### Malware Response Basics

| Step | Action |
|---|---|
| Contain | Disconnect from network (Wi-Fi off, unplug cable), do not power off if forensic data matters |
| Identify | Process Explorer, Autoruns, TCPView, Defender detections, Event Viewer |
| Eradicate | Full offline scan (Defender Offline), remove persistence (Run keys, tasks, services) |
| Recover | Restore from known-good backup; reimage if rootkit or high-impact compromise suspected |
| Credentials | Reset passwords and revoke sessions for any account used on the machine |
| Lessons | Find the entry point (phishing, exposed RDP, unpatched software) and close it |

> Ransomware precautions: offline and immutable backups, patching, MFA, disabling macros from the internet, Controlled Folder Access, and network segmentation.

### Phishing and Social Engineering Awareness

IT support staff are frequent targets. Verify identity before password resets, use call-back procedures, and never accept remote-control tools from unsolicited callers.

---

## Active Directory and Enterprise Management

### Active Directory Domain Services (AD DS)

| Term | Meaning |
|---|---|
| Domain | Administrative boundary with a shared directory (`corp.example.com`) |
| Domain Controller (DC) | Server holding a copy of the directory, authenticates users |
| Forest / Tree | Collection of domains sharing schema and trust |
| Organizational Unit (OU) | Container for users, computers, and groups; target for Group Policy |
| Global Catalog | Searchable partial replica of all objects in the forest |
| FSMO roles | Schema Master, Domain Naming Master (forest); PDC Emulator, RID Master, Infrastructure Master (domain) |
| SYSVOL / NETLOGON | Shared folders holding GPO files and logon scripts |
| Site | Physical network location used for replication and client DC selection |

AD depends on **DNS**: clients find DCs using SRV records, so most domain-join and logon problems are DNS problems.

### Group Types and Scope

| Scope | Can Contain | Used For |
|---|---|---|
| Domain Local | Users and groups from any domain | Assigning permissions to resources |
| Global | Users from the same domain | Grouping users by role |
| Universal | Users and groups from any domain in the forest | Cross-domain grouping |

Common strategy **AGDLP**: Accounts go into Global groups, Global groups into Domain Local groups, Domain Local groups receive Permissions.

### Group Policy

Group Policy Objects (GPOs) apply settings to users and computers. Processing order: **Local, Site, Domain, OU** (LSDOU), where later policies override earlier ones unless **Enforced**.

```cmd
gpupdate /force                     :: refresh computer and user policy
gpresult /r                         :: summary of applied GPOs
gpresult /h C:\report.html          :: detailed report
gpresult /scope computer /v
rsop.msc                            :: resultant set of policy (GUI)
gpmc.msc                            :: Group Policy Management Console (RSAT)
```

Clients refresh policy about every 90 minutes (plus a random offset); domain controllers every 5 minutes.

### Domain Commands

```cmd
echo %LOGONSERVER%                              :: DC that authenticated you
nltest /dsgetdc:corp.example.com                :: locate a domain controller
nltest /sc_verify:corp.example.com              :: verify secure channel
net time /domain
net user jsmith /domain
net group "Domain Admins" /domain
nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.example.com
setspn -L SERVERNAME                            :: service principal names
dsregcmd /status                                :: Entra ID / hybrid join status
whoami /fqdn
```

```powershell
Test-ComputerSecureChannel -Verbose
Test-ComputerSecureChannel -Repair -Credential (Get-Credential)      # fix "trust relationship failed"
Add-Computer -DomainName corp.example.com -Credential (Get-Credential) -Restart
Get-ADUser jsmith -Properties LockedOut, PasswordLastSet         # needs RSAT ActiveDirectory module
Unlock-ADAccount -Identity jsmith
Set-ADAccountPassword -Identity jsmith -Reset
```

> RSAT (Remote Server Administration Tools) provides ADUC (`dsa.msc`), DNS, DHCP, and GPMC consoles on a Windows client: `Add-WindowsCapability -Online -Name Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0`.

### Common Domain Problems

| Symptom | Likely Cause | Fix |
|---|---|---|
| "The trust relationship between this workstation and the primary domain failed" | Machine account password out of sync (restored snapshot, long offline) | `Test-ComputerSecureChannel -Repair`, or rejoin domain |
| Cannot find domain during join | DNS not pointing at a domain controller | Set DNS to DC, `ipconfig /flushdns`, `nslookup` SRV records |
| Account locked repeatedly | Stale saved credentials, mapped drives, mobile device, service with old password | Event 4740 shows the source computer; clear Credential Manager |
| Slow logon | Many GPOs, logon scripts, roaming profile size, DNS issues | `gpresult`, Event Viewer GroupPolicy log |
| Kerberos errors | Time skew, SPN duplicates, DNS | `w32tm /resync`, `setspn -X` |
| "Access is denied" on a share | NTFS and share permissions, stale token | Re-logon, `whoami /groups`, `icacls` |

### Microsoft Entra ID, Intune and Cloud Management

| Item | Meaning |
|---|---|
| Entra ID (formerly Azure Active Directory) | Cloud identity provider for Microsoft 365 and SaaS apps |
| Entra joined | Device joined only to the cloud directory |
| Hybrid joined | Device joined to on-premises AD and registered in Entra ID |
| Entra registered | Personal device registered (BYOD) |
| Intune | Mobile Device Management (MDM) and Mobile Application Management |
| Autopilot | Zero-touch provisioning of new devices |
| Conditional Access | Policies requiring MFA or compliant device for access |
| Single Sign-On (SSO) | One sign-in for many applications |

### Imaging and Deployment

| Tool | Purpose |
|---|---|
| Windows Autopilot | Cloud-based device provisioning for new hardware |
| MDT / Configuration Manager | Task-sequence based imaging and software deployment |
| `sysprep /generalize /oobe /shutdown` | Prepare a reference image |
| Unattend answer files | Automate setup (`unattend.xml`) |
| DISM image commands | Capture, apply, and service `.wim` images |

---

## Windows Server Roles Quick Reference

Roles and features most IT support engineers meet. Run from an elevated PowerShell on the server.

| Role | Purpose | Install |
|---|---|---|
| AD DS | Directory and authentication | `Install-WindowsFeature AD-Domain-Services -IncludeManagementTools` |
| DNS | Name resolution (required by AD) | `Install-WindowsFeature DNS -IncludeManagementTools` |
| DHCP | Automatic IP assignment | `Install-WindowsFeature DHCP -IncludeManagementTools` |
| File Server / FSRM / DFS | Shares, quotas, namespaces | `Install-WindowsFeature FS-FileServer, FS-Resource-Manager, FS-DFS-Namespace` |
| Print Server | Central printers | `Install-WindowsFeature Print-Server` |
| IIS (Web Server) | Websites and web apps | `Install-WindowsFeature Web-Server -IncludeManagementTools` |
| Hyper-V | Virtualization | `Install-WindowsFeature Hyper-V -IncludeManagementTools -Restart` |
| Remote Desktop Services | Session hosts and gateway | Installed through Server Manager roles |

```powershell
Get-WindowsFeature | Where-Object Installed
Get-WindowsFeature *DHCP*
```

**Server Core** (no desktop) is the recommended install for infrastructure roles. Manage it with `sconfig`, Windows Admin Center, RSAT, or PowerShell remoting.

### DNS Server

```powershell
Add-DnsServerPrimaryZone -ZoneName "corp.local" -ReplicationScope Domain          # AD-integrated zone
Add-DnsServerResourceRecordA -Name "srv01" -ZoneName "corp.local" -IPv4Address "10.0.0.10"
Add-DnsServerResourceRecordCName -Name "files" -HostNameAlias "srv01.corp.local" -ZoneName "corp.local"
Add-DnsServerForwarder -IPAddress 1.1.1.1
Get-DnsServerResourceRecord -ZoneName "corp.local"
Clear-DnsServerCache
```

Clients must use the **internal** DNS server (not a public one) to find domain controllers. This is the cause of most domain-join failures.

### DHCP Server

```powershell
Add-DhcpServerv4Scope -Name "LAN" -StartRange 192.168.1.100 -EndRange 192.168.1.199 -SubnetMask 255.255.255.0 -State Active
Set-DhcpServerv4OptionValue -ScopeId 192.168.1.0 -Router 192.168.1.1 -DnsServer 192.168.1.2 -DnsDomain corp.local
Add-DhcpServerv4Reservation -ScopeId 192.168.1.0 -IPAddress 192.168.1.30 -ClientId "AA-BB-CC-DD-EE-FF" -Description "Printer"
Add-DhcpServerInDC                            # authorize the server in AD (required in a domain)
Get-DhcpServerv4Lease -ScopeId 192.168.1.0
```

Only one DHCP server should serve a subnet. A rogue router with DHCP enabled is a classic cause of "wrong IP or gateway on some PCs".

### IIS Basics

```cmd
%windir%\system32\inetsrv\appcmd list site
%windir%\system32\inetsrv\appcmd list apppool
%windir%\system32\inetsrv\appcmd recycle apppool /apppool.name:"DefaultAppPool"
iisreset
```

| IIS Error | Meaning |
|---|---|
| 401 | Authentication failed or missing |
| 403 | Forbidden: permissions on the folder or directory browsing disabled |
| 404 | Not found: wrong path or missing handler/MIME type |
| 500 | Application error: check Failed Request Tracing and the Application log |
| 503 | Application pool stopped: check identity, rapid-fail protection |

Logs: `C:\inetpub\logs\LogFiles`. App pool identity (`IIS AppPool\<name>`) needs NTFS rights on the site folder.

### Domain Controller Health

```cmd
dcdiag /q                           :: show only failures
dcdiag /test:dns /v
repadmin /replsummary               :: replication health overview
repadmin /showrepl                  :: per-partner status and last errors
repadmin /syncall /AdeP             :: force replication
nltest /dclist:corp.local
netdom query fsmo                   :: who holds the FSMO roles
```

```powershell
Get-ADDomainController -Filter * | Select Name, IPv4Address, IsGlobalCatalog, OperatingSystem
```

Typical causes of replication failures: DNS misconfiguration, time skew over 5 minutes, blocked ports (TCP 135, 389, 445, 88, dynamic RPC), and a long-offline DC (lingering objects; do not reconnect a DC that was offline beyond the tombstone lifetime without guidance).

---

## Remote Access and Administration

### Remote Desktop (RDP)

| Fact | Details |
|---|---|
| Port | TCP/UDP 3389 |
| Host editions | Pro, Enterprise, Education, Server (Home cannot host) |
| Authentication | Network Level Authentication (NLA) recommended |
| Access control | Member of Administrators or **Remote Desktop Users** |
| Security | Never expose 3389 directly to the internet; use VPN, RD Gateway, or a bastion |

```cmd
mstsc /v:server01
mstsc /v:server01 /admin                :: connect to the admin session
mstsc /v:server01 /w:1920 /h:1080
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
netsh advfirewall firewall set rule group="remote desktop" new enable=Yes
net localgroup "Remote Desktop Users" hitesh /add
query user /server:server01
logoff 3 /server:server01
```

### Remote Command Execution

```cmd
psexec \\PC01 -s cmd                       :: Sysinternals; run as SYSTEM on a remote machine
psexec \\PC01 ipconfig /all
shutdown /r /m \\PC01 /t 30
tasklist /s PC01 /u DOMAIN\admin
sc \\PC01 query Spooler
schtasks /query /s PC01
```

```powershell
Enable-PSRemoting -Force                            # on the target (admin)
Enter-PSSession -ComputerName PC01 -Credential DOMAIN\admin
Invoke-Command -ComputerName PC01 -ScriptBlock { Get-Service Spooler }
Get-Service -ComputerName PC01 -Name Spooler
```

WinRM listens on TCP 5985 (HTTP) / 5986 (HTTPS).

### OpenSSH Server on Windows

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic
New-NetFirewallRule -Name sshd -DisplayName "OpenSSH Server" -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```

| Item | Location |
|---|---|
| Server config | `C:\ProgramData\ssh\sshd_config` |
| User keys | `C:\Users\<name>\.ssh\authorized_keys` |
| Admin account keys | `C:\ProgramData\ssh\administrators_authorized_keys` (must have restricted ACL: only Administrators and SYSTEM) |
| Default shell | Set `DefaultShell` in `HKLM\SOFTWARE\OpenSSH` (cmd.exe by default) |

### Other Remote Tools

| Tool | Use |
|---|---|
| Quick Assist | Built-in remote help session with a code |
| Remote Assistance (`msra`) | Legacy invitation-based help |
| Windows Admin Center | Browser-based server management |
| RSAT / MMC remote connect | `compmgmt.msc` > Connect to another computer |
| VPN (Always On VPN, third-party) | Secure remote network access |
| Intune remote actions | Sync, wipe, restart, remote help |

---

## Windows for Developers

### Windows Subsystem for Linux (WSL)

WSL2 runs a real Linux kernel in a lightweight VM, integrated with Windows.

```cmd
wsl --install                       :: enables features and installs default Ubuntu
wsl --list --online                 :: available distributions
wsl --install -d Ubuntu-24.04
wsl -l -v                           :: installed distros and WSL version
wsl --set-default-version 2
wsl --set-version Ubuntu 2
wsl -d Ubuntu                       :: start a specific distro
wsl --shutdown                      :: stop all distros and the WSL VM
wsl --update
wsl --status
wsl --export Ubuntu D:\backup\ubuntu.tar
wsl --import Ubuntu-Dev D:\wsl\dev D:\backup\ubuntu.tar
wsl --unregister Ubuntu             :: deletes the distro and its data
```

| Topic | Details |
|---|---|
| Windows drives from Linux | `/mnt/c`, `/mnt/d` |
| Linux files from Windows | `\\wsl$\Ubuntu` or `\\wsl.localhost\Ubuntu` in Explorer |
| Open Explorer from WSL | `explorer.exe .` |
| Path conversion | `wslpath 'C:\Users\me'` / `wslpath -w ~` |
| Performance | Keep project files **inside the Linux filesystem** (`~/project`), not under `/mnt/c`, for much faster I/O |
| Config for resources | `%UserProfile%\.wslconfig` (memory, processors, swap) |
| Per-distro config | `/etc/wsl.conf` (`[boot] systemd=true`) |
| Localhost networking | Services in WSL are reachable from Windows via `localhost` in most setups; mirrored networking mode is available on recent builds |

```ini
# %UserProfile%\.wslconfig
[wsl2]
memory=8GB
processors=4
swap=4GB
```

### Developer Tooling

```cmd
winget install Git.Git
winget install Microsoft.VisualStudioCode
winget install Microsoft.WindowsTerminal
winget install Docker.DockerDesktop
winget install OpenJS.NodeJS.LTS
winget install Python.Python.3.13
winget install GoLang.Go
winget install Microsoft.OpenJDK.21
winget install Kubernetes.kubectl
winget install Hashicorp.Terraform
winget install Microsoft.AzureCLI
winget install Amazon.AWSCLI
winget install Microsoft.Sysinternals.Suite
winget install Microsoft.PowerToys
```

| Windows Developer Feature | Purpose |
|---|---|
| Developer Mode (Settings > System > For developers) | Create symlinks without admin, sideload apps, enable `sudo` |
| Dev Drive | ReFS-based volume tuned for build performance, with Defender performance mode |
| Windows Terminal | Multi-shell tabs and panes |
| PowerToys | FancyZones, PowerToys Run, text extractor, file locksmith (shows what locks a file) |
| Windows Sandbox | Disposable clean VM for testing untrusted software |

### Git on Windows

```cmd
git config --global core.autocrlf true         :: Windows: checkout CRLF, commit LF
git config --global core.longpaths true        :: avoid 260-character path failures
git config --global core.fileMode false        :: ignore the Unix executable bit on NTFS
git config --global credential.helper manager  :: Git Credential Manager (HTTPS tokens, OAuth)
git update-index --chmod=+x scripts/deploy.sh  :: record the executable bit explicitly
```

Recommended `.gitattributes` for cross-platform teams:

```text
* text=auto
*.sh   text eol=lf
*.bash text eol=lf
*.bat  text eol=crlf
*.cmd  text eol=crlf
*.ps1  text eol=crlf
Dockerfile text eol=lf
```

### Windows-Specific Development Gotchas

| Issue | Detail |
|---|---|
| CRLF in shell scripts | `bad interpreter: /bin/bash^M` inside Linux/Docker; fix with `.gitattributes`, `dos2unix`, or `sed -i 's/\r$//' file` |
| Case-insensitive filesystem | `File.js` and `file.js` collide; Linux CI may then fail on imports that worked locally. Per-directory case sensitivity is possible: `fsutil file setCaseSensitiveInfo C:\repo enable` |
| File locking | Windows prevents deleting or replacing open files; use Resource Monitor, Process Explorer, or PowerToys File Locksmith |
| Path length | Enable `LongPathsEnabled` and `git config core.longpaths true`; deep `node_modules` trees are common triggers |
| Path separators | Use platform-independent path APIs (`path.join`, `os.path`, `Path`) |
| PATH delimiter | `;` on Windows, `:` on Linux |
| Antivirus scanning | Real-time scanning slows builds (`node_modules`, `.git`); use Dev Drive or carefully scoped exclusions |
| Symlinks | Need Developer Mode or admin; some tools silently copy instead |
| Reserved names | A file named `con`, `aux`, or `nul` breaks checkouts on Windows |
| Port conflicts | `netstat -ano \| findstr :3000` then `taskkill /pid <PID> /f` |
| Excluded port ranges | Hyper-V and WSL may reserve ports: `netsh int ipv4 show excludedportrange protocol=tcp` |
| Docker volume performance | Mount code from the WSL2 filesystem, not `C:\` |
| Python launcher | `py -3.13 script.py`, `py -0` lists interpreters; manage environments with `venv` |
| Node versions | Use `nvm-windows`, `fnm`, or install Node inside WSL |
| Execution policy | PowerShell tools like `npm.ps1` can be blocked; set `RemoteSigned` for CurrentUser |
| Console encoding | `chcp 65001` for UTF-8 output in legacy console |

### Docker on Windows

| Mode | Description |
|---|---|
| Docker Desktop with WSL2 backend | Default; runs Linux containers fast |
| Linux containers | The common case; Windows hosts run a Linux VM |
| Windows containers | Run Windows Server Core / Nano Server images; needs process or Hyper-V isolation |
| Hyper-V backend | Older option; WSL2 is preferred |

Docker Desktop has commercial licensing terms for larger organizations; alternatives include Rancher Desktop, Podman Desktop, or Docker Engine installed directly in WSL.

```cmd
docker version
docker run --rm -it ubuntu:24.04 bash
docker run -p 8080:80 -v "%cd%":/usr/share/nginx/html nginx
```

### Windows Application Types Developers Meet

| Type | Notes |
|---|---|
| Win32 / native `.exe` | Traditional desktop apps |
| .NET (Framework, .NET 8+) | Managed apps, services, web APIs |
| UWP / WinUI / MSIX | Modern packaged apps |
| Windows Services | Background executables registered with SCM |
| Scheduled Tasks | Background jobs without services |
| IIS-hosted sites | Web server role: `inetmgr`, `%windir%\System32\inetsrv\config\applicationHost.config` |
| COM / DCOM | Legacy inter-process objects (`dcomcnfg`) |

---

## Virtualization and Containers

### Hyper-V

Built-in type-1 hypervisor on Pro, Enterprise, Education, and Server.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
Get-VM
New-VM -Name "Test01" -MemoryStartupBytes 4GB -Generation 2 -NewVHDPath D:\VMs\Test01.vhdx -NewVHDSizeBytes 60GB
Start-VM Test01
Checkpoint-VM -Name Test01 -SnapshotName "Before update"
Get-VMSwitch
```

```cmd
virtmgmt.msc                       :: Hyper-V Manager
systeminfo | findstr /i "hyper-v"  :: virtualization requirements
```

| Concept | Details |
|---|---|
| Generation 1 | Legacy BIOS VM |
| Generation 2 | UEFI VM, Secure Boot, faster boot |
| VHDX | Virtual disk format, up to 64 TB |
| Checkpoints | Point-in-time VM state; do not keep long-term |
| External vSwitch | Bridge to the physical network |
| Internal vSwitch | VM and host only |
| Private vSwitch | VMs only |
| Default Switch | NAT with built-in DHCP for quick use |
| Nested virtualization | `Set-VMProcessor -VMName X -ExposeVirtualizationExtensions $true` |

> Hyper-V, WSL2, Windows Sandbox, and virtualization-based security all use the Windows hypervisor. Modern VirtualBox and VMware support running alongside it, with reduced performance.

### Windows Sandbox

A disposable, clean desktop for testing untrusted files. Enable with `Containers-DisposableClientVM` (Pro, Enterprise, Education). Everything is deleted when the window closes.

### Containers Overview

| Item | VM | Container |
|---|---|---|
| Kernel | Own guest OS kernel | Shares the host kernel (Windows containers) or a Linux VM kernel (via WSL2) |
| Isolation | Strong (hardware virtualization) | Process-level, optional Hyper-V isolation |
| Startup | Tens of seconds | Seconds |

---

## Power, Printers and Peripherals

### Power Management

```cmd
powercfg /list                         :: available power plans
powercfg /getactivescheme
powercfg /setactive SCHEME_MIN         :: High performance (SCHEME_BALANCED, SCHEME_MAX = Power saver)
powercfg /batteryreport /output "%USERPROFILE%\Desktop\battery.html"
powercfg /energy                       :: 60-second energy efficiency diagnostic (energy-report.html)
powercfg /a                            :: available sleep states
powercfg /requests                     :: what is preventing sleep
powercfg /lastwake                     :: what woke the PC
powercfg /devicequery wake_armed       :: devices allowed to wake the PC
powercfg /h off                        :: disable hibernation (also disables Fast Startup)
powercfg /sleepstudy                   :: Modern Standby drain report
```

| Sleep State | Behavior |
|---|---|
| Sleep (S3) / Modern Standby | RAM stays powered, instant resume |
| Hibernate (S4) | RAM written to `hiberfil.sys`, power off |
| Hybrid sleep | Sleep with a hibernation backup |
| Shutdown | Full power off (hybrid with Fast Startup) |

### Printing

```cmd
net stop spooler
del /q /f /s %systemroot%\System32\spool\PRINTERS\*
net start spooler                       :: clears stuck print jobs
printui /s /t2                          :: print server properties, drivers
rundll32 printui.dll,PrintUIEntry /il   :: Add Printer wizard
printmanagement.msc                     :: Print Management (Pro and above / RSAT)
control printers                        :: Devices and Printers
```

```powershell
Get-Printer
Get-PrintJob -PrinterName "Office-Printer"
Remove-PrintJob -PrinterName "Office-Printer" -ID 3
Add-Printer -Name "Office-Printer" -DriverName "HP Universal Printing PCL 6" -PortName "IP_10.0.0.50"
Restart-Service Spooler
```

| Printing Problem | Typical Fix |
|---|---|
| Job stuck, printer shows Printing | Clear spooler files (above) |
| Driver issue after update | Remove printer and driver (`printui /s`), install OEM driver |
| Cannot connect to shared printer | Check name resolution, `\\server\printer` access, point-and-print policy, credential manager |
| Printer offline | Check IP and port (TCP 9100), power, "Use printer offline" setting |

### Devices, Display, and Audio

| Area | Tools and Actions |
|---|---|
| Device Manager | `devmgmt.msc`; Scan for hardware changes; show hidden devices |
| USB not detected | Try another port, disable USB selective suspend, update chipset driver |
| Display issues | `Win+Ctrl+Shift+B`, update GPU driver, DDU for clean driver reinstall, `dxdiag` |
| Audio | Restart `Audiosrv` and `AudioEndpointBuilder` services, check default device in Sound settings |
| Bluetooth | Restart `bthserv`, remove and re-pair device, update adapter driver |
| Time zone / clock | `tzutil /g` (get), `tzutil /s "India Standard Time"` (set) |
| Keyboard layouts | Settings > Time and language > Language and region |

---

## Backup and Recovery

### Backup Strategy

Follow the **3-2-1 rule**: three copies of data, on two different media, with one copy offsite (or immutable).

| Tool | Use |
|---|---|
| OneDrive Known Folder Move | Syncs Desktop, Documents, Pictures to the cloud |
| Windows Backup app | Backs up settings, apps list, and folders (Windows 11) |
| File History | Versioned backups of user folders to an external drive |
| Backup and Restore (Windows 7) / `wbadmin` | System image and file backups |
| System Restore points | Roll back system files and registry (not personal files) |
| Volume Shadow Copy | Point-in-time snapshots and "Previous Versions" |
| `robocopy` | Scripted file backup to a drive or share |
| Third-party / enterprise | Veeam, Acronis, Azure Backup, Microsoft Defender for Cloud |

```cmd
wbadmin start backup -backupTarget:E: -include:C: -allCritical -quiet
wbadmin get versions
wbadmin start sysrecovery -version:10/21/2026-10:00 -backupTarget:E:       :: run from WinRE
robocopy C:\Users\hitesh\Documents E:\Backup\Documents /MIR /R:2 /W:5 /LOG:C:\logs\backup.log
vssadmin list shadows
recdisc                                    :: create a system repair disc
```

```powershell
Enable-ComputerRestore -Drive "C:\"
Checkpoint-Computer -Description "Manual restore point"
Get-ComputerRestorePoint
```

### Recovery Options Summary

| Situation | Recovery |
|---|---|
| Deleted a file | Recycle Bin, Previous Versions, File History, OneDrive recycle bin |
| Bad update or driver | Uninstall update, roll back driver, System Restore |
| Cannot boot | Startup Repair, Safe Mode, `bootrec`, `bcdboot`, offline `sfc` |
| OS corruption | `DISM` + `sfc`, in-place upgrade repair, Reset this PC |
| Failed disk | Replace and restore image or reinstall and restore files |
| Ransomware | Isolate machine, wipe and restore from clean offline backup |
| Forgot local password | Use PIN or Microsoft account recovery; for domain accounts, reset in AD |
| BitLocker recovery screen | Enter the 48-digit key from account or AD, then investigate why it triggered |

### Test Restores

A backup that has never been restored is unverified. Schedule restore tests and record Recovery Time (RTO) and Recovery Point (RPO).

---

## Installing, Deploying and Migrating Windows

### Getting Installation Media

- Download the ISO or the Media Creation Tool only from Microsoft; verify the hash when one is published: `Get-FileHash .\Win11.iso -Algorithm SHA256`.
- Create a bootable USB with the Media Creation Tool, Rufus, or manually (below).
- Check which editions an image holds: `DISM /Get-WimInfo /WimFile:D:\sources\install.wim`.

### Manual Bootable USB (UEFI/GPT)

> `clean` erases the selected disk. Run `list disk` and confirm by size that you picked the USB.

```cmd
diskpart
list disk
select disk 2
clean
convert gpt
create partition primary
format fs=fat32 quick label=WINSETUP
assign
exit
```

Copy the ISO contents to the USB (mount the ISO, then `robocopy D:\ E:\ /E`).

If `install.wim` is larger than 4 GB (FAT32 file limit), split it:

```cmd
DISM /Split-Image /ImageFile:D:\sources\install.wim /SWMFile:E:\sources\install.swm /FileSize:3800
```

(Copy everything **except** `install.wim`, then run the split into `E:\sources`.)

### During Setup

| Key | Use |
|---|---|
| `Shift+F10` | Open a command prompt (diskpart, driver loading, logs) |
| `F2` / `Del` / `F12` (varies) | Firmware setup and boot menu |
| "Load driver" | Add a storage driver when no disk is listed (Intel RST/VMD, some RAID/NVMe controllers) |

Setup logs: `C:\$WINDOWS.~BT\Sources\Panther` (during a failed upgrade) and `C:\Windows\Panther`.

### In-Place Upgrade (Repair Install)

Run `setup.exe` from a mounted ISO while signed in, choose **Keep personal files and apps**. It repairs deep OS corruption without a reinstall and is the best step after `DISM` and `sfc` fail. Enterprise deployments use `setup.exe /auto upgrade` with a configuration file or Configuration Manager task sequences.

### Before Reimaging or Replacing a PC

| Item | How |
|---|---|
| BitLocker recovery key | `manage-bde -protectors -get C:`; confirm it is backed up |
| User files | OneDrive Known Folder Move, `robocopy`, or USMT |
| Installed software list | `winget export -o apps.json`; note licenses |
| Saved Wi-Fi networks | `netsh wlan export profile folder=C:\wifi key=clear` (contains passwords; protect and delete afterwards) |
| Printers and mapped drives | `Get-Printer`, `net use` |
| Certificates | Export personal `.pfx` files with their private keys if needed |
| Browser profiles and passwords | Browser sync or export |
| Developer data | `.ssh`, `.gitconfig`, `.aws`, `.kube`, local databases, WSL (`wsl --export`) |
| Email and app data | `%APPDATA%`, application-specific export tools |

### USMT (User State Migration Tool)

Part of the Windows ADK; moves user profiles and settings between PCs in bulk.

```cmd
scanstate C:\MigStore /i:migdocs.xml /i:migapp.xml /o /c /v:5 /l:C:\scan.log
loadstate C:\MigStore /i:migdocs.xml /i:migapp.xml /c /v:5 /l:C:\load.log
```

### Post-Install Checklist

```text
[ ] Install all Windows updates, then OEM drivers and firmware
[ ] Rename the PC (Rename-Computer) and set the time zone (tzutil /s "India Standard Time")
[ ] Join the domain or Entra ID; apply policies (gpupdate /force)
[ ] Enable BitLocker and back up the recovery key to AD/Entra ID
[ ] Configure the local administrator (LAPS) and remove unneeded admins
[ ] Confirm Defender, firewall, and update settings
[ ] Install required software (winget import -i apps.json)
[ ] Map drives and printers; test the network and VPN
[ ] Create a restore point and document the asset (serial, IP, user)
```

---

## Error Codes and Stop Codes

### Looking Up Error Codes

```cmd
net helpmsg 5                       :: translate a Win32 error number
net helpmsg 1326
certutil -error 0x80070005          :: translate an HRESULT
echo %ERRORLEVEL%                   :: exit code of the last command
```

```powershell
[ComponentModel.Win32Exception]5    # translate a Win32 error number
```

### Win32 System Errors

| Code | Message | Typical Cause |
|---|---|---|
| 2 | The system cannot find the file specified | Wrong path or missing file |
| 3 | The system cannot find the path specified | Wrong directory |
| 5 | Access is denied | Permissions, not elevated, UAC, antivirus |
| 32 | The process cannot access the file because it is being used by another process | File locked (find the handle) |
| 53 | The network path was not found | Name resolution, SMB blocked, server off |
| 67 | The network name cannot be found | Share name wrong |
| 112 | There is not enough space on the disk | Disk full |
| 1219 | Multiple connections to a server by the same user with different credentials | `net use * /delete` |
| 1326 | The user name or password is incorrect | Bad credentials |
| 1722 | The RPC server is unavailable | Firewall, RPC service, DNS |
| 1053 | The service did not respond in a timely fashion | Service startup hang |

### Common HRESULT Codes

| Code | Meaning | Typical Fix |
|---|---|---|
| `0x80070005` | Access denied | Run as admin, check ACLs, antivirus |
| `0x80070002` | File not found | Reset Windows Update cache, verify path |
| `0x80070003` | Path not found | Check installation path |
| `0x80070057` | Invalid parameter | Repair with DISM/SFC, check input values |
| `0x80004005` | Unspecified error | Generic; check logs for context |
| `0x8007000E` | Out of memory | Free RAM or increase virtual memory |
| `0x80070020` | Sharing violation | Close the program holding the file |
| `0x80070070` | Not enough disk space | Free space |
| `0x80070422` | Service cannot be started because it is disabled | Enable the service (`sc config ... start= demand`) |
| `0x80070643` | Fatal error during installation | Repair Windows Installer, .NET, re-download installer |
| `0x80073712` | Component store file missing | `DISM /RestoreHealth` |
| `0x800F0922` | Update failed (CBS), often low System Reserved space or .NET | Free EFI/Reserved partition space, repair components |
| `0x80072EE7` | Server name could not be resolved | DNS or proxy issue |
| `0x80072EFE` | Connection terminated | Network or proxy interruption |
| `0x800704C7` | Operation cancelled by the user | Security prompt dismissed, antivirus |
| `0xC0000005` | Access violation (crash) | Faulty app, driver, or RAM |
| `0xC000007B` | Bad image (32/64-bit mismatch) | Install matching Visual C++ and DirectX runtimes |
| `0xC0000135` | DLL or .NET component not found | Install required runtime |
| `0xC0000142` | DLL initialization failed | Reinstall app, check antivirus |
| `0xC000021A` | Critical system process terminated (boot) | Offline `sfc`, restore, repair |

### Common BSOD Stop Codes

| Stop Code | Hex | Usual Direction |
|---|---|---|
| `IRQL_NOT_LESS_OR_EQUAL` | `0x0A` | Driver accessing invalid memory; update/remove recent drivers, test RAM |
| `KMODE_EXCEPTION_NOT_HANDLED` | `0x1E` | Faulty driver or hardware |
| `MEMORY_MANAGEMENT` | `0x1A` | Failing RAM or driver; run memory diagnostics |
| `SYSTEM_SERVICE_EXCEPTION` | `0x3B` | Driver or antivirus; check dump for module |
| `PAGE_FAULT_IN_NONPAGED_AREA` | `0x50` | Bad RAM, driver, antivirus, corrupted disk |
| `INACCESSIBLE_BOOT_DEVICE` | `0x7B` | Storage driver/controller mode change (AHCI/RAID), failing disk, bad update |
| `SYSTEM_THREAD_EXCEPTION_NOT_HANDLED` | `0x7E` | Driver (the `.sys` named in the stop screen) |
| `KERNEL_DATA_INPAGE_ERROR` | `0x7A` | Failing disk, cable, controller |
| `DRIVER_POWER_STATE_FAILURE` | `0x9F` | Driver cannot handle sleep/wake; update chipset, USB, GPU drivers |
| `BAD_POOL_CALLER` | `0xC2` | Driver bug |
| `DRIVER_IRQL_NOT_LESS_OR_EQUAL` | `0xD1` | Network or storage driver |
| `WHEA_UNCORRECTABLE_ERROR` | `0x124` | Hardware fault: CPU, RAM, overheating, overclock, PSU |
| `CRITICAL_PROCESS_DIED` | `0xEF` | Corrupt system files, failing disk, bad update |
| `VIDEO_TDR_FAILURE` | `0x116` | GPU driver or GPU hardware; clean reinstall driver |
| `DPC_WATCHDOG_VIOLATION` | `0x133` | Driver or firmware (often SSD firmware, storage driver) |
| `KERNEL_SECURITY_CHECK_FAILURE` | `0x139` | Memory corruption, driver, disk |
| `NTFS_FILE_SYSTEM` | `0x24` | Disk errors; run `chkdsk` |

---

## Linux to Windows Command Mapping

### Commands

| Linux | CMD | PowerShell |
|---|---|---|
| `pwd` | `cd` | `Get-Location` / `pwd` |
| `ls -la` | `dir /a` | `Get-ChildItem -Force` |
| `cd` | `cd /d` | `Set-Location` |
| `cat file` | `type file` | `Get-Content file` |
| `cp -r` | `xcopy /E /I`, `robocopy /E` | `Copy-Item -Recurse` |
| `mv` | `move`, `ren` | `Move-Item`, `Rename-Item` |
| `rm` | `del` | `Remove-Item` |
| `rm -rf` | `rmdir /s /q` | `Remove-Item -Recurse -Force` |
| `mkdir -p` | `mkdir` (creates parents) | `New-Item -ItemType Directory -Force` |
| `touch` | `type nul > file` | `New-Item file` |
| `grep` | `findstr` | `Select-String` |
| `find . -name` | `dir /s /b name` | `Get-ChildItem -Recurse -Filter` |
| `which` | `where` | `Get-Command`, `where.exe` |
| `head -n` / `tail -n` | `more` | `Get-Content -TotalCount` / `-Tail` |
| `tail -f` | — | `Get-Content -Wait` |
| `wc -l` | `find /c /v ""` | `Measure-Object -Line` |
| `sort`, `uniq` | `sort` | `Sort-Object`, `Sort-Object -Unique` |
| `diff` | `fc` | `Compare-Object` |
| `echo` | `echo` | `Write-Output` |
| `clear` | `cls` | `Clear-Host` |
| `man` | `help`, `/?` | `Get-Help` |
| `history` | `doskey /history` | `Get-History` |
| `alias` | `doskey` | `Set-Alias` |
| `env` / `printenv` | `set` | `Get-ChildItem Env:` |
| `export VAR=x` | `setx VAR x` | `$env:VAR = "x"` |
| `date` | `date /t`, `time /t` | `Get-Date` |
| `uname -a` | `ver`, `systeminfo` | `Get-ComputerInfo` |
| `hostname` | `hostname` | `hostname` |
| `whoami` | `whoami` | `whoami` |
| `id` | `whoami /all` | `whoami /all` |
| `sudo` | `runas`, elevated terminal | `Start-Process -Verb RunAs`, `sudo` (if enabled) |
| `su` | `runas /user:` | `Start-Process -Credential` |
| `chmod` | `icacls`, `attrib` | `Set-Acl` |
| `chown` | `takeown`, `icacls /setowner` | `Set-Acl` |
| `ln -s` | `mklink` | `New-Item -ItemType SymbolicLink` |
| `tar` | `tar` | `Compress-Archive` |
| `curl` | `curl.exe` | `Invoke-WebRequest`, `curl.exe` |
| `wget` | `curl.exe -O` | `Invoke-WebRequest -OutFile` |
| `ssh`, `scp` | `ssh`, `scp` | `ssh`, `scp` |
| `ping` | `ping` | `Test-Connection` |
| `traceroute` | `tracert` | `Test-NetConnection -TraceRoute` |
| `ip addr`, `ifconfig` | `ipconfig /all` | `Get-NetIPAddress` |
| `ss -tulnp`, `netstat` | `netstat -ano` | `Get-NetTCPConnection` |
| `nslookup`, `dig` | `nslookup` | `Resolve-DnsName` |
| `nc -zv host port` | — | `Test-NetConnection host -Port port` |
| `ps aux` | `tasklist` | `Get-Process` |
| `kill` / `kill -9` | `taskkill` / `taskkill /f` | `Stop-Process` / `Stop-Process -Force` |
| `top` | Task Manager | `Get-Process \| Sort CPU -desc` |
| `df -h` | `fsutil volume diskfree` | `Get-Volume` |
| `du -sh` | `dir /s` | `Get-ChildItem -Recurse \| Measure-Object Length -Sum` |
| `free -h` | `systeminfo` | `Get-CimInstance Win32_OperatingSystem` |
| `lsblk`, `fdisk -l` | `diskpart` > `list disk` | `Get-Disk`, `Get-Partition` |
| `mount` | `mountvol`, `subst` | `Mount-DiskImage` |
| `mkfs` | `format` | `Format-Volume` |
| `fsck` | `chkdsk` | `Repair-Volume` |
| `useradd` | `net user /add` | `New-LocalUser` |
| `usermod -aG` | `net localgroup ... /add` | `Add-LocalGroupMember` |
| `passwd` | `net user name *` | `Set-LocalUser` |
| `systemctl status` | `sc query` | `Get-Service` |
| `systemctl start/stop` | `net start` / `net stop`, `sc` | `Start-Service` / `Stop-Service` |
| `systemctl enable` | `sc config x start= auto` | `Set-Service -StartupType Automatic` |
| `journalctl` | `wevtutil qe` | `Get-WinEvent` |
| `crontab` | `schtasks` | `Register-ScheduledTask` |
| `apt install` | `winget install` | `winget install` |
| `lsof` | Resource Monitor, `handle.exe` | — |
| `strace` | Process Monitor | — |
| `dmesg` | Event Viewer System log | `Get-WinEvent -LogName System` |
| `shutdown -h now` | `shutdown /s /t 0` | `Stop-Computer` |
| `reboot` | `shutdown /r /t 0` | `Restart-Computer` |
| `nohup cmd &` | `start /b cmd` | `Start-Process` |
| `xargs` | `for /f` | `ForEach-Object` |
| `/dev/null` | `nul` | `$null` |
| `$?` | `%ERRORLEVEL%` | `$?`, `$LASTEXITCODE` |

### Paths and Concepts

| Linux | Windows |
|---|---|
| `/` | `C:\` (one root per drive) |
| `/home/user` | `C:\Users\user` |
| `/root` | `C:\Users\Administrator` (disabled by default) |
| `/etc` | Registry, `C:\ProgramData`, `C:\Windows\System32\config`, `%APPDATA%` |
| `/etc/hosts` | `C:\Windows\System32\drivers\etc\hosts` |
| `/etc/passwd`, `/etc/shadow` | SAM database (`C:\Windows\System32\config\SAM`), Active Directory |
| `/etc/fstab` | Persistent volume mounts via volume GUID, Disk Management |
| `/var/log` | Event Viewer, `C:\Windows\Logs`, `C:\ProgramData\<app>\logs` |
| `/tmp` | `%TEMP%`, `C:\Windows\Temp` |
| `/usr/bin`, `/bin` | `C:\Windows\System32`, `C:\Program Files` |
| `/opt` | `C:\Program Files` |
| `/proc`, `/sys` | WMI/CIM, performance counters, Registry |
| `/dev` | Device Manager, `\\.\` device namespace |
| `~/.bashrc` | `$PROFILE` (PowerShell), registry `AutoRun` for CMD |
| `~/.ssh` | `C:\Users\<name>\.ssh` |
| `bash`, `zsh` | `cmd`, PowerShell, or Bash in WSL / Git Bash |
| daemon | Service |
| cron | Task Scheduler |
| package manager | winget, Chocolatey, MSI, MSIX |
| file permissions `rwx` | NTFS ACLs |
| root | Administrators group, `SYSTEM` |
| signals | Window messages, `taskkill`, events |
| `PATH=a:b` | `PATH=a;b` |
| `LF` | `CRLF` |

---

## Scenario-Based Troubleshooting

### User Cannot Reach Websites

```cmd
ipconfig /all                       :: valid IP? (169.254.x.x means no DHCP)
ping 127.0.0.1
ping <default-gateway>
ping 8.8.8.8                        :: internet works without DNS?
ping google.com                     :: DNS works?
ipconfig /flushdns
ipconfig /release && ipconfig /renew
netsh winsock reset
netsh int ip reset                  :: then restart
netsh winhttp show proxy            :: proxy interfering?
```

### Computer Is Slow

```cmd
tasklist /v /fi "CPUTIME gt 00:05:00"
wmic cpu get loadpercentage         :: legacy; or use Task Manager
powershell -NoProfile "Get-Process | Sort CPU -Desc | Select -First 5 Name,CPU,WS"
```

Review Task Manager > Startup apps, free disk space, Defender scan, Windows Update activity, and `Get-PhysicalDisk` health.

### Find Which Process Uses a Port

```cmd
netstat -ano | findstr :8080
tasklist /fi "PID eq 4321"
taskkill /pid 4321 /f
```

### Free Disk Space

```cmd
cleanmgr /sageset:1 & cleanmgr /sagerun:1
DISM /Online /Cleanup-Image /StartComponentCleanup /ResetBase
powercfg /h off                                     :: removes hiberfil.sys (disables hibernation)
rd /s /q "%TEMP%"                                   :: user temp files (close apps first)
```

```powershell
Get-ChildItem C:\ -Recurse -File -ErrorAction SilentlyContinue | Sort-Object Length -Descending | Select-Object -First 20 FullName, @{n='GB';e={[math]::Round($_.Length/1GB,2)}}
```

### Locked-Out or Locked Account

```cmd
net user jsmith /domain                      :: check "Account active" and lockout status
```

In AD: `Unlock-ADAccount -Identity jsmith`. In Event Viewer on the DC, filter Security Event ID **4740** to find the **Caller Computer Name** causing lockouts, then clear stale credentials there (Credential Manager, mapped drives, scheduled tasks, services, phones).

### Application Hung or Not Responding

```cmd
tasklist /fi "STATUS eq NOT RESPONDING"
taskkill /im app.exe /f
```

If a file cannot be deleted: Resource Monitor > CPU > Associated Handles > search file name.

### Restart a Frozen Taskbar or Explorer

```cmd
taskkill /f /im explorer.exe & start explorer.exe
```

### Check Last Boot Time and Why It Restarted

```cmd
systeminfo | findstr /C:"System Boot Time"
wevtutil qe System /q:"*[System[(EventID=41 or EventID=1074 or EventID=6008)]]" /c:5 /rd:true /f:text
```

### Find Who Logged In Recently

```cmd
query user
```

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624; StartTime=(Get-Date).AddDays(-1)} -MaxEvents 20 | Select-Object TimeCreated, @{n='User';e={$_.Properties[5].Value}}, @{n='LogonType';e={$_.Properties[8].Value}}
```

### Collect Hardware Details for an Asset Ticket

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object Manufacturer, Model, Name, TotalPhysicalMemory
Get-CimInstance Win32_BIOS | Select-Object SerialNumber
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, InstallDate
Get-NetIPConfiguration
Get-BitLockerVolume | Select-Object MountPoint, ProtectionStatus
```

### Clear Print Queue

```cmd
net stop spooler & del /q /f /s %systemroot%\System32\spool\PRINTERS\* & net start spooler
```

### Repair Windows When It Behaves Strangely

```cmd
DISM /Online /Cleanup-Image /RestoreHealth
sfc /scannow
chkdsk C: /scan
```

### Remote Support of a Domain PC

```cmd
ping PC01
nslookup PC01
psexec \\PC01 -s cmd
shutdown /r /m \\PC01 /t 60 /c "Restart for maintenance"
```

---

## Windows Gotchas for Linux Users

| Gotcha | Detail |
|---|---|
| Paths use `\` and drive letters | `C:\dir\file`; many tools accept `/` but CMD built-ins treat `/` as an option prefix |
| Case-insensitive names | `File.txt` equals `file.txt`; Git on Windows can hide case bugs |
| Line endings | CRLF breaks shell scripts and some tools on Linux |
| No `fork()` | Process creation is slower, so shell-heavy scripts and Git operations run slower than on Linux |
| Open files are locked | Cannot delete or rename a file in use |
| `where` is an alias | In PowerShell `where` means `Where-Object`; use `where.exe` |
| `curl` is an alias in PowerShell 5.1 | It calls `Invoke-WebRequest`; use `curl.exe` |
| `>` encoding | PowerShell 5.1 redirects as UTF-16; use `Out-File -Encoding utf8` |
| No `chmod +x` | Executability depends on extension and ACLs; Git tracks the bit separately |
| Environment changes need new shell | `setx` affects only new processes |
| `PATH` separator | `;` not `:` |
| Reserved names | `con`, `aux`, `nul`, `prn`, `com1` cannot be file names |
| `MAX_PATH` of 260 characters | Enable long paths; affects `node_modules` and deep repos |
| Antivirus scans every write | Builds and `git status` can slow down; use Dev Drive |
| Symlinks need privilege | Developer Mode or admin required |
| Windows Update restarts | Active hours and deadlines can trigger reboots |
| Shutdown is not a full reboot | Fast Startup keeps kernel state; use Restart |
| Backslash escapes | Quote paths and escape trailing `\` carefully in `cmd` strings |
| `%` expansion | Variables use `%VAR%`; in batch files `%%i` in loops |
| Services are not files | Manage with `sc`/`services.msc`, not `/etc/init.d` or unit files |
| Registry edits are global | No version control; always export before editing |
| Home edition limits | No `gpedit.msc`, `lusrmgr.msc`, RDP host, or full BitLocker management |

---

## Quick Reference Cheatsheet

### Navigation and Files

```cmd
cd /d D:\path        dir /a /s /b      tree /f
copy a b             xcopy src dst /E /I /Y
robocopy src dst /E /R:2 /W:5 /MT:16
move a b             ren old new       del /f /q file
rmdir /s /q dir      type file         more file
mklink /D link target
attrib +h file       fc a b            where program
```

### System Information

```cmd
winver   ver   systeminfo   hostname   whoami /all
msinfo32   dxdiag   devmgmt.msc   diskmgmt.msc
Get-ComputerInfo   Get-CimInstance Win32_OperatingSystem
```

### Processes and Services

```cmd
tasklist /svc          taskkill /im app.exe /f     taskkill /pid N /t /f
sc query               sc qc svc                  net start svc / net stop svc
sc config svc start= auto
Get-Process            Get-Service                Restart-Service svc
```

### Networking

```cmd
ipconfig /all          ipconfig /flushdns         ipconfig /renew
ping host              tracert host               pathping host
nslookup host          netstat -ano               route print
arp -a                 netsh winsock reset        netsh int ip reset
Test-NetConnection host -Port 443
net use Z: \\srv\share
```

### Users and Security

```cmd
net user               net user name /add         net localgroup Administrators name /add
whoami /groups         gpupdate /force            gpresult /r
icacls path            takeown /f path /r /d y    manage-bde -status
klist                  cmdkey /list               runas /user:name cmd
```

### Disk and Repair

```cmd
diskpart               chkdsk C: /f /r            sfc /scannow
DISM /Online /Cleanup-Image /RestoreHealth
bootrec /rebuildbcd    bcdedit /enum              reagentc /info
cleanmgr               defrag C: /O               vssadmin list shadows
```

### Logs, Scheduling, Registry

```cmd
eventvwr.msc           wevtutil qe System /c:20 /rd:true /f:text
schtasks /query /fo TABLE    schtasks /create /tn name /tr cmd /sc daily /st 01:00
reg query key          reg add key /v name /t REG_SZ /d value /f
reg export key file.reg
```

### Packages and Developer Tools

```cmd
winget search x        winget install --id X -e   winget upgrade --all
wsl --install          wsl -l -v                  wsl --shutdown
tar -xvf file.tar      curl.exe -I URL            ssh user@host
```

### Frequently Used Run Commands

```text
cmd  powershell  wt  taskmgr  services.msc  eventvwr.msc  devmgmt.msc  diskmgmt.msc
compmgmt.msc  gpedit.msc  lusrmgr.msc  secpol.msc  wf.msc  taskschd.msc  regedit
msconfig  msinfo32  resmon  perfmon  mstsc  ncpa.cpl  appwiz.cpl  sysdm.cpl  control
```

---

*This document covers Windows OS fundamentals, administration, troubleshooting, and developer workflows with CMD and basic PowerShell commands. Practice each section hands-on in a virtual machine or a test PC. Take a restore point or snapshot before changing the registry, partitions, or system files.*