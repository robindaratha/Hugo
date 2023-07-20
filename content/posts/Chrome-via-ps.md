---
title: "Chrome via Ps"
date: 2023-07-20T19:50:25+02:00
draft: false
tags: 
    - Windows
    - Skripte
    - Powershell
    - Installation
---
Nachfolgend das Skript für die Installation von Chrome über die Powershell ohne zusätzliche Mittel wie Winget:



``` 

$LocalTempDir = $env:TEMP; $ChromeInstaller = "ChromeInstaller.exe"; (new-object System.Net.WebClient).DownloadFile('http://dl.google.com/chrome/install/375.126/chrome_installer.exe', "$LocalTempDir\$ChromeInstaller"); & "$LocalTempDir\$ChromeInstaller" /silent /install; $Process2Monitor = "ChromeInstaller"; Do { $ProcessesFound = Get-Process | ?{$Process2Monitor -contains $_.Name} | Select-Object -ExpandProperty Name; If ($ProcessesFound) { "Still running: $($ProcessesFound -join ', ')" | Write-Host; Start-Sleep -Seconds 2 } else { rm "$LocalTempDir\$ChromeInstaller" -ErrorAction SilentlyContinue -Verbose } } Until (!$ProcessesFound) 

```
 credit to https://www.snel.com/support/install-chrome-in-windows-server/