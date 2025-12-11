# Component Manager

Manage components (`packages`, `optional features`, `capabilities`) easily within a single tool. It currently gets the lists of all components using three background jobs, which means that the lists won't instantly appear on startup. I used `Start-Job`, since the GUI would otherwise take ~`30` seconds to load.
> https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/start-job?view=powershell-7.5

Preview:

https://github.com/user-attachments/assets/39e91700-2ebc-4d35-a9ea-07910774cbc7

Default `24H2 (Build 26100.1742)` components:
> [AppX.txt](https://github.com/nohuto/CompMan/blob/main/AppX.txt)  
> [Optional.txt](https://github.com/nohuto/CompMan/blob/main/Optional.txt)  
> [Capabilities.txt](https://github.com/nohuto/CompMan/blob/main/Capabilities.txt)  

## __AppX Packages Tab:__
Get all of your current packages with:
```ps
Get-AppxPackage
```
If you're on any LTSC version (no preinstalled UWP apps) then there's probably not much to remove, since most of the leftovers are `Non Removeable`. If you're on a non LTSC version, remove whatever you want, e.g.:
```ps
$apps = @(
    "Microsoft.BingNews",
    "Microsoft.BingWeather",
    "Microsoft.GetHelp",
    "Microsoft.MicrosoftOfficeHub",
    "Microsoft.MicrosoftStickyNotes",
    "Microsoft.Todos",
    "Microsoft.WindowsAlarms",
    "Microsoft.WindowsCamera",
    "Microsoft.WindowsFeedbackHub",
    "Microsoft.WindowsSoundRecorder",
    "Microsoft.YourPhone",
    "Microsoft.ZuneMusic",
    "MSTeams"
)

$apps | % {Get-AppxPackage -AllUsers -Name $_ | Remove-AppxPackage}
```
> https://learn.microsoft.com/en-us/powershell/module/appx/get-appxpackage?view=windowsserver2025-ps  
> https://learn.microsoft.com/en-us/powershell/module/appx/add-appxpackage?view=windowsserver2025-ps  
> https://learn.microsoft.com/en-us/powershell/module/appx/remove-appxpackage?view=windowsserver2025-ps  


## __Optional Features Tab:__
Get all information about the optional features with
```ps
Get-WindowsOptionalFeature -Online -FeatureName * | Format-List *
```
Disable whatever you don't need, like all `Printing*` features, if you don't use your PC for printing. Other examples:
```ps
$defeatures = @(
    # Printing
    'Printing-Foundation-InternetPrinting-Client',
    'Printing-Foundation-LPDPrintService',
    'Printing-Foundation-LPRPortMonitor',
    'Printing-PrintToPDFServices-Features',
    'Printing-XPSServices-Features',
    'Printing-Foundation-Features',
    'WorkFolders-Client',

    # Other
    'Recall',
    'SmbDirect',
    'MSRDC-Infrastructure',
    'Windows-Defender-Default-Definitions',
    'Microsoft-RemoteDesktopConnection',
    'Microsoft-Hyper-V-Hypervisor',

    # PowerShell 2.0 (deprecated)
    'MicrosoftWindowsPowerShellV2',
    'MicrosoftWindowsPowerShellV2Root'
)

$enfeatures= @(
    'LegacyComponents',
    'DirectPlay'
)

$defeatures | % {Disable-WindowsOptionalFeature -FeatureName $_ -Online -NoRestart}
$enfeatures| % {Enable-WindowsOptionalFeature -FeatureName $_ -Online -NoRestart}
```
> https://devblogs.microsoft.com/powershell/windows-powershell-2-0-deprecation/  
> https://learn.microsoft.com/en-us/powershell/module/dism/get-windowsoptionalfeature?view=windowsserver2025-ps  
> https://learn.microsoft.com/en-us/powershell/module/dism/enable-windowsoptionalfeature?view=windowsserver2025-ps  
> https://learn.microsoft.com/en-us/powershell/module/dism/disable-windowsoptionalfeature?view=windowsserver2025-ps  


## __Capabilities Tab:__
Get all information about the capabilities with:
```ps
Get-WindowsCapability -Online -Name * | Format-List *
```
Removal depends on what capabilities you use on your system. Some features can be safely removed on any system, like `PowerShell ISE`, `StepsRecorder`. You can also use it to add/remove language packages. Examples:

```ps
$capabilities = @(
    "Microsoft.Windows.PowerShell.ISE*",
    "MathRecognizer*",
    "App.StepsRecorder*",
    #"*SnippingTool*", # If using ShareX/Lightshot etc.
    "*MSPaint*",
    #"*Notepad*", # If using Notepad++
    "*Wallpapers.Extended*",
    "*Hello.Face*"
    #"Microsoft.Windows.Wifi*" # Remove built‑in WiFi support
)

$capabilities | % {Get-WindowsCapability -Online -Name $_ | Remove-WindowsCapability -Online}
Add-WindowsCapability -Online -Name 'WMIC~~~~'
```
> https://learn.microsoft.com/en-us/powershell/module/dism/get-windowscapability?view=windowsserver2025-ps  
> https://learn.microsoft.com/en-us/powershell/module/dism/add-windowscapability?view=windowsserver2025-ps  
> https://learn.microsoft.com/en-us/powershell/module/dism/remove-windowscapability?view=windowsserver2025-ps  
