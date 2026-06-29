---
name: powershell-wrapper
description: PowerShell wrapper patterns - creating wrappers for executables, COM objects, .NET assemblies, and external tools.
license: MIT
metadata:
  author: powershell-skills
  version: "1.0.0"
---

# PowerShell Wrapper

Patterns for wrapping executables and APIs in PowerShell.

## Executable Wrapper

```powershell
function Invoke-MyApp {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$true)]
        [string[]]$Arguments,
        
        [string]$WorkingDirectory = $PWD
    )
    
    process {
        $psi = New-Object System.Diagnostics.ProcessStartInfo
        $psi.FileName = "myapp.exe"
        $psi.Arguments = $Arguments -join " "
        $psi.WorkingDirectory = $WorkingDirectory
        $psi.RedirectStandardOutput = $true
        $psi.RedirectStandardError = $true
        $psi.UseShellExecute = $false
        
        $process = [System.Diagnostics.Process]::Start($psi)
        $output = $process.StandardOutput.ReadToEnd()
        $process.WaitForExit()
        
        return $output
    }
}
```

## COM Object Wrapper

```powershell
function Get-WMIInfo {
    [CmdletBinding()]
    param([string]$ComputerName = $env:COMPUTERNAME)
    
    $wmi = Get-WmiObject -Class Win32_OperatingSystem -ComputerName $ComputerName
    return [PSCustomObject]@{
        OS = $wmi.Caption
        Version = $wmi.Version
        FreeMem = $wmi.FreePhysicalMemory
    }
}
```

## .NET Assembly Wrapper

```powershell
function New-HttpClientWrapper {
    [CmdletBinding()]
    param([string]$BaseUri)
    
    $client = New-Object System.Net.Http.HttpClient
    $client.BaseAddress = [System.Uri]$BaseUri
    
    return [PSCustomObject]@{
        Client = $client
        Get = { param($Path) $client.GetAsync($Path) }
        Post = { param($Path, $Content) $client.PostAsync($Path, $Content) }
    }
}
```

## Wrapper with Credentials

```powershell
function Invoke-RemoteCommand {
    [CmdletBinding()]
    param(
        [PSCredential]$Credential,
        [string]$ComputerName,
        [string]$ScriptBlock
    )
    
    $session = New-PSSession -ComputerName $ComputerName -Credential $Credential
    try {
        Invoke-Command -Session $session -ScriptBlock ([scriptblock]::Create($ScriptBlock))
    }
    finally {
        Remove-PSSession $session
    }
}
```

## Batch File Wrapper

```powershell
function Invoke-BatchFile {
    param([string]$Path, [string[]]$Args)
    
    $psi = New-Object System.Diagnostics.ProcessStartInfo
    $psi.FileName = "cmd.exe"
    $psi.Arguments = "/c `"$Path`""
    if ($Args) { $psi.Arguments += " " + ($Args -join " ") }
    $psi.RedirectStandardOutput = $true
    $psi.UseShellExecute = $false
    
    $result = [System.Diagnostics.Process]::Start($psi)
    return $result.StandardOutput.ReadToEnd()
}
```