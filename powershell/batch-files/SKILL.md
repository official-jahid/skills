---
name: windows-batch
description: Windows batch file patterns - scripting, commands, flow control, and automation for cmd.exe environments.
license: MIT
metadata:
  author: powershell-skills
  version: "1.0.0"
---

# Windows Batch Files

Essential patterns for Windows batch file (.bat/.cmd) scripting.

## Basic Structure

```batch
@echo off
setlocal enabledelayedexpansion

REM Set variables
set "APP_NAME=MyApp"
set "VERSION=1.0"

REM Main logic
call :Main
exit /b 0

:Main
echo Starting %APP_NAME% v%VERSION%
goto :eof
```

## Parameters

```batch
@echo off
setlocal

REM %1, %2, %3 are positional parameters
set "INPUT_FILE=%~1"
set "OUTPUT_DIR=%~2"

REM Check if parameter exists
if "%~1"=="" (
    echo Usage: script.bat input_file output_dir
    exit /b 1
)
```

## Loop Patterns

```batch
REM For loop
for %%i in (*.txt) do (
    echo Processing: %%i
    copy "%%i" "processed\%%i"
)

REM For /L loop (numeric)
for /l %%i in (1,1,10) do (
    echo Iteration %%i
)

REM While loop emulation
set "COUNT=0"
:loop
if %COUNT% geq 10 goto :done
echo Count: %COUNT%
set /a COUNT+=1
goto :loop
:done
```

## Conditional Logic

```batch
REM If exists
if exist "file.txt" (
    echo File exists
) else (
    echo File not found
)

REM If errorlevel
your-command
if errorlevel 1 (
    echo Command failed
    exit /b 1
)

REM String comparison
if /i "%VAR%"=="value" (
    echo Match found
)
```

## Functions/Subroutines

```batch
call :LogMessage "Starting process"
call :ProcessFile "input.txt"
goto :eof

:LogMessage
echo [%date% %time%] %~1
goto :eof

:ProcessFile
set "FILE=%~1"
if exist "%FILE%" (
    type "%FILE%"
)
goto :eof
```

## Environment Variables

```batch
REM Set and read
set "VAR=value"
set "VAR"

REM Registry value
for /f "tokens=2*" %%a in ('reg query "HKCU\Software\App" /v "Setting" ^| find "Setting"') do set "VALUE=%%b"

REM Command output to variable
for /f "delims=" %%a in ('dir ^| findstr "File"') do set "FILES=%%a"
```

## Error Handling

```batch
@echo off
your-command || (
    echo Command failed with error
    exit /b 1
)

REM Try multiple methods
if not exist "tool.exe" (
    echo Tool not found, installing...
    call :InstallTool || exit /b 1
)
```