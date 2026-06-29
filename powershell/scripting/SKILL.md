---
name: powershell-scripting
description: PowerShell scripting patterns - cmdlets, modules, error handling, and automation scripts for Windows administration.
license: MIT
metadata:
  author: powershell-skills
  version: "1.0.0"
---

# PowerShell Scripting

Essential patterns for PowerShell script development.

## Basic Script Structure

```powershell
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string]$Path,
    
    [Parameter()]
    [int]$Timeout = 30
)

begin {
    # Initialize - runs once before pipeline
    Write-Verbose "Starting script..."
}

process {
    # Main logic - runs for each pipeline item
    try {
        Get-ChildItem -Path $Path -ErrorAction Stop
    }
    catch {
        Write-Error "Failed to access path: $_"
        throw
    }
}

end {
    # Cleanup - runs once after pipeline
}
```

## Error Handling

```powershell
try {
    $result = Get-Content "file.txt" -ErrorAction Stop
}
catch [System.IO.FileNotFoundException] {
    Write-Warning "File not found"
}
catch {
    Write-Error "Unexpected error: $($_.Exception.Message)"
}
finally {
    # Cleanup
}
```

## Common Patterns

### Process Management
```powershell
$process = Start-Process -FilePath "notepad.exe" -PassThru
$process.WaitForExit()
$process.Kill()
```

### Registry Operations
```powershell
Set-ItemProperty -Path "HKCU:\Software\App" -Name "Setting" -Value "enabled"
Get-ItemProperty -Path "HKCU:\Software\App" -Name "Setting"
```

### Service Management
```powershell
Start-Service -Name "Spooler"
Stop-Service -Name "Spooler"
Restart-Service -Name "Spooler"
```

## Module Structure

```powershell
function Get-MyData {
    [CmdletBinding()]
    param([string]$Name)
    
    process {
        return @{ Name = $Name; Value = "Data" }
    }
}

Export-ModuleMember -Function Get-MyData
```

## Best Practices

- Use `[CmdletBinding()]` for advanced functions
- Support `-WhatIf` and `-Confirm` with `ShouldProcess()`
- Use `Write-Verbose`, `Write-Debug` for diagnostics
- Handle pipeline input properly
- Return objects, not strings