---
name: automation
description: Automation patterns - file operations, registry changes, scheduled tasks, and system monitoring.
license: MIT
metadata:
  author: powershell-skills
  version: "1.0.0"
---

# Automation (PowerShell)

Windows automation patterns for system administration.

## File Operations

```powershell
# Bulk file operations
Get-ChildItem -Path "C:\Logs" -Filter "*.log" | 
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } |
    Remove-Item

# Copy with conditions
Copy-Item -Path $source -Destination $dest -Force

# Monitor file changes
Register-ObjectEvent -InputObject (Get-Item "file.txt") -EventName Changed -Action {
    Write-Host "File changed!"
}
```

## Registry Automation

```powershell
# Bulk registry changes
$registryChanges = @{
    "HKLM:\SOFTWARE\App" = @{
        "Setting1" = "value1"
        "Setting2" = 42
    }
}

foreach ($key in $registryChanges.Keys) {
    if (!(Test-Path $key)) {
        New-Item -Path $key -Force
    }
    foreach ($value in $registryChanges[$key].Keys) {
        Set-ItemProperty -Path $key -Name $value -Value $registryChanges[$key][$value]
    }
}
```

## Scheduled Tasks

```powershell
# Create scheduled task
$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-File C:\scripts\backup.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At "2:00AM"
$settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries

Register-ScheduledTask -TaskName "DailyBackup" -Action $action -Trigger $trigger -Settings $settings
```

## Service Management

```powershell
# Bulk service control
$services = @("Spooler", "BITS", "WSearch")
foreach ($service in $services) {
    if (Get-Service -Name $service -ErrorAction SilentlyContinue) {
        Set-Service -Name $service -StartupType Automatic
        Start-Service -Name $service
    }
}
```

## System Monitoring

```powershell
# Monitor CPU and memory
function Get-SystemHealth {
    $cpu = Get-Counter '\Processor(_Total)\% Processor Time'
    $mem = Get-Counter '\Memory\Available MBytes'
    
    return [PSCustomObject]@{
        CPU = $cpu.CounterSamples.CookedValue
        AvailableMemory = $mem.CounterSamples.CookedValue
    }
}

# Continuous monitoring
while ($true) {
    $health = Get-SystemHealth
    if ($health.CPU -gt 80) {
        Write-Warning "High CPU: $($health.CPU)%"
    }
    Start-Sleep 60
}
```

## Event Log Management

```powershell
# Clean event logs
Get-EventLog -List | ForEach-Object {
    Clear-EventLog -LogName $_.Log
}

# Query specific events
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4624, 4625
    StartTime = (Get-Date).AddDays(-1)
} | Select-Object TimeCreated, Id, Message
```

## Backup Automation

```powershell
# Automated backup with rotation
$backupRoot = "D:\Backups"
$date = Get-Date -Format "yyyyMMdd"
$dest = Join-Path $backupRoot $date

robocopy "C:\Data" $dest /MIR /Z /R:3 /W:10

# Remove old backups
Get-ChildItem $backupRoot | 
    Where-Object { $_.CreationTime -lt (Get-Date).AddDays(-30) } |
    Remove-Item -Recurse -Force
```