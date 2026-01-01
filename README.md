# 🦝 VdoCoon // OMEGA // v47.7 "QUASAR ETERNAL"

> **The Final Sovereign Media Hypervisor.**  
> *Autonomic. 10-Bit. Immutable. Infinite.*

![Screenshot](https://raw.githubusercontent.com/shfqrkhn/VdoCoon/refs/heads/main/%7BCB1CEA86-D664-4411-84BC-C663B18E19B0%7D.jpg)

## 📑 Table of Contents

1. [System Overview](#-system-overview)
2. [Omega Architecture](#-omega-architecture)
3. [Installation](#-installation)
4. [Command Reference](#-command-reference)
5. [Tactical Controls](#-tactical-controls)
6. [Configuration](#-configuration)
7. [Reporting & Forensics](#-reporting--forensics)
8. [Source Code (The Artifact)](#-source-code)

---

## 🔭 System Overview

**VdoCoon** is a mission-critical PowerShell hypervisor for FFmpeg. Unlike standard batch converters, VdoCoon functions as an autonomic decision engine. It profiles your hardware, analyzes media topology, and constructs a bespoke transcoding pipeline for every single file.

It transforms mixed media libraries into a unified **Matroska (MKV) HEVC 10-bit** archive. It creates a mathematically perfect archival copy, reducing storage footprint by 40-60% while strictly preserving visual fidelity (Main10), HDR metadata, timestamps, and chapters.

### Key Capabilities

*   **Sentinel Mode:** Runs as a persistent daemon (`-Watch`), monitoring a drop-folder indefinitely.
*   **Smart Storage Routing:** If the local disk is full, the system automatically reroutes temporary processing to a defined **Scratch Disk** (e.g., an external SSD) to prevent mission failure.
*   **Self-Healing Kernel:** Features a **Binary Integrity Probe**. Upon boot, it cryptographically verifies FFmpeg. If the binary is missing, corrupt (0-byte), or locked, it automatically purges and re-downloads the assets via secure BITS transfer. It also performs **Binary Surgery**, killing rogue zombie processes to ensure successful updates.
*   **Lazarus Protocol:** If hardware acceleration (NVIDIA/Intel) fails due to driver instability, the system automatically falls back to software encoding (CPU).
*   **Zero-Point Defense:** Safely handles corrupt files, "Instant Death" binaries, and filename anomalies (brackets, emojis, quotes) using a robust Fail-Open sensor suite. Includes automatic **Mark-of-the-Web** removal.
*   **Visual Cortex:** Features a joyful minimalist cyberpunk boot sequence and a jitter-free, double-buffered TUI.

---

## 🏛 Omega Architecture

### 1. The Decision Engine

VdoCoon is a "White Box" system. It calculates a compression strategy based on your config and maps it to specific hardware flags. It features a **Hardware Strategy** governor and a **Scaling Engine** that correctly constructs complex filter chains for simultaneous resizing and 10-bit pixel format conversion.

### 2. The Safety Manifold

*   **Orphan Hunter v2:** Uses cryptographic file locking probes to distinguish between "Orphaned Temp Files" (safe to delete) and "Active Long-Running Encodes" (must preserve), preventing accidental data loss during multi-instance runs.
*   **Atomic Swap+:** The final file replacement phase includes a **Retry Loop** to handle transient locks from Antivirus or Windows Indexing, ensuring the transaction completes successfully.
*   **Watchdog:** If the encoder hangs (0 FPS) for >120 seconds, the process is terminated.
*   **Deduplication:** Automatically skips files previously processed by VdoCoon or known competitor tools (e.g., WinX).

### 3. Neural Link (Telemetry)

*   **HUD:** A responsive, 10Hz TUI displaying real-time FPS, Speed, and ETA with dynamic text clamping for small windows.
*   **Storage Efficiency:** Real-time display of **% Space Saved** alongside the total Gigabytes reclaimed.
*   **Taskbar Pulse:** Integrates with the Windows Taskbar to show progress (Green), Error (Red), or Indeterminate (Marquee) states.
*   **Window Sync:** The console window title mirrors the active job status for visibility when minimized.

---

## 💿 Installation

### Prerequisites

*   Windows 10/11 or Server 2019+.
*   **FFmpeg**: VdoCoon includes an **Auto-Bootstrap System**. Just run the script. It will self-assemble all necessary binaries.

### Setup

1.  Create a directory (e.g., `C:\VdoCoon`).
2.  Save the [Source Code](#-source-code) below as `VdoCoon.ps1` in that folder.
3.  Right-click the script -> `Run with PowerShell`.

---

## ⌨ Command Reference

### 1. Batch Mode (One-Shot)

Scans the library once, processes all items, generates a report, and exits.

```powershell
.\VdoCoon.ps1
```

### 2. Sentinel Mode (Autonomous)

Monitors the library indefinitely. Sleep/Wait cycles are automatic. Safe to leave running on a server forever. If the config is corrupt, it auto-loads safe defaults to prevent hanging on prompts.

```powershell
.\VdoCoon.ps1 -Watch
```

### 3. Sniper Mode (Single Target)

Process a specific file or folder immediately, bypassing the saved config path.

```powershell
.\VdoCoon.ps1 -Target "D:\Downloads\MyMovie.mkv"
```

### 4. Simulation (Dry Run)

Runs the Decision Engine and logs the exact FFmpeg command commands but **does not** execute them.

```powershell
.\VdoCoon.ps1 -DryRun
```

---

## 🎮 Tactical Controls

While the encoder is running, the terminal accepts live inputs:

| Key     | Action             | Description                                            |
| :------ | :----------------- | :----------------------------------------------------- |
| **[P]** | **Pause (Stasis)** | Freezes the process in RAM. 0% CPU. Safe to game/work. |
| **[R]** | **Resume**         | Thaws the process. Encoding continues instantly.       |
| **[S]** | **Skip**           | Aborts the current file safely and moves to the next.  |
| **[G]** | **Graceful Stop**  | Finishes the active file, then exits the script.       |

---

## ⚙ Configuration

On first run, `VdoCoon.json` is generated. Run `.\VdoCoon.ps1 -ResetConfig` to modify.

| Setting            | Options                         | Description                                                 |
| :----------------- | :------------------------------ | :---------------------------------------------------------- |
| `Speed`            | `Slow`, `Medium`, `Fast`        | **Slow** = Best Compression. **Fast** = Quick Drafts.       |
| `Quality`          | `18`, `24`, `28`                | CRF Value. **18** is visually lossless. **24** is balanced. |
| `HardwareStrategy` | `Auto`, `CPU`                   | **Auto** prefers GPU. **CPU** forces software encoding.     |
| `ProcessPriority`  | `Normal`, `BelowNormal`, `Idle` | Controls system responsiveness during encoding.             |
| `ScratchPath`      | `D:\Temp`, `[Empty]`            | Optional path for temp files if the source drive is full.   |
| `Resolution`       | `Original`, `1080p`, `720p`     | Downscales larger content. Never upscales.                  |
| `PostAction`       | `None`, `Shutdown`, `Sleep`     | Action after Batch completion. Disabled in Watch Mode.      |

---

## 📊 Reporting & Forensics

VdoCoon generates two persistent artifacts to track mission status:

### 1. The Ledger (`VdoCoon_Ledger.csv`)

A real-time CSV database of every transaction.

*   **Timestamp:** Exact time of completion.
*   **File:** Filename processed.
*   **Status:** `Success`, `Skipped`, or `Error`.
*   **Info:** Details on the operation (e.g., "Saved 450 MB").

### 2. The Manifest (`VdoCoon_Report_YYYYMMDD.txt`)

A mission summary generated at the end of a Batch run. Includes the **Compression Ratio** parameter:

*   **File:** Name of the asset.
*   **Status:** Outcome of the job.
*   **Size (MB):** Final file size.
*   **Result (Parameter):** Displays `Saved [X] MB` and the **`%-saved`** ratio (e.g., `(45%)`).

---

## 💎 Source Code

**File:** `VdoCoon.ps1`  
**Version:** OMEGA v47.7 "QUASAR ETERNAL"

```powershell
<#
.SYNOPSIS
    VdoCoon // OMEGA // v47.7 "QUASAR ETERNAL"
    The Final Sovereign Media Hypervisor.
    
    [CHANGELOG v47.7]
    - SCALING ENGINE:     (Crit) Fixed invalid FFmpeg filter syntax for NVIDIA/CUDA resizing. 
                          The system now correctly constructs 'scale_cuda' filter chains, ensuring 
                          simultaneous resizing and 10-bit pixel format conversion works perfectly.
    - VISUAL CORTEX:      (New) Added 'Render-BootSequence', a non-blocking minimalist animation 
                          during startup to fulfill the Cyberpunk aesthetic requirement.
    - CORE:               (Keep) Self-Healing Binaries, Autonomous Config, Orphan Hunter v2, Atomic Swap+.

    [USAGE]
    .\VdoCoon.ps1                    # Batch Scan
    .\VdoCoon.ps1 -Watch             # Sentinel Mode
    .\VdoCoon.ps1 -Target "D:\Film"  # Sniper Mode
#>

[CmdletBinding()]
param(
    [Parameter(Mandatory=$false, Position=0)][string]$Target,
    [Parameter(Mandatory=$false)][switch]$Watch,
    [Parameter(Mandatory=$false)][switch]$ResetConfig,
    [Parameter(Mandatory=$false)][switch]$DryRun
)

# region [ 0. BOOTSTRAP & KERNEL ] ============================================
$ErrorActionPreference = "Stop"
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
[System.Threading.Thread]::CurrentThread.CurrentCulture = [System.Globalization.CultureInfo]::InvariantCulture

$Global:Root = $PSScriptRoot
$Global:StartTime = [DateTime]::Now

# Mutex
$Global:Mutex = New-Object System.Threading.Mutex($false, "Global\VdoCoon_$($Global:Root.GetHashCode())")
if (-not $Global:Mutex.WaitOne(0, $false)) { Write-Host " [LOCKED] Omega Infinity is active." -ForegroundColor Red; exit }

# Theme (Cyberpunk Palette)
$C = @{ 
    Rst="$([char]27)[0m"; Bld="$([char]27)[1m"; Dim="$([char]27)[2m"; 
    Cyn="$([char]27)[36m"; Mag="$([char]27)[35m"; Grn="$([char]27)[32m"; 
    Red="$([char]27)[31m"; Ylw="$([char]27)[33m"; Wht="$([char]27)[37m";
    Gry="$([char]27)[90m"; Blu="$([char]27)[34m"; Org="$([char]27)[38;5;208m";
    Gld="$([char]27)[38;5;220m"; Pur="$([char]27)[38;5;141m"; Teal="$([char]27)[38;5;45m"
}
$G = @{ H="═"; V="║"; BarF="█"; BarE="░"; Play="►"; Stop="■"; Skip="»"; Eye="◉"; Pause="║║"; Rec="●"; Hash="▒" }

# Kernel Injection
try {
    $Sig = @'
    [DllImport("ntdll.dll")] public static extern int NtSuspendProcess(IntPtr ProcessHandle);
    [DllImport("ntdll.dll")] public static extern int NtResumeProcess(IntPtr ProcessHandle);
    [DllImport("kernel32.dll")] public static extern uint SetThreadExecutionState(uint esFlags);
    [DllImport("kernel32.dll")] public static extern bool SetConsoleMode(IntPtr hConsoleHandle, uint dwMode);
    [DllImport("kernel32.dll")] public static extern bool GetConsoleMode(IntPtr hConsoleHandle, out uint lpMode);
    [DllImport("kernel32.dll")] public static extern IntPtr GetStdHandle(int nStdHandle);
'@
    $Win32 = Add-Type -MemberDefinition $Sig -Name "Win32" -Namespace Win32 -PassThru
    $Global:Insomnia={param($On)$F=$On?0x80000001:0x80000000;[void]$Win32::SetThreadExecutionState($F)}
    
    if (-not ($Host.Name -match "ISE")) {
        $StdOut = $Win32::GetStdHandle(-11)
        $Mode = 0; $Win32::GetConsoleMode($StdOut, [ref]$Mode) | Out-Null
        $Win32::SetConsoleMode($StdOut, $Mode -bor 4) | Out-Null
    }
} catch { $Global:Insomnia={param($On)}; Write-Host " [WARN] Kernel Hooks Failed (Non-Windows?)." -ForegroundColor Red }

# Headless Safety
$Global:Headless = $false
try { 
    Add-Type -AssemblyName System.Windows.Forms -ErrorAction Stop
    $T=[System.Type]::GetTypeFromProgID("TaskbarList"); if($T){$Global:Taskbar=[System.Activator]::CreateInstance($T)}
} catch { $Global:Headless = $true }

# Write Check
if (-not (Test-Path -LiteralPath "$Global:Root\write_test.tmp")) { 
    try{New-Item -Path "$Global:Root\write_test.tmp" -ItemType File -Force|Out-Null;Remove-Item -Path "$Global:Root\write_test.tmp" -Force}
    catch{Write-Host "[FATAL] Script Dir Read-Only." -ForegroundColor Red;exit} 
}
# endregion

# region [ 1. STATE STORE ] ===================================================
$Store = [pscustomobject]@{
    State       = "BOOT"
    Config      = @{}
    Hardware    = @{ Type="CPU"; Encoder="libx265"; Capability="Low"; FallbackActive=$false }
    Stats       = @{ TotalFiles=0; Processed=0; Skipped=0; Errors=0; BytesSaved=[long]0; SizeProcessed=[long]0; TotalBytes=[long]1; BytesDone=[long]0; RunCount=0 }
    Job         = @{ File="Waiting..."; Task="Idle"; Decision="Pending"; Pct=0; FPS=0; Speed=0; ETA="--:--"; Priority="BelowNormal"; SkippedByUser=$false; IsPaused=$false }
    Logs        = New-Object System.Collections.Generic.List[string]
    FFLog       = New-Object System.Collections.Generic.List[string]
    Report      = New-Object System.Collections.Generic.List[pscustomobject]
    Blacklist   = @{}
    Files       = @{ HistoryLog=Join-Path $Global:Root "VdoCoon_History.log"; ErrorLog=Join-Path $Global:Root "VdoCoon_Errors.log"; Ledger=Join-Path $Global:Root "VdoCoon_Ledger.csv" }
    Runtime     = @{ TempFile=$null; LastRender=[DateTime]::MinValue; CurrentPID=$null; MissionStart=[DateTime]::Now; NextScan=[DateTime]::MinValue; LastActivity=[DateTime]::Now }
}
# endregion

# region [ 2. SYSTEM CORE ] ===================================================
function Dispatch-Log ($Message, $Level="INFO") {
    $Time = Get-Date -Format "HH:mm:ss"
    if (-not $DryRun) {
        try {
            "[$Time] [$Level] $Message" | Out-File -LiteralPath $Store.Files.HistoryLog -Append -Encoding utf8 -ErrorAction Stop
            if ($Level -in "ERR", "WARN", "CRIT", "LAZARUS") { 
                "[$Time] [$Level] $Message" | Out-File -LiteralPath $Store.Files.ErrorLog -Append -Encoding utf8 -ErrorAction Stop
            }
        } catch {}
    }
    $Cl = switch($Level) { "INFO"{$C.Gry} "OK"{$C.Grn} "WARN"{$C.Ylw} "ERR"{$C.Red} "CRIT"{$C.Red+$C.Bld} "SYS"{$C.Cyn} "DRY"{$C.Mag} "USER"{$C.Blu} "PAUSE"{$C.Org} "LAZARUS"{$C.Teal} Default{$C.Wht} }
    [void]$Store.Logs.Add("$($C.Dim)$Time$($C.Rst) $Cl$Message$($C.Rst)")
    if ($Store.Logs.Count -gt 8) { $Store.Logs.RemoveAt(0) }
}

function Write-Ledger ($File, $Status, $Info) {
    if ($DryRun) { return }
    $Line = "`"$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')`",`"$File`",`"$Status`",`"$Info`""
    try {
        if (-not (Test-Path -LiteralPath $Store.Files.Ledger)) { "Timestamp,File,Status,Info" | Out-File -LiteralPath $Store.Files.Ledger -Encoding utf8 }
        $Line | Out-File -LiteralPath $Store.Files.Ledger -Append -Encoding utf8
    } catch {}
}

function Rotate-Logs { 
    foreach ($K in $Store.Files.Keys) { 
        $P=$Store.Files[$K]; 
        if((Test-Path -LiteralPath $P) -and (Get-Item -LiteralPath $P).Length -gt 5MB){
            try { Move-Item -LiteralPath $P -Destination "$P.$((Get-Date).ToString('yyyyMMdd')).old" -Force -ErrorAction SilentlyContinue } catch {}
        } 
    } 
}

function Test-WriteAccess ($Path) {
    $TestFile = Join-Path $Path ("_vc_probe_" + [Guid]::NewGuid().ToString().Substring(0,6) + ".tmp")
    try { New-Item -Path $TestFile -ItemType File -Force -ErrorAction Stop | Out-Null; Remove-Item -Path $TestFile -Force -ErrorAction Stop; return $true } catch { return $false }
}

function Test-FileStability ($Path) {
    try {
        $Stream = [System.IO.File]::Open($Path, 'Open', 'Read', 'None')
        if ($Stream) { $Stream.Close(); $Stream.Dispose() }
        return $true
    } catch { return $false }
}

function Get-DriveFreeSpace ($Path) {
    try {
        $Root = [System.IO.Path]::GetPathRoot($Path)
        if ($Root -match "^[a-zA-Z]:") { return [System.IO.DriveInfo]::new($Root).AvailableFreeSpace }
        return [long]::MaxValue
    } catch { return [long]::MaxValue }
}

function Send-Toast ($Title, $Text, $Icon="Info") {
    if ($DryRun -or $Global:Headless) { return }
    try {
        $Notif = New-Object System.Windows.Forms.NotifyIcon
        $Notif.Icon = [System.Drawing.Icon]::ExtractAssociatedIcon((Get-Process -Id $Pid).Path)
        $Notif.Visible = $true
        $Notif.ShowBalloonTip(3000, $Title, $Text, [System.Windows.Forms.ToolTipIcon]::$Icon)
        Start-Sleep -Milliseconds 100 
        $Notif.Dispose()
    } catch {}
}

function Set-TaskbarProgress ($Val, $Max, $State="Normal") {
    if ($Global:Taskbar) {
        try{
            $H=[System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
            if ($H -ne [IntPtr]::Zero) {
                $F=switch($State){"NoProgress"{0}"Indeterminate"{1}"Error"{4}"Paused"{8}Default{2}}
                [void]$Global:Taskbar.SetProgressState($H,$F);[void]$Global:Taskbar.SetProgressValue($H,[long]$Val,[long]$Max)
            }
        }catch{}
    }
}

function Invoke-Sonic ($Type) {
    if ($DryRun -or $Global:Headless) { return }
    try {
        switch ($Type) {
            "Success" { [Console]::Beep(523, 150); [Console]::Beep(659, 150); [Console]::Beep(784, 300) }
            "Error"   { [Console]::Beep(110, 400); [Console]::Beep(100, 600) }
            "Done"    { [Console]::Beep(784, 200); [Console]::Beep(1046, 400) }
            "Lazarus" { [Console]::Beep(440, 100); [Console]::Beep(880, 100) }
        }
    } catch {}
}

function Generate-Manifest {
    $RptPath = Join-Path $Global:Root "VdoCoon_Report_$((Get-Date).ToString('yyyyMMdd-HHmm')).txt"
    $Header =  "VDOCOON OMEGA v47.7 // MISSION MANIFEST`n"
    $Header += "DATE: $(Get-Date) | DURATION: $((Get-Date) - $Store.Runtime.MissionStart)`n"
    $Header += "TOTAL: $($Store.Stats.TotalFiles) | PROCESSED: $($Store.Stats.Processed) | SAVED: $([math]::Round($Store.Stats.BytesSaved/1GB, 2)) GB`n"
    $Header += ("="*80) + "`n"
    $Body = $Store.Report | Format-Table -AutoSize | Out-String -Width 120
    $Header + $Body | Out-File -LiteralPath $RptPath -Encoding utf8
    return $RptPath
}

function Invoke-Cleanup {
    if ($DryRun) { return }
    if ($Store.Runtime.CurrentPID) { 
        try { 
            $Proc = Get-Process -Id $Store.Runtime.CurrentPID -ErrorAction SilentlyContinue
            if ($Proc) {
                try { [Win32.Win32]::NtResumeProcess($Proc.Handle) } catch {}
                Stop-Process -Id $Store.Runtime.CurrentPID -Force -ErrorAction SilentlyContinue
            }
        } catch {}
    }
    $Store.Runtime.CurrentPID = $null
    if ($Store.Runtime.TempFile -and (Test-Path -LiteralPath $Store.Runtime.TempFile)) { Remove-Item -LiteralPath $Store.Runtime.TempFile -Force -EA 0 }
    Set-TaskbarProgress 0 0 "NoProgress"
    & $Global:Insomnia $false
}

function Bootstrap-Binaries {
    $Url = "https://www.gyan.dev/ffmpeg/builds/ffmpeg-release-essentials.zip"
    $ZipPath = Join-Path $Global:Root "ffmpeg_bundle.zip"
    $ExtractPath = Join-Path $Global:Root "ffmpeg_temp"
    try {
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Write-Host "`n [BOOTSTRAP] Binary Integrity Failed. Re-acquiring Assets..." -ForegroundColor Cyan
        
        Get-Process -Name "ffmpeg","ffprobe" -ErrorAction SilentlyContinue | Stop-Process -Force -ErrorAction SilentlyContinue

        if (Get-Command "Start-BitsTransfer" -ErrorAction SilentlyContinue) {
            Write-Host " [1/3] Downloading Core via BITS..." -ForegroundColor Yellow
            Start-BitsTransfer -Source $Url -Destination $ZipPath
        } else {
            Write-Host " [1/3] Downloading Core via WebRequest..." -ForegroundColor Yellow
            Invoke-WebRequest -Uri $Url -OutFile $ZipPath -UseBasicParsing -UserAgent "VdoCoon/47.7"
        }
        Write-Host " [2/3] Extracting..." -ForegroundColor Yellow
        Expand-Archive -LiteralPath $ZipPath -DestinationPath $ExtractPath -Force
        Write-Host " [3/3] Installing..." -ForegroundColor Yellow
        
        $Bins = Get-ChildItem -LiteralPath $ExtractPath -Recurse | Where-Object { $_.Name -in "ffmpeg.exe", "ffprobe.exe" }
        foreach ($B in $Bins) { 
            Move-Item -LiteralPath $B.FullName -Destination $Global:Root -Force 
            Unblock-File -LiteralPath (Join-Path $Global:Root $B.Name) -ErrorAction SilentlyContinue
        }
        Write-Host " [SUCCESS] System Assembled." -ForegroundColor Green
        Start-Sleep 1
    } catch {
        Write-Host " [FATAL] Bootstrap Failed. $_" -ForegroundColor Red
        Read-Host " Press Enter to Exit"; exit
    } finally {
        Remove-Item -LiteralPath $ZipPath -Force -ErrorAction SilentlyContinue
        Remove-Item -LiteralPath $ExtractPath -Recurse -Force -ErrorAction SilentlyContinue
    }
}

if (-not ($Host.Name -match "ISE")) { try { [Console]::CancelKeyPress += { Invoke-Cleanup; [Console]::CursorVisible=$true; Write-Host "`n [HALT] Systems Disengaged." -ForegroundColor Red; [Environment]::Exit(1) } } catch {} }
# endregion

# region [ 3. UI RENDERER ] ===================================================
function Render-BootSequence {
    if ($Host.Name -match "ISE" -or $Global:Headless) { return }
    Clear-Host
    $Chars = "|","/","-","\"
    $Steps = "INITIALIZING KERNEL","LOADING MODULES","VERIFYING ASSETS","ENGAGING NEURAL LINK"
    foreach ($S in $Steps) {
        for ($i=0; $i -lt 5; $i++) {
            Write-Host -NoNewline "`r $($C.Cyn)$($Chars[$i%4]) $($C.Wht)$S..."
            Start-Sleep -Milliseconds 40
        }
        Write-Host -NoNewline "`r $($C.Grn)$($G.Eye) $($C.Wht)$S...OK   `n"
    }
    Start-Sleep -Milliseconds 200
}

function Render-PreFlight {
    if ($Host.Name -match "ISE") { return }
    Clear-Host
    try { $W = [Console]::WindowWidth } catch { $W = 80 }
    if ($W -lt 40) { $W = 40 }
    
    $Sep = $G.H * ($W - 5)

    Write-Host "`n $($C.Teal)$($G.Eye) VDOCOON OMEGA INFINITY // PRE-FLIGHT$($C.Rst)"
    Write-Host " $($C.Dim)$Sep$($C.Rst)"
    Write-Host " TARGET:   $($C.Wht)$($Store.Config.LibraryPath)$($C.Rst)"
    Write-Host " SCRATCH:  $($C.Wht)$(if($Store.Config.ScratchPath){$Store.Config.ScratchPath}else{"[AUTO]"})$($C.Rst)"
    Write-Host " HARDWARE: $($C.Mag)$($Store.Hardware.Type) ($($Store.Hardware.Enc))$($C.Rst)"
    Write-Host " PRIORITY: $($C.Blu)$($Store.Config.ProcessPriority)$($C.Rst)"
    Write-Host " STRATEGY: $($C.Cyn)$($Store.Config.HardwareStrategy)$($C.Rst)"
    Write-Host " SPEED:    $($C.Gld)$($Store.Config.Preset)$($C.Rst) | CRF: $($C.Ylw)$($Store.Config.CRF)$($C.Rst)"
    Write-Host " AUDIO:    $($C.Cyn)$($Store.Config.AudioMode)$($C.Rst)"
    
    $Mode = if ($Watch) { "$($C.Org)SENTINEL (AUTONOMOUS)$($C.Rst)" } else { "$($C.Blu)BATCH (ONE SHOT)$($C.Rst)" }
    Write-Host " MODE:     $Mode"
    
    Write-Host " $($C.Dim)$Sep$($C.Rst)"
    Write-Host " $($C.Grn)ENGAGING IN 3 SECONDS...$($C.Rst)"
    Start-Sleep 3
    if ($Watch) { Dispatch-Log "Sentinel Mode Engaged." "SYS" }
}

function Render-Frame {
    if ($Host.Name -match "ISE") { return }
    if ((Get-Date) - $Store.Runtime.LastRender -lt [TimeSpan]::FromMilliseconds(100)) { return }
    $Store.Runtime.LastRender = Get-Date

    try { $W = [Console]::WindowWidth } catch { $W = 80 }
    if ($W -lt 40) { $W = 40 } 

    $Buf = New-Object System.Text.StringBuilder
    
    function Add-Line($Txt) { 
        $Clean = $Txt -replace "\x1B\[[0-9;]*[a-zA-Z]",""
        $Len = $Clean.Length; $Pad = [math]::Max(0, $W - $Len - 1)
        [void]$Buf.AppendLine("$Txt$(' '*$Pad)") 
    }
    
    $Span = (Get-Date) - $Global:StartTime
    $Up = if ($Span.TotalHours -ge 24) { $Span.ToString("dd\.hh\:mm\:ss") } else { $Span.ToString("hh\:mm\:ss") }

    $Status = if($Store.Job.IsPaused) { "$($C.Org)[STASIS]" } elseif($Store.State -eq "DRAINING") { "$($C.Ylw)[DRAINING]" } elseif ($Watch -and $Store.State -eq "SCAN") { "$($C.Org)[SENTINEL]" } elseif ($DryRun) { "$($C.Mag)[SIMULATION]" } else { "$($C.Grn)[LIVE]" }
    Add-Line "$($C.Teal)$($G.Play) VDOCOON $($C.Bld)OMEGA v47.7$($C.Rst) $Status $($C.Dim)// QUASAR // UP: $Up$($C.Rst)"
    
    $Sv = "{0:N2} GB" -f ($Store.Stats.BytesSaved / 1GB)
    $SvPct = if ($Store.Stats.SizeProcessed -gt 0) { [math]::Round(($Store.Stats.BytesSaved / $Store.Stats.SizeProcessed) * 100, 1) } else { 0 }
    Add-Line "$($C.Wht)DONE: $($C.Grn)$($Store.Stats.Processed)   $($C.Wht)SKIP: $($C.Ylw)$($Store.Stats.Skipped)   $($C.Wht)FAIL: $($C.Red)$($Store.Stats.Errors)   $($C.Wht)SAVED: $($C.Cyn)$Sv ($SvPct%)$($C.Rst)"
    
    if ($Watch) {
        $Next = $Store.Runtime.NextScan.ToString("HH:mm:ss")
        Add-Line "$($C.Org) SENTINEL ACTIVE $($C.Rst) Run: #$($Store.Stats.RunCount) | Next Scan: $($C.Ylw)$Next$($C.Rst)" 
        if ($Host.UI.RawUI.WindowTitle -ne "VdoCoon Sentinel") { $Host.UI.RawUI.WindowTitle = "VdoCoon Sentinel" }
    } else {
        $Elapsed = (Get-Date) - $Store.Runtime.MissionStart
        $MissionPct = if($Store.Stats.TotalBytes -gt 0){ ($Store.Stats.BytesDone / $Store.Stats.TotalBytes) * 100 } else { 0 }
        if($MissionPct -gt 100){$MissionPct=100}
        
        $MissionETA = "--:--"
        if ($MissionPct -gt 0.1) {
            $TotalSecs = $Elapsed.TotalSeconds / ($MissionPct/100); $RemSecs = $TotalSecs - $Elapsed.TotalSeconds
            $MissionETA = [TimeSpan]::FromSeconds($RemSecs).ToString("hh\:mm\:ss")
        }
        $BW=[math]::Max(0, $W - 22); $Fl=[math]::Floor(($MissionPct/100)*$BW); if($Fl -lt 0){$Fl=0}; if($Fl -gt $BW){$Fl=$BW}
        $MBar="$($C.Dim)[$($C.Rst)$($C.Cyn)"+($G.BarF*$Fl)+"$($C.Dim)"+($G.BarE*($BW-$Fl))+"$($C.Dim)]$($C.Rst)"
        Add-Line " BATCH:  $MBar $($C.Wht)$("{0,3}" -f [int]$MissionPct)%$($C.Rst)"
        $Host.UI.RawUI.WindowTitle = "VdoCoon [$([int]$MissionPct)%]"
    }
    Add-Line "$($C.Dim)$($G.H * $W)$($C.Rst)"

    if ($Store.State -in "WORK", "DRAINING") {
        $FStr = $Store.Job.File; $MaxNameLen = [math]::Max(10, $W - 15)
        if ($FStr.Length -gt $MaxNameLen) { $FStr = "..." + $FStr.Substring($FStr.Length - $MaxNameLen) }
        
        $BW=[math]::Max(0, $W-22)
        if ($Store.Job.Pct -lt 0) {
            $Pat="////      "; $IndBar=""; while($IndBar.Length -lt $BW){$IndBar+=$Pat}; if($IndBar.Length -gt $BW){$IndBar=$IndBar.Substring(0,$BW)}
            $Bar="$($C.Dim)[$($C.Rst)$($C.Cyn)$IndBar$($C.Dim)]$($C.Rst)"
            Add-Line " JOB:    $Bar $($C.Wht)   ?$($C.Rst)"
        } else {
            $Fl=[math]::Floor(($Store.Job.Pct/100)*$BW); if($Fl -lt 0){$Fl=0}; if($Fl -gt $BW){$Fl=$BW}
            $Color = if($Store.Job.IsPaused){$C.Org}else{$C.Grn}
            $Bar="$($C.Dim)[$($C.Rst)$Color"+($G.BarF*$Fl)+"$($C.Dim)"+($G.BarE*($BW-$Fl))+"$($C.Dim)]$($C.Rst)"
            Add-Line " JOB:    $Bar $($C.Wht)$("{0,3}" -f [int]$Store.Job.Pct)%$($C.Rst)"
        }
        $SpdStr = "{0:N2}x" -f $Store.Job.Speed
        Add-Line " TELE:   $($C.Grn)$SpdStr @ $($Store.Job.FPS) fps$($C.Rst) | ETA: $($C.Ylw)$($Store.Job.ETA)$($C.Rst)"
        
        $TitlePrefix = if ($Store.Job.IsPaused) { "[PAUSED]" } elseif ($Store.Job.Pct -lt 0) { "[BUSY]" } else { "[$([int]$Store.Job.Pct)%]" }
        $CleanName = if ($Store.Job.File.Length -gt 64) { $Store.Job.File.Substring(0,61)+"..." } else { $Store.Job.File }
        $Host.UI.RawUI.WindowTitle = "$TitlePrefix $CleanName"
    } else {
        Add-Line "$($C.Ylw) SYSTEM STANDBY: $($Store.State) $($C.Rst)"; Add-Line ""; Add-Line ""; Add-Line ""; Add-Line ""; Add-Line ""
    }

    Add-Line "$($C.Dim)$($G.H * $W)$($C.Rst)"
    foreach ($L in $Store.Logs) { 
        $CleanL = $L -replace "\x1B\[[0-9;]*[a-zA-Z]",""
        if ($CleanL.Length -gt ($W - 2)) { Add-Line $L } else { Add-Line $L }
    }
    Add-Line "$($C.Dim)$($G.H * $W)$($C.Rst)"
    
    $Ctrl = if($Store.State -eq "DRAINING") { "$($C.Red)STOPPING AFTER CURRENT..." } else { "$($C.Wht)[S]$($C.Dim)kip   $($C.Wht)[G]$($C.Dim)raceful Stop   $($C.Wht)[P]$($C.Dim)ause" }
    Add-Line " $Ctrl"
    [Console]::SetCursorPosition(0,0); [Console]::Write($Buf.ToString())
}
# endregion

# region [ 4. LOGIC ADAPTERS ] ================================================
function Mount-Configuration {
    $CfgPath = Join-Path $Global:Root "VdoCoon.json"
    $PathToScan = $Target
    $ConfigLoaded = $false

    if (Test-Path -LiteralPath $CfgPath) { 
        try { 
            $J=Get-Content -LiteralPath $CfgPath -Raw | ConvertFrom-Json
            $PathToScan = $J.LibraryPath
            $ConfigLoaded = $true
        } catch { 
            Dispatch-Log "Config Corrupt. Resetting." "WARN"; $ResetConfig = $true 
        } 
    }

    if ($Watch -and (-not $ConfigLoaded -or $ResetConfig)) {
        Dispatch-Log "AUTONOMOUS: Loading Safe Defaults." "SYS"
        if ([string]::IsNullOrWhiteSpace($PathToScan)) { $PathToScan = $Global:Root }
        $Store.Config = @{
            LibraryPath=$PathToScan; Resolution="Original"; CRF=24; Preset="medium"; 
            AudioMode="Opus"; PostAction="None"; ProcessPriority="BelowNormal"; 
            HardwareStrategy="Auto"; ScratchPath=""; Tag="VdoCoon_Omega"
        }
        $Store.Config | ConvertTo-Json | Set-Content -LiteralPath $CfgPath
        return
    }

    if ($ResetConfig -or -not $ConfigLoaded -or [string]::IsNullOrWhiteSpace($PathToScan)) {
        if (-not ($Host.Name -match "ISE")) { Clear-Host }
        Write-Host " [VDOCOON OMEGA CONFIG]" -ForegroundColor Cyan
        
        if ([string]::IsNullOrWhiteSpace($PathToScan)) { $PathToScan = (Read-Host " Target Path (File/Folder)").Trim('"') }
        
        Write-Host "`n [QUALITY]" -ForegroundColor Yellow; 
        Write-Host " [1] Archival (CRF 18) - Visually Lossless" 
        Write-Host " [2] Balanced (CRF 24) - Best for Streaming"
        Write-Host " [3] Compact  (CRF 28) - Max Space Saving"
        $Q=Read-Host " Select [1-3] (Def: 2)"; $CRF=switch($Q){"1"{18}"3"{28}Default{24}}

        Write-Host "`n [SPEED]" -ForegroundColor Yellow; 
        Write-Host " [1] Slow   (Best Compression)"
        Write-Host " [2] Medium (Balanced)"
        Write-Host " [3] Fast   (Quick Draft)"
        $S=Read-Host " Select [1-3] (Def: 2)"; $Pre=switch($S){"1"{"slow"}"3"{"fast"}Default{"medium"}}

        Write-Host "`n [PRIORITY]" -ForegroundColor Yellow; 
        Write-Host " [1] Normal (Fastest, High CPU)"
        Write-Host " [2] Below Normal (Recommended)"
        Write-Host " [3] Idle (Invisible)"
        $Pr=Read-Host " Select [1-3] (Def: 2)"; $Prio=switch($Pr){"1"{"Normal"}"3"{"Idle"}Default{"BelowNormal"}}

        Write-Host "`n [HARDWARE]" -ForegroundColor Yellow; 
        Write-Host " [1] Auto (Prefer GPU, Fallback to CPU)"
        Write-Host " [2] CPU Only (Force Software - Stable)"
        $H=Read-Host " Select [1-2] (Def: 1)"; $Strat=switch($H){"2"{"CPU"}Default{"Auto"}}

        Write-Host "`n [SCRATCH DISK] (Optional)" -ForegroundColor Yellow; 
        Write-Host " Enter a path for temp files (e.g., D:\Temp) if C: is full."
        Write-Host " Leave empty to use source folder (Default)."
        $Scratch = (Read-Host " Scratch Path").Trim('"')

        Write-Host "`n [AUDIO]" -ForegroundColor Yellow; 
        Write-Host " [1] Modern (Opus) - Transparent"
        Write-Host " [2] Universal (AAC) - Compatible"
        Write-Host " [3] Copy (Passthrough) - Lossless"
        $A=Read-Host " Select [1-3] (Def: 1)"; $AC=switch($A){"2"{"AAC"}"3"{"Copy"}Default{"Opus"}}

        Write-Host "`n [RESOLUTION]" -ForegroundColor Yellow; 
        Write-Host " [1] Original (No Resize)"; Write-Host " [2] 1080p"; Write-Host " [3] 720p"; Write-Host " [4] 480p"
        $R=Read-Host " Select [1-4] (Def: 1)"; $Res=switch($R){"2"{"1080p"}"3"{"720p"}"4"{"480p"}Default{"Original"}}

        Write-Host "`n [ACTION COMPLETE]" -ForegroundColor Yellow; 
        Write-Host " [1] None"; Write-Host " [2] Shutdown PC"; Write-Host " [3] Sleep PC"
        $P=Read-Host " Select [1-3] (Def: 1)"; $Act=switch($P){"2"{"Shutdown"}"3"{"Sleep"}Default{"None"}}

        $Store.Config = @{LibraryPath=$PathToScan; Resolution=$Res; CRF=$CRF; Preset=$Pre; AudioMode=$AC; PostAction=$Act; ProcessPriority=$Prio; HardwareStrategy=$Strat; ScratchPath=$Scratch; Tag="VdoCoon_Omega"}
        $Store.Config | ConvertTo-Json | Set-Content -LiteralPath $CfgPath
    } else { 
        $J=Get-Content -LiteralPath $CfgPath -Raw | ConvertFrom-Json; $J.PSObject.Properties|%{$Store.Config[$_.Name]=$_.Value}
        if ($Target) { $Store.Config.LibraryPath = $Target }
        if (-not $Store.Config.ProcessPriority) { $Store.Config.ProcessPriority = "BelowNormal" }
        if (-not $Store.Config.HardwareStrategy) { $Store.Config.HardwareStrategy = "Auto" }
        if (-not $Store.Config.Preset) { $Store.Config.Preset = "medium" }
    }
    if ($Watch -and $Store.Config.PostAction -eq "Shutdown") { $Store.Config.PostAction = "None"; Dispatch-Log "Safety: Shutdown disabled in Watch Mode." "WARN" }
}

function Detect-Hardware {
    param($ForceCPU = $false)
    if ($ForceCPU -or $Store.Config.HardwareStrategy -eq "CPU") {
         $Store.Hardware=@{Type="CPU"; Enc="libx265"; Args=""; Filter="scale"; Rc="-crf"; ProfileCmd="-pix_fmt yuv420p10le"; PresetFlag="-preset $($Store.Config.Preset)"; FallbackActive=$true}
         return
    }
    Dispatch-Log "Probing Hardware..." "SYS"
    try {
        $Out = & $Global:FFMPEG -v quiet -hwaccels
    } catch {
        Dispatch-Log "Hardware Probe Crashed: $_" "ERR"; Detect-Hardware -ForceCPU $true; return
    }
    $S = $Store.Config.Preset
    
    if ($Out -match "cuda") { 
        $P = switch($S){"slow"{"p7"}"fast"{"p1"}Default{"p4"}}
        # [SCALING ENGINE] Use separate property for base filter type
        $Store.Hardware=@{Type="NVIDIA"; Enc="hevc_nvenc"; Args="-hwaccel cuda -hwaccel_output_format cuda"; Filter="scale_cuda"; Rc="-rc constqp -qp"; ProfileCmd="-profile:v main10"; PresetFlag="-preset $P"; FallbackActive=$false}
        return 
    }
    if ($Out -match "qsv")  { 
        $P = switch($S){"slow"{"veryslow"}"fast"{"veryfast"}Default{"medium"}}
        $Store.Hardware=@{Type="INTEL"; Enc="hevc_qsv"; Args="-init_hw_device qsv=qsv:MFX_IMPL_hw -filter_hw_device qsv"; Filter="vpp_qsv"; Rc="-global_quality"; ProfileCmd="-profile:v main10"; PresetFlag="-preset $P"; FallbackActive=$false}
        return 
    }
    Detect-Hardware -ForceCPU $true
}

function Analyze-Media ($Meta) {
    $Vid = $Meta.streams | ? {$_.codec_type -eq "video" -and $_.disposition.attached_pic -ne 1 -and $_.codec_name -ne "mjpeg"} | Sort width -Descending | Select -First 1
    if (-not $Vid) { $Vid = $Meta.streams | ? {$_.codec_type -eq "video"} | Select -First 1 }
    $MaxChan = 2; $Meta.streams | ? {$_.codec_type -eq "audio"} | % { if($_.channels -gt $MaxChan){ $MaxChan = $_.channels } }
    
    $HDR = @{Exists=$false; Primaries=""; Transfer=""; Space=""}
    if ($Vid) {
        if ($Vid.color_primaries -and $Vid.color_primaries -ne "unknown") {
            $HDR.Exists = $true
            $HDR.Primaries = $Vid.color_primaries
            $HDR.Transfer = $Vid.color_transfer
            $HDR.Space = $Vid.color_space
        }
    }

    return @{ VIdx=$Vid.index; H=$Vid.height; Channels=$MaxChan; Codec=$Vid.codec_name; HDR=$HDR }
}

function Hunt-Orphans ($Path) {
    if ([string]::IsNullOrWhiteSpace($Path) -or -not (Test-Path -LiteralPath $Path)) { return }
    Dispatch-Log "Scanning for orphans in: $Path" "SYS"
    try {
        $Orphans = Get-ChildItem -LiteralPath $Path -Recurse -Filter "_vc_*.mkv" -ErrorAction SilentlyContinue
        foreach ($O in $Orphans) {
            try {
                $Stream = [System.IO.File]::Open($O.FullName, 'Open', 'ReadWrite', 'None')
                $Stream.Close(); $Stream.Dispose()
                
                if ($O.LastWriteTime -lt (Get-Date).AddHours(-2)) { 
                    Dispatch-Log "Purged Orphan: $($O.Name)" "WARN" 
                    Remove-Item -LiteralPath $O.FullName -Force -ErrorAction SilentlyContinue 
                }
            } catch {}
        }
    } catch {}
}
# endregion

# region [ 5. EXECUTION CORE ] ================================================
function Execute-FFmpeg ($Arguments, $FileDur, $FileLen, $StartBytes) {
    $Store.Job.Task = "Encoding"; $Store.FFLog.Clear()
    $Store.Runtime.LastActivity = Get-Date

    $Proc = New-Object System.Diagnostics.Process
    $Proc.StartInfo.FileName = $Global:FFMPEG; $Proc.StartInfo.Arguments = $Arguments
    $Proc.StartInfo.UseShellExecute = $false; $Proc.StartInfo.RedirectStandardError = $true; $Proc.StartInfo.CreateNoWindow = $true
    [void]$Proc.Start(); $Store.Runtime.CurrentPID = $Proc.Id
    
    try { 
        $Proc.PriorityClass = $Store.Config.ProcessPriority 
        $Store.Job.Priority = $Store.Config.ProcessPriority 
    } catch {}

    while (-not $Proc.HasExited) {
        if ($Proc.StandardError.Peek() -ge 0) {
            $Line = $Proc.StandardError.ReadLine()
            if ($Line) {
                $Store.Runtime.LastActivity = Get-Date
                [void]$Store.FFLog.Add($Line); if ($Store.FFLog.Count -gt 50) { $Store.FFLog.RemoveAt(0) }
                if ($Line -match "time=(\d{2}):(\d{2}):(\d{2}\.\d{2})") {
                    $Cur = ([int]$Matches[1]*3600)+([int]$Matches[2]*60)+[double]$Matches[3]
                    if ($FileDur -gt 0) {
                        $Store.Job.Pct = [math]::Min(100, ($Cur/$FileDur)*100)
                        $Store.Stats.BytesDone = [long]($StartBytes + ($FileLen * ($Store.Job.Pct / 100)))
                        Set-TaskbarProgress $Store.Job.Pct 100 "Normal"
                        if ($Line -match "speed=\s*(\d+\.?\d*)x") { 
                            $RawSpd=[double]$Matches[1]; 
                            if($Store.Job.Speed -eq 0){$Store.Job.Speed = $RawSpd}
                            else{$Store.Job.Speed = ($Store.Job.Speed * 0.9) + ($RawSpd * 0.1)} 
                            if($Store.Job.Speed-gt 0.01){$Store.Job.ETA=[TimeSpan]::FromSeconds(($FileDur-$Cur)/$Store.Job.Speed).ToString("hh\:mm\:ss")} 
                        }
                    } else {
                        $Store.Job.Pct = -1; $Store.Job.ETA = "Calculating..."
                        Set-TaskbarProgress 0 0 "Indeterminate"
                    }
                }
                if ($Line -match "fps=\s*(\d+)") { $Store.Job.FPS = $Matches[1] }
            }
        } else {
            if (-not $Store.Job.IsPaused -and ((Get-Date) - $Store.Runtime.LastActivity).TotalSeconds -gt 120) {
                Dispatch-Log "WATCHDOG: Process Hung (120s). Killing." "CRIT"
                Stop-Process -Id $Proc.Id -Force; break
            }
        }
        
        if ([Console]::KeyAvailable) {
            $K = [Console]::ReadKey($true).Key
            if ($K -eq "S") { Dispatch-Log "USER: SKIP." "USER"; $Store.Job.SkippedByUser=$true; Stop-Process -Id $Proc.Id -Force; }
            if ($K -eq "G") { Dispatch-Log "USER: STOPPING SOON." "USER"; $Store.State = "DRAINING" }
            if ($K -eq "P" -and -not $Store.Job.IsPaused) { $Store.Job.IsPaused=$true; [Win32.Win32]::NtSuspendProcess($Proc.Handle); Dispatch-Log "SYSTEM PAUSED (STASIS)" "PAUSE"; Set-TaskbarProgress $Store.Job.Pct 100 "Paused" }
            if ($K -eq "R" -and $Store.Job.IsPaused) { $Store.Job.IsPaused=$false; [Win32.Win32]::NtResumeProcess($Proc.Handle); Dispatch-Log "SYSTEM RESUMED" "SYS" }
        }
        Render-Frame; Start-Sleep -Milliseconds 100
    }
    
    $PostMortem = $Proc.StandardError.ReadToEnd()
    if (-not [string]::IsNullOrWhiteSpace($PostMortem)) { 
        $Lines = $PostMortem -split "`n"; foreach($L in $Lines){[void]$Store.FFLog.Add($L)}
    }
    
    $Exit = $Proc.ExitCode; $Proc.Dispose(); $Store.Runtime.CurrentPID = $null; Set-TaskbarProgress 0 100 "NoProgress"; $Store.Job.IsPaused=$false
    return $Exit
}

function Build-FFmpeg-Cmd ($File, $Info, $HW) {
    $ACodec = "libopus"; $ABitrate = ""; $AFilt = ""
    if ($Store.Config.AudioMode -eq "Copy") { $ACodec = "copy"; $AudioDesc = "Passthrough" } 
    elseif ($Store.Config.AudioMode -eq "AAC") { $ACodec = "aac"; $ABitrate = "-b:a 224k"; $AudioDesc = "AAC 224k" } 
    else {
        $BitR = if($Info.Channels -ge 6){320000}else{128000}
        $ABitrate = "-b:a $BitR"; $AFilt = "-af `"aformat=channel_layouts=7.1|5.1|stereo`""
        $AudioDesc = "Opus $([math]::Round($BitR/1000))k"
    }

    $Store.Job.AudioChannels = $Info.Channels; $Store.Job.SrcCodec = $Info.Codec
    $Store.Job.Decision = "Encode $($Info.H)p [10-Bit] | $AudioDesc"
    
    $Filters = @(); $TgtH = switch($Store.Config.Resolution) { "1080p"{1080} "720p"{720} "480p"{480} Default{0} }
    $Scale = ($TgtH -gt 0 -and $Info.H -gt $TgtH)
    
    # [SCALING ENGINE] Construct correct filter chain syntax for resizing
    if ($HW.Type -eq "NVIDIA") { 
        if ($Scale) { $Filters += "$($HW.Filter)=w=-2:h=$TgtH:format=p010le" } 
        else { $Filters += "$($HW.Filter)=format=p010le" } 
    } 
    elseif ($HW.Type -eq "INTEL") { 
        if ($Scale) { $Filters += "$($HW.Filter)=w=-2:h=$TgtH" } 
    } 
    else { 
        if ($Scale) { $Filters += "$($HW.Filter)=-2:$TgtH" } 
    }
    
    if (-not $Filters -and $HW.Type -ne "CPU" -and $HW.Type -ne "NVIDIA") { $Filters += "null" }
    $VF = if ($Filters) { "-vf `"$($Filters -join ",")`"" } else { "" }
    
    $EncCmd = "-c:v $($HW.Enc) $($HW.ProfileCmd) $($HW.PresetFlag) $($HW.Rc) $($Store.Config.CRF)"; if ($HW.Type -eq "NVIDIA") { $EncCmd += " -b:v 0" }

    if ($Info.HDR.Exists) {
        $EncCmd += " -color_primaries $($Info.HDR.Primaries) -color_trc $($Info.HDR.Transfer) -colorspace $($Info.HDR.Space)"
        if ($HW.Type -eq "NVIDIA") { $Store.Job.Decision += " [HDR:CUDA]" } else { $Store.Job.Decision += " [HDR]" }
    }

    $SafePath = $File.FullName.Replace('"', '\"')
    $SafeTemp = $Store.Runtime.TempFile.Replace('"', '\"')

    return "$($HW.Args) -i `"$SafePath`" -map 0:$($Info.VIdx) -map 0:a? -map 0:s? -map 0:t? -map_metadata 0 -map_chapters 0 " +
            "$EncCmd $VF " +
            "-c:a $ACodec $ABitrate $AFilt -c:s copy -c:t copy " +
            "-metadata comment=$($Store.Config.Tag) -metadata:s:v:0 comment=$($Store.Config.Tag) " +
            "-y `"$SafeTemp`""
}

function Process-Item ($File) {
    if ($Store.State -eq "DRAINING") { return }
    $FN = $File.FullName
    if ($Store.Blacklist.ContainsKey($FN) -and $Store.Blacklist[$FN] -ge 3) {
        Dispatch-Log "QUARANTINE: $FN (3 Failures)" "WARN"; $Store.Stats.Errors++; return
    }

    $Store.Job.File = $File.Name; $Store.Job.Task = "Analysis"; $Store.Job.Pct = 0; $Store.Job.Speed=0; $Store.Job.SkippedByUser=$false; Render-Frame
    if (-not (Test-WriteAccess $File.DirectoryName)) { Dispatch-Log "SKIP: Read-Only/Locked Dir." "WARN"; $Store.Stats.Skipped++; $Store.Stats.Errors++; return }
    if (-not (Test-FileStability $FN)) { Dispatch-Log "WAIT: File Locked/Transferring..." "WARN"; return }

    try { 
        $JsonRaw = & $Global:FFPROBE -v quiet -print_format json -show_format -show_streams $File.FullName
        $Meta = $JsonRaw | ConvertFrom-Json 
    } catch { 
        Dispatch-Log "Probe Failed: Corrupt/AccessDenied." "ERR"; $Store.Stats.Errors++; $Store.Stats.BytesDone += $File.Length; return 
    }

    $Tags = @{}; if ($Meta.format.tags) { $Meta.format.tags.PSObject.Properties | % { $Tags[$_.Name] = $_.Value } }
    $TStr = $Tags.Values -join " "; if ($TStr -match $Store.Config.Tag -or $TStr -match "WinX") { 
        Dispatch-Log "Already Optimized." "INFO"; $Store.Stats.Skipped++; $Store.Stats.BytesDone += $File.Length; 
        Write-Ledger $File.Name "Skipped" "Already Optimized"
        $Store.Report.Add([pscustomobject]@{File=$File.Name; Status="Skipped"; SizeMB=[math]::Round($File.Length/1MB); Result="Optimized"}); return 
    }

    $Info = Analyze-Media $Meta
    if ($null -eq $Info.VIdx) { Dispatch-Log "SKIP: No Video Stream." "WARN"; $Store.Stats.Skipped++; return }
    
    # [SMART STORAGE ROUTING]
    $Required = [long]($File.Length * 1.5)
    $TargetDir = $File.DirectoryName
    $UsingScratch = $false
    
    $Free = Get-DriveFreeSpace $TargetDir
    if ($Free -lt $Required) {
        if ($Store.Config.ScratchPath -and (Test-Path -LiteralPath $Store.Config.ScratchPath) -and (Get-DriveFreeSpace $Store.Config.ScratchPath) -gt $Required) {
             $TargetDir = $Store.Config.ScratchPath; $UsingScratch = $true
             Dispatch-Log "Local Full. Rerouting to Scratch." "SYS"
        } else {
             $FreeMB = [math]::Round($Free/1MB); $ReqMB = [math]::Round($Required/1MB)
             Dispatch-Log "SKIP: Disk Full (Free: ${FreeMB}MB, Req: ${ReqMB}MB)." "WARN"; $Store.Stats.Skipped++; return
        }
    }
    
    $Store.Runtime.TempFile = Join-Path $TargetDir ("_vc_" + [Guid]::NewGuid().ToString().Substring(0,6) + ".mkv")
    
    # LAZARUS PROTOCOL START
    $Attempts = 0; $Success = $false
    $GlobalSnapshot = $Store.Stats.BytesDone

    do {
        $Store.Stats.BytesDone = $GlobalSnapshot
        $UseHW = if ($Attempts -gt 0 -and $Store.Hardware.Type -ne "CPU") { Detect-Hardware -ForceCPU $true; $Store.Hardware } else { $Store.Hardware }
        $CmdArgs = Build-FFmpeg-Cmd $File $Info $UseHW
        
        if ($DryRun) { Dispatch-Log "[SIMULATION] CMD: ffmpeg $CmdArgs" "DRY"; Start-Sleep -Milliseconds 200; $Store.Stats.BytesDone += $File.Length; return }
        
        $Dur=0; [void][double]::TryParse($Meta.format.duration, [ref]$Dur)
        $ExitCode = Execute-FFmpeg $CmdArgs $Dur $File.Length $GlobalSnapshot
        
        if ($Store.Job.SkippedByUser) {
             Dispatch-Log "Skipped by User." "WARN"; if(Test-Path -LiteralPath $Store.Runtime.TempFile){Remove-Item -LiteralPath $Store.Runtime.TempFile -Force}; 
             Write-Ledger $File.Name "Skipped" "User Action"
             $Store.Report.Add([pscustomobject]@{File=$File.Name; Status="Skipped"; SizeMB=[math]::Round($File.Length/1MB); Result="UserAction"}); return
        }

        if ($ExitCode -eq 0) {
            $Success = $true
        } else {
            if ($UseHW.Type -ne "CPU" -and $Attempts -eq 0 -and $Store.Config.HardwareStrategy -ne "CPU") {
                Dispatch-Log "Hardware Encoding Failed ($ExitCode). Invoking LAZARUS (CPU Fallback)..." "LAZARUS"
                Invoke-Sonic "Lazarus"; $Attempts++
            } else {
                Dispatch-Log "Fatal Error ($ExitCode). Forensic Dump:" "ERR"
                Dispatch-Log "CMD: ffmpeg $CmdArgs" "ERR"
                $Store.FFLog | Select-Object -Last 5 | % { Dispatch-Log ">> $_" "ERR" }
                $Attempts = 99
            }
        }
    } while (-not $Success -and $Attempts -eq 1)

    if ($Success) {
        $Old = $File.Length; $New = (Get-Item -LiteralPath $Store.Runtime.TempFile).Length
        if ($New -ge $Old -and $Old -gt 50MB) {
            Dispatch-Log "Bloat ($([math]::Round($New/1MB))MB). Discarding." "WARN"; Remove-Item -LiteralPath $Store.Runtime.TempFile -Force; $Store.Stats.Skipped++
            Write-Ledger $File.Name "Skipped" "Bloat Detected"
            $Store.Report.Add([pscustomobject]@{File=$File.Name; Status="Skipped"; SizeMB=[math]::Round($File.Length/1MB); Result="Bloat"})
        } else {
            $SwapSuccess = $false
            for ($i=1; $i -le 3; $i++) {
                try {
                    $OriginalTime = $File.LastWriteTime
                    $Bak = "$($File.FullName).bak"; Move-Item -LiteralPath $File.FullName -Destination $Bak -Force -ErrorAction Stop
                    Move-Item -LiteralPath $Store.Runtime.TempFile -Destination ([System.IO.Path]::ChangeExtension($File.FullName, ".mkv")) -Force -ErrorAction Stop
                    $NewFile = Get-Item -LiteralPath ([System.IO.Path]::ChangeExtension($File.FullName, ".mkv")); $NewFile.LastWriteTime = $OriginalTime
                    Remove-Item -LiteralPath $Bak -Force; $SwapSuccess = $true; break
                } catch {
                    if($i -lt 3){Start-Sleep -Milliseconds 500}
                }
            }

            if ($SwapSuccess) {
                $Saved = $Old - $New; $Store.Stats.BytesSaved += $Saved; $Store.Stats.Processed++; $Store.Stats.SizeProcessed += $Old
                $Ratio = [math]::Round(($Saved / $Old) * 100, 0)
                Dispatch-Log "Success. Saved $([math]::Round($Saved/1MB)) MB ($Ratio%)" "OK"
                Write-Ledger $File.Name "Success" "Saved $([math]::Round($Saved/1MB)) MB"
                $Store.Report.Add([pscustomobject]@{File=$File.Name; Status="Success"; SizeMB=[math]::Round($New/1MB); Result="-$([math]::Round($Saved/1MB)) MB ($Ratio%)"})
                if ($Store.Blacklist.ContainsKey($FN)) { $Store.Blacklist.Remove($FN) }
            } else {
                 Dispatch-Log "Swap Failed (Locked). Restoring." "ERR"; if(Test-Path -LiteralPath $Bak){Move-Item -LiteralPath $Bak -Destination $File.FullName -Force}; $Store.Stats.Errors++ 
                 Write-Ledger $File.Name "Error" "File Lock Failure"
                 $Store.Report.Add([pscustomobject]@{File=$File.Name; Status="Error"; SizeMB=[math]::Round($File.Length/1MB); Result="FileLock"})
            }
        }
    } else {
        $Store.Stats.Errors++
        if(Test-Path -LiteralPath $Store.Runtime.TempFile){Remove-Item -LiteralPath $Store.Runtime.TempFile -Force}
        Write-Ledger $File.Name "Error" "FFmpeg Exit Code $ExitCode"
        $Store.Report.Add([pscustomobject]@{File=$File.Name; Status="Error"; SizeMB=[math]::Round($File.Length/1MB); Result="Failure:$ExitCode"})
        if (-not $Store.Blacklist.ContainsKey($FN)) { $Store.Blacklist[$FN] = 1 } else { $Store.Blacklist[$FN]++ }
    }
    
    if ($Store.Hardware.FallbackActive) { Detect-Hardware } 
    $Store.Stats.BytesDone = $GlobalSnapshot + $File.Length 
    $Store.Runtime.TempFile = $null
}
# endregion

# region [ 6. MAIN MANIFOLD ] =================================================
try {
    if (-not ($Host.Name -match "ISE")) { [Console]::CursorVisible = $false }
    
    function Verify-Binary ($Path) {
        if (-not (Test-Path -LiteralPath $Path)) { return $false }
        try {
            $Size = (Get-Item -LiteralPath $Path).Length; if ($Size -lt 10MB) { return $false }
            Unblock-File -LiteralPath $Path -ErrorAction SilentlyContinue
            $P = Start-Process -FilePath $Path -ArgumentList "-version" -NoNewWindow -Wait -PassThru -ErrorAction Stop; return ($P.ExitCode -eq 0)
        } catch { return $false }
    }

    $LocalFFMPEG = Join-Path $Global:Root "ffmpeg.exe"
    $LocalFFPROBE = Join-Path $Global:Root "ffprobe.exe"
    
    $FF_OK = Verify-Binary $LocalFFMPEG; $FP_OK = Verify-Binary $LocalFFPROBE
    if (-not $FF_OK -or -not $FP_OK) {
        if (Test-Path -LiteralPath $LocalFFMPEG) { Remove-Item -LiteralPath $LocalFFMPEG -Force -ErrorAction SilentlyContinue }
        if (Test-Path -LiteralPath $LocalFFPROBE) { Remove-Item -LiteralPath $LocalFFPROBE -Force -ErrorAction SilentlyContinue }
        Bootstrap-Binaries
        $Global:FFMPEG = $LocalFFMPEG; $Global:FFPROBE = $LocalFFPROBE
    } else {
        $Global:FFMPEG = $LocalFFMPEG; $Global:FFPROBE = $LocalFFPROBE
    }
    if (-not (Verify-Binary $Global:FFMPEG)) { Throw "CRITICAL: FFmpeg binary unusable." }

    Mount-Configuration; Rotate-Logs; Render-BootSequence; Detect-Hardware
    Render-PreFlight
    if (-not $DryRun) { Hunt-Orphans $Store.Config.LibraryPath }
    
    do {
        $Store.Stats.RunCount++; Rotate-Logs; $Store.State = "SCAN"; Render-Frame
        
        if (Test-Path -LiteralPath $Store.Config.LibraryPath -PathType Leaf) {
            $Q = @(Get-Item -LiteralPath $Store.Config.LibraryPath)
        } else {
            $RawQ = Get-ChildItem -LiteralPath $Store.Config.LibraryPath -Recurse -Include "*.mkv","*.mp4","*.avi","*.mov","*.m4v" | Sort Length -Descending
            $NoMediaMarkers = Get-ChildItem -LiteralPath $Store.Config.LibraryPath -Recurse -Filter ".nomedia" -Force -ErrorAction SilentlyContinue
            if ($NoMediaMarkers) {
                $Exclusions = $NoMediaMarkers | Select-Object -ExpandProperty DirectoryName | Sort-Object | Get-Unique
                $ExRegex = ($Exclusions | % { "^" + [Regex]::Escape($_) + "($|\\)" }) -join "|"
                $Q = $RawQ | ? { $_.DirectoryName -notmatch $ExRegex }
                $IgnoredCount = $RawQ.Count - $Q.Count
                if ($IgnoredCount -gt 0) { Dispatch-Log "Ignored $IgnoredCount items (.nomedia)." "INFO" }
            } else { $Q = $RawQ }
        }
        
        $Store.Stats.TotalBytes = ($Q | Measure-Object -Property Length -Sum).Sum; if($Store.Stats.TotalBytes -eq 0){$Store.Stats.TotalBytes=1}
        $Store.Stats.TotalFiles = $Q.Count
        if (-not $Watch) { Dispatch-Log "Mission: $($Q.Count) items ($([math]::Round($Store.Stats.TotalBytes/1GB,2)) GB)." "INFO" }
        
        $Store.State = "WORK"; $Store.Runtime.MissionStart = Get-Date
        foreach ($F in $Q) { 
            if ($Store.State -eq "DRAINING") { break }
            Process-Item $F
            [GC]::Collect() 
        }
        
        if ($Watch -and $Store.State -ne "DRAINING") {
            if ($Store.Report.Count -gt 500) { $Store.Report.Clear() }
            $Store.Runtime.NextScan = (Get-Date).AddSeconds(60)
            $Store.State = "SCAN"
            while ((Get-Date) -lt $Store.Runtime.NextScan) { Render-Frame; Start-Sleep -Milliseconds 500 }
        }

    } while ($Watch -and $Store.State -ne "DRAINING")
    
    Invoke-Cleanup
    if ($Store.Config.PostAction -ne "None") { 
        & $Global:Insomnia $false
        if ($Store.Config.PostAction -eq "Shutdown") { Dispatch-Log "SHUTDOWN IN 60S" "WARN"; Start-Sleep 60; Stop-Computer -Force }
        if ($Store.Config.PostAction -eq "Sleep") { [System.Windows.Forms.Application]::SetSuspendState("Suspend",$false,$false) }
    }
    
    $Store.State = "DONE"; Render-Frame; Invoke-Sonic "Done"
    Send-Toast "VdoCoon Mission Complete" "Processed $($Store.Stats.Processed) files. Saved $([math]::Round($Store.Stats.BytesSaved/1GB, 2)) GB."
    $Rpt = Generate-Manifest
    if (-not ($Host.Name -match "ISE")) { [Console]::CursorVisible = $true }
    Write-Host "`n [MISSION COMPLETE] Report: $Rpt" -ForegroundColor Green; Read-Host " Press Enter to exit."
} catch {
    if (-not ($Host.Name -match "ISE")) { [Console]::CursorVisible = $true }
    Write-Host "`n [FATAL] $_" -ForegroundColor Red; Invoke-Sonic "Error"; Send-Toast "VdoCoon Failed" "Fatal Error: $_" "Error"; Read-Host
    exit 1
}
# endregion
```
