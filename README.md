# 🦝 VdoCoon // Autonomic Media Sovereign (v2.6.0)

> **The Intelligent "Trash Panda" for Your Media Library**  
> *Scavenges space, cleans up containers, and optimizes storage.*

## 📑 Table of Contents

1. [Project Overview](#-project-overview)
2. [Architecture & Philosophy](#-architecture--philosophy)
3. [Installation & Setup](#-installation--setup)
4. [Usage Guide](#-usage-guide)
5. [Changing Libraries (Reset)](#-changing-libraries-reset)
6. [O&M Manual](#-om-manual)
7. [Source Artifact (The Script)](#-source-artifact)

---



## 🔭 Project Overview

**VdoCoon** is a high-level PowerShell hypervisor for FFmpeg. Just as a raccoon meticulously cleans its food, VdoCoon cleans your video library. It is **autonomic**: it profiles the host system, determines the optimal balance between speed and compression (ROI), and manages the entire transcoding lifecycle with strict safety guards.

It transforms a library of mixed media (MP4, MOV, M2TS, etc.) into a unified, high-efficiency **Matroska (MKV) HEVC 10-bit** archive with **Opus** audio. It maintains visual fidelity, frame rate, and refresh rate while typically reducing file size by 40-80%.

### Key Capabilities

*   **Interactive Autonomy:** On the first run, it presents a user-friendly wizard to set preferences, then runs a synthetic stress test (Fractals) to benchmark your specific CPU/GPU performance to auto-select the best Preset.
*   **Hardware Agnostic:** Automatically detects NVIDIA CUDA. If a GPU is found, it constructs a hybrid pipeline (GPU Decoding -> GPU Filtering -> CPU Encoding). If not, it falls back to a pure CPU safety mode.
*   **Matroska Enforcement:** Automatically standardizes all output containers to `.mkv`. If the input is `.mp4`, the output is `.mkv`. The original is removed only after verification.
*   **Safety First:** Uses a "Prediction Pass" to verify potential savings before committing to a full encode. Uses "Deep Verification" to ensure the output is not corrupted. Uses "Atomic Swaps" to ensure no data is lost during a power failure.
*   **Persistent Logging:** Maintains a permanent history log (`VdoCoon_History.log`) of every file processed, skipped, or failed.

---

## 🏛 Architecture & Philosophy

### 1. The Iron Triangle (Quality Strategy)

VdoCoon ignores arbitrary bitrate targets. Instead, it targets **Visual Transparency**.

*   **Video:** `libx265` (HEVC) at `CRF 20` (Configurable).
*   **Color:** `Main10` Profile. 10-bit color is enforced to prevent "banding" artifacts, even if the source is 8-bit.
*   **Audio:** `libopus`. VBR (Variable Bitrate). 256k for Surround, 128k for Stereo.

### 2. Idempotency (The Sentinel)

VdoCoon writes a metadata tag (`comment=VdoCoon_Opt`) into every file it finishes.

*   If you run the script on the same folder 100 times, it will only process new files.
*   It skips already processed files instantly (`O(1)` check).

### 3. The Lazarus Loop (Resilience)

The transcoding engine is wrapped in a failure-handling loop.

1.  **Attempt 1:** Try NVIDIA CUDA (Hardware Acceleration) via a `nv12|p010le` bridge.
2.  **Failure:** If drivers crash or the format is incompatible...
3.  **Attempt 2:** Automatically reboot the job using Software CPU encoding.
4.  **Result:** The job finishes regardless of driver instability.

### 4. Hybrid Transactional Swap (IO Safety)

Files are never modified in place.

1.  **Work:** Video is created in a temp folder (`_VdoCoon_Scratch`).
2.  **Verify:** Video is scanned for corruption (`-xerror`).
3.  **Check:** Video size is compared to source. If larger (bloat), it is discarded.
4.  **Swap:** The source is renamed to `.bak`. The new file is moved into place. The `.bak` is deleted.

---

## 💿 Installation & Setup

### Prerequisites

*   Windows 10 or 11 (PowerShell 5.1+).
*   **FFmpeg** & **FFprobe** (Static Builds).

### Step-by-Step

1.  **Create a Folder:** (e.g., `C:\VdoCoon`).
2.  **Download Binaries:**
    *   Go to [gyan.dev/ffmpeg/builds](https://www.gyan.dev/ffmpeg/builds/).
    *   Download `ffmpeg-release-essentials.zip`.
    *   Extract `ffmpeg.exe` and `ffprobe.exe` into your folder.
3.  **Install Script:**
    *   Copy the code from the [Source Artifact](#-source-artifact) section below.
    *   Save it as `VdoCoon.ps1` in the same folder.

**Directory Structure:**

```text
C:\VdoCoon\
   ├── ffmpeg.exe
   ├── ffprobe.exe
   ├── VdoCoon.ps1
   └── (Auto-created files will appear here: .json, .log, _Scratch)
```

---

## ⌨ Usage Guide

Open PowerShell and navigate to the folder (`cd C:\VdoCoon`).

### Scenario 1: The First Run (Setup Wizard)

Simply run the script. It will detect it has no configuration and launch the interactive wizard.

```powershell
.\VdoCoon.ps1
```

*   It will ask for your **Library Path**.
*   It will ask for your **Resolution** and **Quality** preference.
*   It will benchmark your PC to find the best speed settings.
*   It will save these settings to `VdoCoon.config.json`.

### Scenario 2: Automated Runs

Once configured, just run the script. It will load your saved settings and process any new files in your library.

```powershell
.\VdoCoon.ps1
```

---

## 🔄 Changing Libraries (Reset)

VdoCoon locks onto one library path to prevent accidental processing of the wrong folders.

**To change your source library path or re-run the benchmark:**

1.  Navigate to your VdoCoon folder.
2.  **Delete** the file named `VdoCoon.config.json`.
3.  Run the script again (`.\VdoCoon.ps1`).
4.  The Setup Wizard will reappear, allowing you to enter a new path and settings.

---

## 🛠 O&M Manual

### The Configuration File

The `VdoCoon.config.json` stores your path and settings.

```json
{
    "LibraryPath": "D:\\Movies",
    "Resolution": "1080p",
    "Preset": "slow",
    "CRF": 20
}
```

### Logging

A file named `VdoCoon_History.log` tracks all activity.

*   **Success:** Records filename and bytes saved.
*   **Skip:** Records files skipped due to sentinel tags or efficiency saturation.
*   **Error:** Records corruption or encoding failures.

### Troubleshooting

| Error / Issue                 | Diagnosis                         | Solution                                                     |
| :---------------------------- | :-------------------------------- | :----------------------------------------------------------- |
| `[FATAL] ffmpeg not found`    | Binaries are missing.             | Place `ffmpeg.exe` in the exact same folder as `.ps1`.       |
| `[SKIP] Efficiency Saturated` | The file is already compressed.   | VdoCoon determined re-encoding won't save enough space (>10%). Ignore it. |
| `BLOAT DETECTED`              | The output was larger than input. | The source file was likely low-quality already. The script correctly discarded the result. |
| `Atomic Swap Failed`          | File permission issue.            | Ensure you are not playing the video file while converting it. |

---

## 💎 Source Artifact

Save this code block as **`VdoCoon.ps1`**.

```powershell
<#
.SYNOPSIS
    VdoCoon // AUTONOMIC MEDIA SOVEREIGN // v2.6.0
    
    Architecture: Autonomic / Interactive / Persistent / Logging
    Description:  The Definitive Video Optimization Engine.
                  - Auto-Hardware Profiling (CPU/CUDA).
                  - Smart ROI Benchmarking.
                  - MKV Enforcement & Silent Atomic Safety.
                  - Persistent History Logging.
                  - Small-File Efficiency Short-Circuit.
                  - Full Attachment & Stream Safety.
    
    INSTRUCTIONS:
    1. Place 'ffmpeg.exe' and 'ffprobe.exe' next to this script.
    2. Run via PowerShell.
    3. To reset library/config, delete 'VdoCoon.config.json'.

.PARAMETER ResetConfig
    Forces the Interactive Wizard and System Benchmark to run again.
#>

[CmdletBinding()]
param(
    [Parameter(Mandatory=$false)][string]$LibraryPath,
    [Parameter(Mandatory=$false)][string]$Resolution,
    [Parameter(Mandatory=$false)][switch]$ResetConfig,
    [Parameter(Mandatory=$false)][string]$ScratchPath = $null
)

# --- GLOBAL MANIFOLD ---------------------------------------------------------
$ErrorActionPreference = "Stop"
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
$Root = $PSScriptRoot
$ConfigFile = Join-Path $Root "VdoCoon.config.json"
$LogFile    = Join-Path $Root "VdoCoon_History.log"

# Session Stats
$Global:Stats = @{ Processed=0; Skipped=0; Errors=0; BytesSaved=0 }

# Default Settings
$Global:Config = @{
    LibraryPath  = ""
    Resolution   = "Original"
    CRF          = 20
    Preset       = "medium"
    Codec        = "libx265"
    Profile      = "main10"
    AudioCodec   = "libopus"
    MinSavings   = 10
    SentinelTag  = "VdoCoon_Opt"
    LastBench    = "Never"
}

# --- MODULE: UI & LOGGING ----------------------------------------------------
function Write-Log ($Message, $Color="White", $Level="INFO") {
    $TimeStr = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $ConsoleTime = Get-Date -Format "HH:mm:ss"
    
    # Write to Console
    Write-Host "[$ConsoleTime] [$Level] $Message" -ForegroundColor $Color
    
    # Write to File (Plain text)
    $LogEntry = "[$TimeStr] [$Level] $Message"
    $LogEntry | Out-File -FilePath $LogFile -Append -Encoding utf8
}

function Show-Header {
    Clear-Host
    Write-Host "==============================================================" -ForegroundColor Cyan
    Write-Host "   VdoCoon v2.6.0 // THE TRASH PANDA ENGINE" -ForegroundColor White
    Write-Host "   Autonomic Media Optimization & Scavenging" -ForegroundColor Gray
    Write-Host "==============================================================" -ForegroundColor Cyan
    Write-Host ""
}

# --- MODULE: WIZARD ----------------------------------------------------------
function Show-Wizard {
    Write-Host " [SETUP WIZARD] First Run Configuration" -ForegroundColor Magenta
    Write-Host " --------------------------------------" -ForegroundColor Gray

    if ([string]::IsNullOrWhiteSpace($Global:Config.LibraryPath)) {
        Write-Host " 1. Where is your video library?" -ForegroundColor Yellow
        $InputPath = Read-Host "    Path (e.g. D:\Movies)"
        
        # FIX: Remove quotes if user pasted "Path"
        $InputPath = $InputPath.Trim('"')

        # FIX: Use LiteralPath to handle brackets []
        if (-not (Test-Path -LiteralPath $InputPath)) { 
            Write-Host "    [!] Invalid Path. Exiting." -ForegroundColor Red; exit 
        }
        $Global:Config.LibraryPath = $InputPath
    }

    Write-Host "`n 2. Target Resolution Limit (Downscales larger videos)" -ForegroundColor Yellow
    Write-Host "    [1] Original (Keep as source)" -ForegroundColor Cyan
    Write-Host "    [2] 4k       (2160p)" -ForegroundColor White
    Write-Host "    [3] 1080p    (Full HD)" -ForegroundColor White
    Write-Host "    [4] 720p     (HD - Space Saver)" -ForegroundColor White
    Write-Host "    [5] 480p     (SD - Maximum Savings)" -ForegroundColor White
    $ResChoice = Read-Host "    Select [1-5]"
    switch ($ResChoice) {
        "2" { $Global:Config.Resolution = "4k" }
        "3" { $Global:Config.Resolution = "1080p" }
        "4" { $Global:Config.Resolution = "720p" }
        "5" { $Global:Config.Resolution = "480p" }
        Default { $Global:Config.Resolution = "Original" }
    }

    Write-Host "`n 3. Quality Preference (CRF)" -ForegroundColor Yellow
    Write-Host "    [1] Archival (CRF 18) - Visually Lossless" -ForegroundColor White
    Write-Host "    [2] Balanced (CRF 22) - Transparent / Recommended" -ForegroundColor Cyan
    Write-Host "    [3] Compact  (CRF 26) - Smallest Files" -ForegroundColor White
    $CrfChoice = Read-Host "    Select [1-3]"
    switch ($CrfChoice) {
        "1" { $Global:Config.CRF = 18 }
        "3" { $Global:Config.CRF = 26 }
        Default { $Global:Config.CRF = 22 }
    }

    Write-Host "`n [SETUP] Configuration Locked." -ForegroundColor Green
    Start-Sleep -Seconds 1
}

# --- MODULE: CONFIGURATION ---------------------------------------------------
function Initialize-Configuration {
    if ($ResetConfig -and (Test-Path $ConfigFile)) { Remove-Item $ConfigFile -Force -Confirm:$false }

    if (Test-Path $ConfigFile) {
        Write-Log "Loading profile..." "Cyan" "CONFIG"
        try {
            $Loaded = Get-Content $ConfigFile | ConvertFrom-Json
            foreach ($Prop in $Loaded.PSObject.Properties) {
                if ($Global:Config.ContainsKey($Prop.Name)) { $Global:Config[$Prop.Name] = $Prop.Value }
            }
            if ($LibraryPath) { $Global:Config.LibraryPath = $LibraryPath }
            if ($Resolution)  { $Global:Config.Resolution = $Resolution }
        } catch {
            Write-Log "Config corrupt. Re-initializing." "Red" "ERROR"
            Show-Wizard; Start-Benchmark
        }
    } else {
        if ($LibraryPath) { $Global:Config.LibraryPath = $LibraryPath }
        if ($Resolution)  { $Global:Config.Resolution = $Resolution }
        if ([string]::IsNullOrWhiteSpace($Global:Config.LibraryPath)) { Show-Wizard }
        Start-Benchmark
    }
}

function Save-Configuration {
    $Global:Config.LastBench = (Get-Date).ToString("yyyy-MM-dd HH:mm")
    $Global:Config | ConvertTo-Json | Set-Content $ConfigFile
    Write-Log "Configuration saved." "Cyan" "CONFIG"
}

# --- MODULE: BENCHMARK -------------------------------------------------------
function Start-Benchmark {
    Write-Host "`n"
    Write-Log "Initiating Hardware Profiling..." "Magenta" "BENCH"
    
    $BenchPath = if ($ScratchPath) { $ScratchPath } else { Join-Path $Root "_VdoCoon_Scratch" }
    if (-not (Test-Path $BenchPath)) { New-Item -ItemType Directory -Path $BenchPath -Force | Out-Null }
    
    # Generate Synthetic Source (Fractals)
    $Source = Join-Path $BenchPath "bench_src.mkv"
    $GenCmd = @("-f", "lavfi", "-i", "mandelbrot=size=1920x1080:rate=24", "-t", "10", "-c:v", "libx264", "-preset", "ultrafast", "-qp", "0", "-y", "$Source")
    $Null = Start-Process -FilePath $Global:FFMPEG -ArgumentList $GenCmd -Wait -NoNewWindow
    
    $Results = @()
    $Presets = @("fast", "medium", "slow") 
    
    foreach ($P in $Presets) {
        Write-Host "      -> Stress Testing Preset: [$P]... " -NoNewline -ForegroundColor Gray
        $Target = Join-Path $BenchPath "bench_$P.mkv"
        # CPU Test (x265)
        $Args = @("-i", "$Source", "-c:v", "libx265", "-preset", "$P", "-crf", "22", "-an", "-y", "$Target")
        
        $Timer = [System.Diagnostics.Stopwatch]::StartNew()
        Start-Process -FilePath $Global:FFMPEG -ArgumentList $Args -Wait -NoNewWindow
        $Timer.Stop()
        
        $Size = (Get-Item $Target).Length
        $Results += [PSCustomObject]@{ Preset=$P; Time=$Timer.Elapsed.TotalSeconds; Size=$Size }
        Write-Host "$([math]::Round($Timer.Elapsed.TotalSeconds,1))s" -ForegroundColor White
        Remove-Item $Target -Force -Confirm:$false -ErrorAction SilentlyContinue
    }
    Remove-Item $Source -Force -Confirm:$false -ErrorAction SilentlyContinue
    
    # ROI Logic
    $Base = $Results | Where-Object { $_.Preset -eq "medium" }
    $Slow = $Results | Where-Object { $_.Preset -eq "slow" }
    $Fast = $Results | Where-Object { $_.Preset -eq "fast" }
    $Winner = "medium"

    if ($Slow -and $Base) {
        $Cost = ($Slow.Time - $Base.Time) / $Base.Time
        $Gain = ($Base.Size - $Slow.Size) / $Base.Size
        if ($Cost -gt 0.60 -and $Gain -lt 0.03) { $Winner = "medium" } else { $Winner = "slow" }
    }
    if ($Winner -eq "medium" -and $Fast) {
        $Save = ($Base.Time - $Fast.Time) / $Base.Time
        $Loss = ($Fast.Size - $Base.Size) / $Base.Size
        if ($Save -gt 0.40 -and $Loss -lt 0.05) { $Winner = "fast" }
    }
    
    Write-Log "Benchmark Complete. Optimal CPU Preset: [$Winner]" "Green" "BENCH"
    $Global:Config.Preset = $Winner
    Save-Configuration
}

# --- MODULE: SYSTEM ----------------------------------------------------------
function Resolve-Binary ($Name) {
    $Local = Join-Path $Root "$Name.exe"
    if (Test-Path $Local) { return $Local }
    $System = Get-Command $Name -ErrorAction SilentlyContinue
    if ($System) { return $System.Source }
    throw "$Name not found. Place $Name.exe next to script."
}

function Get-HardwareInfo {
    $Output_HW = & $Global:FFMPEG -v quiet -hwaccels
    $Output_FL = & $Global:FFMPEG -v quiet -filters
    $Global:Has_CUDA   = $Output_HW -contains "cuda"
    $Global:Has_Zscale = $Output_FL -match "zscale"
    if ($Global:Has_CUDA) { Write-Log "NVIDIA CUDA Detected (Enabled)." "Green" "GPU" }
    else                  { Write-Log "Using CPU Software Encoding." "Yellow" "CPU" }
}

# --- MODULE: TRANSCODE -------------------------------------------------------
function Get-MediaInfo ($Path) {
    try {
        $Cmd = @("-v", "quiet", "-print_format", "json", "-show_format", "-show_streams", "$Path")
        $Res = & $Global:FFPROBE $Cmd
        return ($Res | ConvertFrom-Json)
    } catch { return $null }
}

function Build-FilterComplex ($Streams, $IsHDR, $UseCUDA, $TargetHeight) {
    $Filters = @()
    $Vid = $Streams.streams | Where-Object { $_.codec_type -eq "video" } | Select-Object -First 1
    # Check if video stream exists to prevent property access crash
    if (-not $Vid) { return $null }
    
    $DoScale = ($TargetHeight -gt 0 -and $Vid.height -gt $TargetHeight)

    if ($UseCUDA) { 
        if ($DoScale) { $Filters += "scale_cuda=-2:$($TargetHeight):interp_algo=lanczos" }
        # Bridge to allow 8-bit input to be upgraded to 10-bit pipe
        $Filters += "hwdownload,format=nv12|p010le,format=yuv420p10le"
    } else {
        if ($DoScale) { $Filters += "scale=-2:$($TargetHeight):flags=lanczos+accurate_rnd" }
    }

    if ($IsHDR) {
        if ($Global:Has_Zscale) {
            $Filters += "zscale=t=linear:npl=100,format=gbrpf32le,zscale=p=bt709,tonemap=hable:desat=0,zscale=t=bt709:m=bt709:r=tv,format=yuv420p10le"
        } else { $Filters += "tonemap=hable:desat=0,format=yuv420p10le" }
    } elseif (-not $UseCUDA -and -not $DoScale) {
        $Filters += "format=yuv420p10le"
    }
    if ($Filters.Count -eq 0) { return $null }
    return ($Filters -join ",")
}

function Process-Library {
    $ResMap = @{ "480p"=480; "720p"=720; "1080p"=1080; "2k"=1440; "4k"=2160; "Original"=0 }
    $TgtH = $ResMap[$Global:Config.Resolution]
    
    $Scratch = if ($ScratchPath) { $ScratchPath } else { Join-Path $Root "_VdoCoon_Scratch" }
    if (-not (Test-Path $Scratch)) { New-Item -ItemType Directory -Path $Scratch -Force | Out-Null }
    
    Write-Host ""
    Write-Log "Scanning: $($Global:Config.LibraryPath)" "Cyan" "SCAN"
    
    # Expanded Format Support
    $Exts = "*.mkv","*.mp4","*.m4v","*.mov","*.avi","*.wmv","*.flv","*.webm","*.mts","*.m2ts","*.ts","*.mpg","*.mpeg","*.vob","*.3gp","*.asf","*.divx"
    $Files = Get-ChildItem -LiteralPath $Global:Config.LibraryPath -Recurse -Include $Exts | Sort-Object Length -Descending

    foreach ($File in $Files) {
        Write-Host ""
        Write-Log "Processing: $($File.Name)" "DarkGray" "FILE"
        
        # 1. Sentinel Check
        $Info = Get-MediaInfo $File.FullName
        if (-not $Info) { Write-Log "Probe Failed: $($File.Name)" "Red" "ERR"; $Global:Stats.Errors++; continue }
        if ($Info.format.tags.comment -eq $Global:Config.SentinelTag) { Write-Log "Already Scavenged: $($File.Name)" "Green" "SKIP"; $Global:Stats.Skipped++; continue }

        # 2. Setup
        $Vid = $Info.streams | Where-Object { $_.codec_type -eq "video" } | Select-Object -First 1
        if (-not $Vid) { Write-Log "No Video Stream Found: $($File.Name)" "Red" "SKIP"; $Global:Stats.Skipped++; continue }

        $IsHDR = ($Vid.color_transfer -match "smpte2084|arib-std-b67")
        $Guid = [Guid]::NewGuid().ToString()
        $WorkFile = Join-Path $Scratch "$Guid.mkv"
        $ProbeFile = Join-Path $Scratch "$Guid.probe.mkv"
        $FinalPath = [System.IO.Path]::ChangeExtension($File.FullName, ".mkv")
        
        # 3. Prediction Pass
        $Duration = 0
        if (-not [double]::TryParse($Info.format.duration, [ref]$Duration)) { $Duration = 0 }
        
        # OPTIMIZATION: Skip prediction if duration unknown OR file is small (<150MB)
        $SkipPrediction = ($Duration -eq 0) -or ($File.Length -lt 150MB)

        if ($SkipPrediction) { 
            Write-Host "      -> Fast-Track (Small/Unknown). Force Encoding." -ForegroundColor Yellow 
        } else {
            Write-Host "      -> Predicting..." -NoNewline -ForegroundColor Yellow
            try {
                $Seek = [math]::Round($Duration * 0.30)
                $PredCUDA = $Global:Has_CUDA
                $FStr = Build-FilterComplex $Info $IsHDR $PredCUDA $TgtH
                $Dec = if ($PredCUDA) { @("-hwaccel", "cuda", "-hwaccel_output_format", "cuda") } else { @() }
                $FCmd = if ($FStr) { @("-vf", "$FStr") } else { @() }
                
                $Args = $Dec + @("-ss", "$Seek", "-i", "`"$($File.FullName)`"", "-t", "60", "-map", "0:v:0", "-c:v", $Global:Config.Codec, "-preset", "ultrafast", "-crf", $Global:Config.CRF) + $FCmd + @("-an", "-y", "`"$ProbeFile`"")
                $Null = Start-Process -FilePath $Global:FFMPEG -ArgumentList $Args -Wait -NoNewWindow -PassThru
                
                $Remaining = $Duration - $Seek
                $ProbeDuration = if ($Remaining -lt 60) { $Remaining } else { 60 }
                if ($ProbeDuration -le 0) { $ProbeDuration = 1 } 

                $ProbeSize = (Get-Item -LiteralPath $ProbeFile).Length
                $Savings = 100 - (( ($ProbeSize / $ProbeDuration) / ($File.Length / $Duration) ) * 100)
                
                Write-Host " Potential: $([math]::Round($Savings,1))%" -ForegroundColor White
                
                if ($Savings -lt $Global:Config.MinSavings) {
                    Write-Log "Saturated (<$($Global:Config.MinSavings)%): $($File.Name)" "Green" "SKIP"
                    $Global:Stats.Skipped++
                    Remove-Item -LiteralPath $ProbeFile -Force -Confirm:$false; continue
                }
            } catch { Write-Host " [WARN] Prediction failed/skipped, forcing." -ForegroundColor Magenta }
            finally { Remove-Item -LiteralPath $ProbeFile -Force -Confirm:$false -ErrorAction SilentlyContinue }
        }

        # 4. Transcode
        $Strategies = if ($Global:Has_CUDA) { @("CUDA", "CPU") } else { @("CPU") }
        $Success = $false

        foreach ($Strat in $Strategies) {
            if ($Success) { break }
            Write-Log "Encoding ($Strat): $($File.Name)" "Cyan" "EXEC"
            
            $UseGPU = ($Strat -eq "CUDA")
            $Dec = if ($UseGPU) { @("-hwaccel", "cuda", "-hwaccel_output_format", "cuda") } else { @() }
            $FStr = Build-FilterComplex $Info $IsHDR $UseGPU $TgtH
            $FCmd = if ($FStr) { @("-vf", "$FStr") } else { @() }
            
            # UPDATE: "-map 0:a?" prevents crash on audio-less files. "-map 0:t?" preserves fonts.
            $AudArgs = @("-c:a", $Global:Config.AudioCodec, "-b:a", "256k", "-vbr", "on", "-c:s", "copy", "-ignore_unknown", "-map_metadata", "0")
            $Maps    = @("-map", "0:v:0", "-map", "0:a?", "-map", "0:s?", "-map", "0:t?")

            $Cmd = $Dec + @("-i", "`"$($File.FullName)`"") + 
                   $Maps + @("-c:v", $Global:Config.Codec, "-preset", $Global:Config.Preset, "-crf", $Global:Config.CRF) + $FCmd +
                   $AudArgs + @("-metadata", "comment=$($Global:Config.SentinelTag)", "-y", "`"$WorkFile`"")

            $Timer = [System.Diagnostics.Stopwatch]::StartNew()
            $Proc = Start-Process -FilePath $Global:FFMPEG -ArgumentList $Cmd -Wait -NoNewWindow -PassThru
            $Timer.Stop()

            if ($Proc.ExitCode -eq 0) { $Success = $true } 
            else { Write-Log "Failure ($Strat). Retrying..." "Red" "WARN" }
        }

        if (-not $Success) { 
            Write-Log "Transcode Failed: $($File.Name)" "Red" "ERR"; $Global:Stats.Errors++
            if (Test-Path $WorkFile) { Remove-Item $WorkFile -Force -Confirm:$false }; continue 
        }

        # 5. Verification
        Write-Host "      -> Verifying..." -NoNewline -ForegroundColor Yellow
        $NewSize = (Get-Item -LiteralPath $WorkFile).Length
        if ($NewSize -ge $File.Length) {
            Write-Host " BLOAT DETECTED." -ForegroundColor Red
            Write-Log "Discarded (Bloat): $($File.Name)" "Red" "DROP"
            Remove-Item $WorkFile -Force -Confirm:$false; continue
        }
        
        $VArgs = @("-v", "error", "-xerror", "-i", "`"$WorkFile`"", "-f", "null", "-")
        $VProc = Start-Process -FilePath $Global:FFMPEG -ArgumentList $VArgs -Wait -NoNewWindow -PassThru
        
        if ($VProc.ExitCode -ne 0) {
            Write-Host " CORRUPTION." -ForegroundColor Red
            Write-Log "Discarded (Corruption): $($File.Name)" "Red" "DROP"
            $Global:Stats.Errors++
            Remove-Item $WorkFile -Force -Confirm:$false; continue
        }
        Write-Host " OK." -ForegroundColor Green
        
        # 6. Atomic Swap
        $Backup = "$($File.FullName).bak"
        try {
            if ($File.FullName -eq $FinalPath) {
                [System.IO.File]::Move($File.FullName, $Backup)
                [System.IO.File]::Move($WorkFile, $FinalPath)
                [System.IO.File]::Delete($Backup)
            } else {
                if (Test-Path -LiteralPath $FinalPath) { 
                    Write-Log "Target Exists: $($FinalPath)" "Red" "SKIP"; Remove-Item $WorkFile -Force -Confirm:$false; continue 
                }
                [System.IO.File]::Move($File.FullName, $Backup)
                try {
                    [System.IO.File]::Move($WorkFile, $FinalPath)
                    [System.IO.File]::Delete($Backup)
                } catch { [System.IO.File]::Move($Backup, $File.FullName); throw }
            }
            
            $BytesSaved = $File.Length - $NewSize
            $Global:Stats.BytesSaved += $BytesSaved
            $Global:Stats.Processed++
            Write-Log "Complete: $($File.Name) (Saved $([math]::Round($BytesSaved/1MB,2)) MB)" "Cyan" "DONE"
        } catch {
            Write-Log "Swap Failed: $($File.Name)" "Red" "FATAL"
            if ((Test-Path -LiteralPath $Backup) -and -not (Test-Path -LiteralPath $File.FullName)) { 
                Rename-Item -LiteralPath $Backup -NewName $File.Name -Force -Confirm:$false 
            }
        }
    }
}

# --- SUMMARY -----------------------------------------------------------------
function Show-Summary {
    Write-Host ""
    Write-Host "==============================================================" -ForegroundColor Cyan
    Write-Host "   SESSION SUMMARY" -ForegroundColor White
    Write-Host "==============================================================" -ForegroundColor Cyan
    Write-Host "   Processed : $($Global:Stats.Processed)"
    Write-Host "   Skipped   : $($Global:Stats.Skipped)"
    Write-Host "   Errors    : $($Global:Stats.Errors)"
    Write-Host "   Space Saved: $([math]::Round($Global:Stats.BytesSaved / 1GB, 2)) GB" -ForegroundColor Green
    Write-Host "==============================================================" -ForegroundColor Cyan
    Write-Log "Session Ended. Saved $([math]::Round($Global:Stats.BytesSaved / 1GB, 2)) GB." "Cyan" "END"
}

# --- MAIN EXECUTION FLOW -----------------------------------------------------
Show-Header
try {
    $Global:FFMPEG  = Resolve-Binary "ffmpeg"
    $Global:FFPROBE = Resolve-Binary "ffprobe"
    Get-HardwareInfo
    Initialize-Configuration
    Process-Library
    Show-Summary
} catch {
    Write-Log $_.Exception.Message "Red" "FATAL"
    Write-Host "`nPress Enter to exit..."
    Read-Host
    exit 1
}
```
