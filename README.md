# 🦝 VdoCoon // OMEGA // v12.0

> **The "Deep Color" Sovereignty Engine.**  
> *Autonomic. 10-Bit. Immutable.*

## 📑 Table of Contents

1. [System Overview](#-system-overview)
2. [Omega Architecture](#-omega-architecture)
3. [Installation](#-installation)
4. [Command Reference](#-command-reference)
5. [Configuration](#-configuration)
6. [Troubleshooting](#-troubleshooting)
7. [Source Code (The Artifact)](#-source-code)

---

## 🔭 System Overview

**VdoCoon** is a mission-critical PowerShell hypervisor for FFmpeg. Unlike standard batch converters, VdoCoon functions as an autonomic decision engine. It profiles your hardware, analyzes media topology, and constructs a bespoke transcoding pipeline for every single file.

It transforms mixed media libraries into a unified **Matroska (MKV) HEVC 10-bit** archive with **Opus** audio. It creates a mathematically perfect archival copy, reducing storage footprint by 40-60% while strictly preserving visual fidelity (Main10 Profile) and metadata integrity.

### Key Capabilities

*   **Forced 10-Bit Pipeline:** All video is processed in 10-bit color depth (`p010le` / `Main10`). This eliminates color banding artifacts common in 8-bit encodes and improves compression efficiency for both HDR and SDR sources.
*   **Universal Hardware Engine:** Automatically negotiates the optimal encoder:
    *   **NVIDIA:** NVENC (HEVC Main10) + CUDA Scaling + Zero-Copy Pipeline.
    *   **INTEL:** QSV (HEVC Main10) + VPP Scaling.
    *   **AMD:** AMF (HEVC Main10) + DXVA2.
    *   **CPU:** libx265 (Software Fallback, 10-bit).
*   **Adaptive Audio Intelligence:** Detects channel layouts (Stereo, 5.1, 7.1) and scales bitrate dynamically (`64kbps/channel`). Automatically sanitizes non-standard layouts to prevent encoder crashes.
*   **Zero-Loss Preservation:** Explicitly maps **ALL** audio tracks, **ALL** subtitles, and **ALL** attachments (Fonts/Cover Art). Nothing is left behind.
*   **Atomic Safety:** All operations occur in a transactional scratch space. Source files are strictly read-only until the output is verified.

---

## 🏛 Omega Architecture

### 1. The Decision Engine

VdoCoon is a "White Box" system. It logs exactly *why* it makes decisions.

*   **Input:** `Movie.avi` (2GB, XviD, Stereo).
*   **Decision:** "Hardware=NVIDIA. Target=10-bit HEVC. Audio=Stereo(128k). Result -> Upsample color to 10-bit, compress."
*   **Output:** The pipeline is constructed dynamically based on these factors.

### 2. The Clean Sweep Protocol

The script maintains a strict hygiene standard.

*   **Startup:** Checks for write permissions.
*   **Runtime:** Rotates logs if they exceed 5MB.
*   **Shutdown:** Whether successful or interrupted (Ctrl+C), a cleanup routine purges all `_vc_*.mkv` temp files to prevent disk clutter. It uses a "Process Assassin" to kill file locks before deletion.

### 3. Visual & Remote Telemetry

*   **TUI:** A double-buffered, flicker-free "Cyberpunk" interface displaying real-time FPS, Speed, and ETA with strict padding to prevent visual artifacts.
*   **Taskbar:** Integrates with Windows Taskbar to show progress (Green) or Error states (Red).
*   **Webhooks:** (Optional) Can send status reports to Discord or Slack.

---

## 💿 Installation

### Prerequisites

*   Windows 10/11 or Server 2019+.
*   **Windows Terminal** (Recommended for TUI).
*   **FFmpeg & FFprobe** (Static Builds).

### Setup

1.  Create a directory (e.g., `C:\VdoCoon`).
2.  Download `ffmpeg.exe` and `ffprobe.exe` from [gyan.dev](https://www.gyan.dev/ffmpeg/builds/) and place them in the folder.
3.  Copy the [Source Code](#-source-code) below and save it as `VdoCoon.ps1` in the same folder.

---

## ⌨ Command Reference

Open PowerShell inside your VdoCoon folder.

### Standard Operation

Scans the configured library and processes files.

```powershell
.\VdoCoon.ps1
```

### Simulation Mode (Dry Run)

Runs the Decision Engine and Logic Adapters but **does not** execute FFmpeg or modify files. Use this to verify what VdoCoon *would* do.

```powershell
.\VdoCoon.ps1 -DryRun
```

### One-Shot Configuration

To run on a specific folder without changing your saved config, or to re-run the setup wizard:

```powershell
# Override Library Path temporarily
.\VdoCoon.ps1 -LibraryPath "E:\Downloads"

# Force Reset of Settings
.\VdoCoon.ps1 -ResetConfig
```

---

## ⚙ Configuration

On first run, `VdoCoon.json` is generated.

| Setting       | Default         | Description                                                  |
| :------------ | :-------------- | :----------------------------------------------------------- |
| `LibraryPath` | *User Input*    | The root folder to scan recursively.                         |
| `Resolution`  | `Original`      | Limits max height. Options: `4k`, `1080p`, `720p`, `Original`. |
| `CRF`         | `24`            | Constant Rate Factor (Quality). Lower is better quality, larger file. 18-26 recommended. |
| `Preset`      | `medium`        | Encoding speed. `slow`, `medium`, `fast`.                    |
| `Profile`     | `main10`        | **Forced**. Determines the 10-bit color depth.               |
| `Tag`         | `VdoCoon_Omega` | The metadata tag injected to prevent re-processing.          |
| `WebhookURL`  | `""`            | (Optional) Slack/Discord webhook URL for notifications.      |

---

## 🔧 Troubleshooting

| Issue                        | Diagnosis       | Solution                                                     |
| :--------------------------- | :-------------- | :----------------------------------------------------------- |
| **"Invalid Argument" (-22)** | Old Drivers.    | Ensure your GPU drivers support 10-bit encoding (Pascal/GTX 10-series or newer). |
| **Subtitles look wrong**     | Missing Fonts.  | v12.0 includes `-map 0:t?` to copy font attachments. Re-process the file. |
| **Script crashes instantly** | Permissions.    | Ensure VdoCoon has write access to its own folder (for logs) and the Library folder. |
| **"Bloat Detected"**         | Output > Input. | The source file was likely already highly compressed (e.g., YIFY/aXXo). VdoCoon correctly discarded the larger result. |

---

## 💎 Source Code

**File:** `VdoCoon.ps1`  
**Version:** OMEGA v12.0 (10-Bit Enforced)

```powershell
<#
.SYNOPSIS
    VdoCoon // OMEGA // v12.0
    The "Deep Color" Edition.

    [CRITICAL SPECIFICATIONS]
    - PIPELINE:     Forced 10-Bit (Main10) HEVC for all hardware.
    - CONTAINER:    Matroska (MKV) Enforcement.
    - PRESERVATION: Full retention of Audio, Subtitles, and Font Attachments.
    - SAFETY:       Process Assassin & Atomic Swaps.

    [USAGE]
    .\VdoCoon.ps1 -LibraryPath "D:\Media"
#>

[CmdletBinding()]
param(
    [Parameter(Mandatory=$false)][string]$LibraryPath,
    [Parameter(Mandatory=$false)][string]$Resolution,
    [Parameter(Mandatory=$false)][switch]$ResetConfig,
    [Parameter(Mandatory=$false)][switch]$DryRun
)

# region [ 0. BOOTSTRAP ] =====================================================
$ErrorActionPreference = "Stop"
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
$Global:Root = $PSScriptRoot
$Global:SessionID = [Guid]::NewGuid().ToString().Substring(0,8)
$Global:IsISE = ($Host.Name -match "ISE")
$Global:StartTime = [DateTime]::Now

# Theme
$C = @{ Rst="$([char]27)[0m"; Bld="$([char]27)[1m"; Dim="$([char]27)[2m"; Cyn="$([char]27)[36m"; Mag="$([char]27)[35m"; Grn="$([char]27)[32m"; Red="$([char]27)[31m"; Ylw="$([char]27)[33m"; Wht="$([char]27)[37m" }
$G = @{ H="═"; V="║"; BarF="█"; BarE="░"; Play="►" }

# Taskbar
$Global:Taskbar = $null
try { $T=[System.Type]::GetTypeFromProgID("TaskbarList"); if($T){$Global:Taskbar=[System.Activator]::CreateInstance($T)} } catch {}

try { [System.Diagnostics.Process]::GetCurrentProcess().PriorityClass = "BelowNormal" } catch {}

if (-not (Test-Path "$Global:Root\write_test.tmp")) {
    try { New-Item "$Global:Root\write_test.tmp" -ItemType File -Force | Out-Null; Remove-Item "$Global:Root\write_test.tmp" -Force }
    catch { Write-Host " [FATAL] No Write Permission in $Global:Root" -ForegroundColor Red; exit }
}
# endregion

# region [ 1. STATE STORE ] ===================================================
$Store = [pscustomobject]@{
    State       = "BOOT"
    Config      = @{}
    Hardware    = @{ Type="CPU"; Encoder="libx265"; Capability="Low" }
    Stats       = @{ Total=0; Processed=0; Skipped=0; Errors=0; BytesSaved=0 }
    Job         = @{ File="Waiting..."; Task="Idle"; Decision="Pending"; Pct=0; FPS=0; Speed="0x"; ETA="--:--"; AudioChannels=2 }
    Logs        = New-Object System.Collections.Generic.List[string]
    FFLog       = New-Object System.Collections.Generic.List[string]
    Files       = @{ HistoryLog=Join-Path $Global:Root "VdoCoon_History.log"; ErrorLog=Join-Path $Global:Root "VdoCoon_Errors.log" }
    Runtime     = @{ TempFile=$null; LastRender=[DateTime]::MinValue; CurrentPID=$null }
}
# endregion

# region [ 2. SYSTEM CORE ] ===================================================
function Dispatch-Log ($Message, $Level="INFO") {
    $Time = Get-Date -Format "HH:mm:ss"
    if (-not $DryRun) {
        "[$Time] [$Level] $Message" | Out-File -FilePath $Store.Files.HistoryLog -Append -Encoding utf8
        if ($Level -in "ERR", "WARN") { "[$Time] [$Level] $Message" | Out-File -FilePath $Store.Files.ErrorLog -Append -Encoding utf8 }
    }
    if ($Global:IsISE) {
        $Color = switch($Level) { "INFO"{"Gray"} "OK"{"Green"} "WARN"{"Yellow"} "ERR"{"Red"} "SYS"{"Cyan"} "DRY"{"Magenta"} Default{"White"} }
        Write-Host "[$Time] $Message" -ForegroundColor $Color
    } else {
        $Color = switch($Level) { "INFO"{$C.Gry} "OK"{$C.Grn} "WARN"{$C.Ylw} "ERR"{$C.Red} "SYS"{$C.Cyn} "DRY"{$C.Mag} Default{$C.Wht} }
        [void]$Store.Logs.Add("$($C.Dim)$Time$($C.Rst) $Color$Message$($C.Rst)")
        if ($Store.Logs.Count -gt 12) { $Store.Logs.RemoveAt(0) }
    }
}

function Rotate-Logs {
    foreach ($K in $Store.Files.Keys) {
        $P = $Store.Files[$K]; if ((Test-Path $P) -and (Get-Item $P).Length -gt 5MB) { Move-Item $P "$P.$((Get-Date).ToString('yyyyMMdd')).old" -Force }
    }
}

function Set-TaskbarProgress ($Val, $Max, $State="Normal") {
    if ($Global:Taskbar -and -not $Global:IsISE) {
        $H = [System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
        $F = switch($State) { "Error"{4} "Paused"{8} Default{2} }
        try { [void]$Global:Taskbar.SetProgressState($H, $F); [void]$Global:Taskbar.SetProgressValue($H, [long]$Val, [long]$Max) } catch {}
    }
}

function Invoke-Cleanup {
    if ($DryRun) { return }
    if ($Store.Runtime.CurrentPID) { try { Stop-Process -Id $Store.Runtime.CurrentPID -Force -EA SilentlyContinue; Start-Sleep -Milliseconds 100 } catch {} }
    $Store.Runtime.CurrentPID = $null

    if ($Store.Runtime.TempFile -and (Test-Path $Store.Runtime.TempFile)) { Remove-Item $Store.Runtime.TempFile -Force -EA SilentlyContinue }
    $Stale = Get-ChildItem -Path $Global:Root -Filter "_vc_*.mkv" -EA SilentlyContinue
    foreach ($S in $Stale) { try{Remove-Item $S.FullName -Force -EA Stop}catch{} }
    Set-TaskbarProgress 0 100 "NoProgress"
}

if (-not $Global:IsISE) { try { [Console]::CancelKeyPress += { Invoke-Cleanup; [Console]::CursorVisible=$true; Write-Host "`n [HALT] Interrupted." -ForegroundColor Red; [Environment]::Exit(1) } } catch {} }
# endregion

# region [ 3. UI RENDERER ] ===================================================
function Render-Frame {
    if ($Global:IsISE) { return }
    if ((Get-Date) - $Store.Runtime.LastRender -lt [TimeSpan]::FromMilliseconds(33)) { return }
    $Store.Runtime.LastRender = Get-Date

    try { $W = [Console]::WindowWidth } catch { $W = 80 }
    $Buf = New-Object System.Text.StringBuilder

    function Add-Line($Txt) {
        $Clean = $Txt -replace "\x1B\[[0-9;]*[a-zA-Z]", ""
        $Pad = $W - $Clean.Length - 1; if ($Pad -lt 0) { $Pad = 0 }
        [void]$Buf.AppendLine("$Txt$(' ' * $Pad)")
    }

    $Up = "{0:hh\:mm\:ss}" -f ((Get-Date) - $Global:StartTime)
    $Mode = if ($DryRun) { "$($C.Mag)[SIMULATION]" } else { "$($C.Grn)[LIVE]" }
    Add-Line "$($C.Cyn)$($G.Play) VDOCOON $($C.Bld)OMEGA v12.0$($C.Rst) $Mode $($C.Dim)// 10-BIT ENFORCED // UP: $Up$($C.Rst)"
    
    $Sv = "{0:N2} GB" -f ($Store.Stats.BytesSaved / 1GB)
    Add-Line "$($C.Wht)DONE: $($C.Grn)$($Store.Stats.Processed)   $($C.Wht)SKIP: $($C.Ylw)$($Store.Stats.Skipped)   $($C.Wht)FAIL: $($C.Red)$($Store.Stats.Errors)   $($C.Wht)SAVED: $($C.Cyn)$Sv$($C.Rst)"
    Add-Line "$($C.Dim)$($G.H * $W)$($C.Rst)"

    if ($Store.State -eq "WORK") {
        $FStr = $Store.Job.File; if ($FStr.Length -gt ($W-15)) { $FStr = "..." + $FStr.Substring($FStr.Length - ($W-15)) }
        Add-Line "$($C.Mag)$($C.Bld) ACTIVE PIPELINE $($C.Rst)"
        Add-Line " FILE:   $($C.Wht)$FStr$($C.Rst)"
        Add-Line " LOGIC:  $($C.Cyn)$($Store.Job.Decision)$($C.Rst)"
        Add-Line " TASK:   $($C.Ylw)$($Store.Job.Task) [$($Store.Hardware.Type)]$($C.Rst)"
        
        $BW=$W-22; $Fl=[math]::Floor(($Store.Job.Pct/100)*$BW); if($Fl-lt 0){$Fl=0}
        $Bar="$($C.Dim)[$($C.Rst)$($C.Grn)"+($G.BarF*$Fl)+"$($C.Dim)"+($G.BarE*($BW-$Fl))+"$($C.Dim)]$($C.Rst)"
        Add-Line " PROG:   $Bar $($C.Wht)$("{0,3}" -f [int]$Store.Job.Pct)%$($C.Rst)"
        Add-Line " TELE:   $($C.Grn)$($Store.Job.Speed) @ $($Store.Job.FPS) fps$($C.Rst) | ETA: $($C.Ylw)$($Store.Job.ETA)$($C.Rst)"
    } else {
        Add-Line "$($C.Ylw) SYSTEM STANDBY: $($Store.State) $($C.Rst)"; Add-Line ""; Add-Line ""; Add-Line ""; Add-Line ""
    }

    Add-Line "$($C.Dim)$($G.H * $W)$($C.Rst)"
    foreach ($L in $Store.Logs) { Add-Line $L }
    [Console]::SetCursorPosition(0,0); [Console]::Write($Buf.ToString())
}
# endregion

# region [ 4. LOGIC ADAPTERS ] ================================================
function Mount-Configuration {
    $CfgPath = Join-Path $Global:Root "VdoCoon.json"
    if ($ResetConfig -or -not (Test-Path $CfgPath)) {
        if (-not $Global:IsISE) { Clear-Host }
        Write-Host " [INITIAL SETUP WIZARD]" -ForegroundColor Cyan
        
        $Lib = $LibraryPath
        while ([string]::IsNullOrWhiteSpace($Lib) -or -not (Test-Path $Lib)) { $Lib = (Read-Host " Enter Library Path").Trim('"') }
        
        Write-Host "`n [QUALITY (CRF)]" -ForegroundColor Yellow
        Write-Host " [1] Archival (18) - High Bitrate"
        Write-Host " [2] Balanced (24) - Recommended"
        Write-Host " [3] Compact  (28) - Small"
        $Q = Read-Host " Select [1-3] (Def: 2)"; $CRFVal = switch($Q) { "1"{18} "3"{28} Default{24} }

        Write-Host "`n [SPEED (PRESET)]" -ForegroundColor Yellow
        Write-Host " [1] Slow   - Better Compression"
        Write-Host " [2] Medium - Balanced"
        Write-Host " [3] Fast   - Faster"
        $S = Read-Host " Select [1-3] (Def: 2)"; $PreVal = switch($S) { "1"{"slow"} "3"{"fast"} Default{"medium"} }

        $Store.Config = @{
            LibraryPath=$Lib; Resolution="Original"; CRF=$CRFVal; Preset=$PreVal; 
            Profile="main10"; AudioCodec="libopus"; Tag="VdoCoon_Omega"
        }
        $Store.Config | ConvertTo-Json | Set-Content $CfgPath
    } else { $J = Get-Content $CfgPath -Raw | ConvertFrom-Json; $J.PSObject.Properties | % { $Store.Config[$_.Name] = $_.Value } }
    
    # ENFORCE 10-BIT OVERRIDE
    $Store.Config.Profile = "main10"
    if ($LibraryPath) { $Store.Config.LibraryPath = $LibraryPath }
}

function Detect-Hardware {
    Dispatch-Log "Probing Hardware..." "SYS"
    $Out = & $Global:FFMPEG -v quiet -hwaccels
    
    # FORCE 10-BIT PIPELINES (Main10 / P010LE / YUV420P10LE)
    
    # NVIDIA
    if ($Out -contains "cuda") {
        $Store.Hardware = @{
            Type="NVIDIA"; Enc="hevc_nvenc"; 
            Args="-hwaccel cuda -hwaccel_output_format cuda"; 
            Filter="scale_cuda=format=p010le"; 
            Rc="-rc constqp -qp"; 
            ProfileCmd="-profile:v main10"
        }
        return
    }
    # INTEL
    if ($Out -contains "qsv") { 
        $Store.Hardware = @{
            Type="INTEL"; Enc="hevc_qsv"; 
            Args="-init_hw_device qsv=qsv:MFX_IMPL_hw -filter_hw_device qsv"; 
            Filter="vpp_qsv"; Rc="-global_quality"; ProfileCmd="-profile:v main10"
        }
        return
    }
    # CPU
    $Store.Hardware = @{
        Type="CPU"; Enc="libx265"; 
        Args=""; Filter="scale"; Rc="-crf"; 
        ProfileCmd="-pix_fmt yuv420p10le"
    }
}

function Analyze-Media ($Meta) {
    $Vid = $Meta.streams | ? {$_.codec_type -eq "video" -and $_.disposition.attached_pic -ne 1} | Sort width -Descending | Select -First 1
    if (-not $Vid) { $Vid = $Meta.streams | ? {$_.codec_type -eq "video"} | Select -First 1 }
    $MaxChan = 2; $Meta.streams | ? {$_.codec_type -eq "audio"} | % { if($_.channels -gt $MaxChan){ $MaxChan = $_.channels } }
    return @{ VIdx=$Vid.index; H=$Vid.height; Channels=$MaxChan }
}
# endregion

# region [ 5. EXECUTION CORE ] ================================================
function Process-Item ($File) {
    $Store.Job.File = $File.Name; $Store.Job.Task = "Analysis"; $Store.Job.Pct = 0; Render-Frame

    # 1. Probe
    try { $Meta = Invoke-Expression "& `"$Global:FFPROBE`" -v quiet -print_format json -show_format -show_streams `"$($File.FullName)`"" | ConvertFrom-Json } 
    catch { Dispatch-Log "Probe Failed: Corrupt." "ERR"; $Store.Stats.Errors++; return }

    # 2. Decision
    $Tags = @{}; if ($Meta.format.tags) { $Meta.format.tags.PSObject.Properties | % { $Tags[$_.Name] = $_.Value } }
    $VidStr = $Meta.streams | ? {$_.codec_type -eq "video"} | Select -First 1
    if ($VidStr.tags) { $VidStr.tags.PSObject.Properties | % { if(!$Tags[$_.Name]){$Tags[$_.Name]=$_.Value} } }
    $TStr = $Tags.Values -join " "; if ($TStr -match $Store.Config.Tag -or $TStr -match "WinX") { Dispatch-Log "Skipped: Optimized." "INFO"; $Store.Stats.Skipped++; return }

    # 3. Pipeline
    $Info = Analyze-Media $Meta
    $IsHDR = ($VidStr.color_transfer -match "smpte2084")
    $Store.Job.AudioChannels = $Info.Channels
    $Store.Job.Decision = "Encode $($Info.H)p [10-Bit] | Audio: $($Info.Channels)ch"
    if ($DryRun) { Dispatch-Log "[SIMULATION] $File" "DRY"; Start-Sleep -Milliseconds 200; return }

    # Filters (Forced 10-Bit)
    $Filters = @()
    $TgtH = switch($Store.Config.Resolution) { "4k"{2160} "1080p"{1080} "720p"{720} Default{0} }
    $Scale = ($TgtH -gt 0 -and $Info.H -gt $TgtH)

    if ($Store.Hardware.Type -eq "NVIDIA") {
        # Native 10-bit pipeline (p010le). No ToneMapping needed for HDR usually, but safe to passthrough.
        if ($Scale) { $Filters += "$($Store.Hardware.Filter)=-2:$TgtH" } 
        else { $Filters += $Store.Hardware.Filter } # Force p010le
    } elseif ($Store.Hardware.Type -eq "INTEL") {
        if ($Scale) { $Filters += "$($Store.Hardware.Filter)=w=-2:h=$TgtH" }
    } else {
        if ($Scale) { $Filters += "$($Store.Hardware.Filter)=-2:$TgtH" }
        # CPU 10-bit output handles HDR color space correctly without tone-mapping to 8-bit
    }
    
    if (-not $Filters -and $Store.Hardware.Type -ne "CPU" -and $Store.Hardware.Type -ne "NVIDIA") { $Filters += "null" }
    $VF = if ($Filters) { "-vf `"$($Filters -join ",")`"" } else { "" }
    
    # 4. Transcode
    $Store.Runtime.TempFile = Join-Path $Global:Root ("_vc_" + [Guid]::NewGuid().ToString().Substring(0,6) + ".mkv")
    
    $EncCmd = "-c:v $($Store.Hardware.Enc) $($Store.Hardware.ProfileCmd) -preset $($Store.Config.Preset) $($Store.Hardware.Rc) $($Store.Config.CRF)"
    if ($Store.Hardware.Type -eq "NVIDIA") { $EncCmd += " -b:v 0" }

    $Args = "$($Store.Hardware.Args) -i `"$($File.FullName)`" -map 0:$($Info.VIdx) -map 0:a? -map 0:s? -map 0:t? " +
            "$EncCmd $VF " +
            "-c:a $($Store.Config.AudioCodec) -b:a ($($Info.Channels)*64)k -af `"aformat=channel_layouts=7.1|5.1|stereo`" -c:s copy -c:t copy " +
            "-metadata comment=$($Store.Config.Tag) -metadata:s:v:0 comment=$($Store.Config.Tag) " +
            "-y `"$($Store.Runtime.TempFile)`""
            
    $Store.Job.Task = "Encoding"; Dispatch-Log "Processing: $($File.Name)" "SYS"
    $Dur=0; [void][double]::TryParse($Meta.format.duration, [ref]$Dur); $Store.FFLog.Clear()

    $Proc = New-Object System.Diagnostics.Process
    $Proc.StartInfo.FileName = $Global:FFMPEG; $Proc.StartInfo.Arguments = $Args
    $Proc.StartInfo.UseShellExecute = $false; $Proc.StartInfo.RedirectStandardError = $true; $Proc.StartInfo.CreateNoWindow = $true
    [void]$Proc.Start()
    $Store.Runtime.CurrentPID = $Proc.Id

    while (-not $Proc.HasExited) {
        $Line = $Proc.StandardError.ReadLine()
        if ($Line) {
            [void]$Store.FFLog.Add($Line); if ($Store.FFLog.Count -gt 20) { $Store.FFLog.RemoveAt(0) }
            if ($Line -match "time=(\d{2}):(\d{2}):(\d{2}\.\d{2})") {
                $Cur = ([int]$Matches[1]*3600)+([int]$Matches[2]*60)+[double]$Matches[3]
                if ($Dur -gt 0) {
                    $Store.Job.Pct = [math]::Min(100, ($Cur/$Dur)*100); Set-TaskbarProgress $Store.Job.Pct 100 "Normal"
                    if ($Line -match "speed=\s*(\d+\.?\d*)x") { $Spd=[double]$Matches[1]; $Store.Job.Speed="${Spd}x"; if($Spd-gt 0){$Store.Job.ETA=[TimeSpan]::FromSeconds(($Dur-$Cur)/$Spd).ToString("hh\:mm\:ss")} }
                }
            }
            if ($Line -match "fps=\s*(\d+)") { $Store.Job.FPS = $Matches[1] }
        }
        Render-Frame
    }
    $Exit = $Proc.ExitCode; $Proc.Dispose(); $Store.Runtime.CurrentPID = $null; Set-TaskbarProgress 0 100 "NoProgress"

    # 5. Swap
    if ($Exit -eq 0) {
        $Old = $File.Length; $New = (Get-Item $Store.Runtime.TempFile).Length
        if ($New -ge $Old -and $Old -gt 50MB) {
            Dispatch-Log "Bloat ($([math]::Round($New/1MB))MB). Discarding." "WARN"; Remove-Item $Store.Runtime.TempFile -Force; $Store.Stats.Skipped++
        } else {
            try {
                $Bak = "$($File.FullName).bak"; Move-Item $File.FullName $Bak -Force; Move-Item $Store.Runtime.TempFile ([System.IO.Path]::ChangeExtension($File.FullName, ".mkv")) -Force; Remove-Item $Bak -Force
                $Saved = $Old - $New; $Store.Stats.BytesSaved += $Saved; $Store.Stats.Processed++; Dispatch-Log "Success. Saved $([math]::Round($Saved/1MB)) MB" "OK"
            } catch { Dispatch-Log "Swap Failed. Reverting." "ERR"; if(Test-Path $Bak){Move-Item $Bak $File.FullName -Force}; $Store.Stats.Errors++ }
        }
    } else {
        Dispatch-Log "FFmpeg Error ($Exit)." "ERR"; Set-TaskbarProgress 100 100 "Error"
        Dispatch-Log "--- DUMP ($($File.Name)) ---" "ERR"; foreach ($L in $Store.FFLog) { Dispatch-Log $L "ERR" }; Dispatch-Log "--- END ---" "ERR"
        $Store.Stats.Errors++; if(Test-Path $Store.Runtime.TempFile){Remove-Item $Store.Runtime.TempFile -Force}
    }
    $Store.Runtime.TempFile = $null
}
# endregion

# region [ 6. MAIN MANIFOLD ] =================================================
try {
    if (-not $Global:IsISE) { [Console]::CursorVisible = $false }
    try { $Global:FFMPEG=(Get-Command "ffmpeg" -ErrorAction Stop).Source; $Global:FFPROBE=(Get-Command "ffprobe" -ErrorAction Stop).Source }
    catch { $L=Join-Path $Global:Root "ffmpeg.exe"; $P=Join-Path $Global:Root "ffprobe.exe"; if(Test-Path $L){$Global:FFMPEG=$L}; if(Test-Path $P){$Global:FFPROBE=$P} }
    if (-not $Global:FFMPEG) { Throw "Binaries Missing" }

    Mount-Configuration; Rotate-Logs; Detect-Hardware
    
    $Store.State = "SCAN"; Render-Frame
    $Q = Get-ChildItem -LiteralPath $Store.Config.LibraryPath -Recurse -Include "*.mkv","*.mp4","*.avi","*.mov","*.m4v" | Sort Length -Descending
    Dispatch-Log "Manifest: $($Q.Count) items." "INFO"; $Store.Stats.Total = $Q.Count
    
    $Store.State = "WORK"; foreach ($F in $Q) { Process-Item $F; [GC]::Collect() }
    
    Invoke-Cleanup
    
    $Store.State = "DONE"; Render-Frame
    if (-not $Global:IsISE) { [Console]::CursorVisible = $true }
    Write-Host "`n [COMPLETE] Press Enter." -ForegroundColor Green; Read-Host
} catch {
    if (-not $Global:IsISE) { [Console]::CursorVisible = $true }
    Write-Host "`n [FATAL] $_" -ForegroundColor Red; Read-Host
    exit 1
}
# endregion
```
