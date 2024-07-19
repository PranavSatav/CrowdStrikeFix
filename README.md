# CrowdStrikeFix
Workaround for BSOD caused by CrowdStrike
Automated Workaround in Safe Mode using Group Policy
You can set up a GPO to run a script during Safe Mode. Here’s how you can do this:

Create the PowerShell Script

Create a PowerShell script that deletes the problematic CrowdStrike driver file causing BSODs and handles the Safe Mode boot and revert:

# CrowdStrikeFix.ps1
# This script deletes the problematic CrowdStrike driver file causing BSODs and reverts Safe Mode

$filePath = "C:\Windows\System32\drivers\C-00000291*.sys"
$files = Get-ChildItem -Path $filePath -ErrorAction SilentlyContinue

foreach ($file in $files) {
    try {
        Remove-Item -Path $file.FullName -Force
        Write-Output "Deleted: $($file.FullName)"
    } catch {
        Write-Output "Failed to delete: $($file.FullName)"
    }
}

# Revert Safe Mode Boot after Fix
bcdedit /deletevalue {current} safeboot
Create a GPO for Safe Mode

Open the Group Policy Management Console (GPMC).
Right-click on the appropriate Organizational Unit (OU) and select Create a GPO in this domain, and Link it here....
Name the GPO, for example, CrowdStrike Fix Safe Mode.
Edit the GPO

Right-click the new GPO and select Edit.
Navigate to Computer Configuration -> Policies -> Windows Settings -> Scripts (Startup/Shutdown).
Double-click Startup, then click Add.
In the Script Name field, browse to the location where you saved CrowdStrikeFix.ps1 and select it.
Click OK to close all dialog boxes.
Force Safe Mode Boot Using a Script

Create another PowerShell script to force Safe Mode boot and link it to a GPO for immediate application:

# ForceSafeMode.ps1
# This script forces the computer to boot into Safe Mode

bcdedit /set {current} safeboot minimal
Restart-Computer
Create a GPO to Apply the Safe Mode Script

Open the Group Policy Management Console (GPMC).
Right-click on the appropriate Organizational Unit (OU) and select Create a GPO in this domain, and Link it here....
Name the GPO, for example, Force Safe Mode.
Right-click the new GPO and select Edit.
Navigate to Computer Configuration -> Policies -> Windows Settings -> Scripts (Startup/Shutdown).
Double-click Startup, then click Add.
In the Script Name field, browse to the location where you saved ForceSafeMode.ps1 and select it.
Click OK to close all dialog boxes.
Apply the GPOs

Make sure the Force Safe Mode GPO is applied to the affected computers first.
The computer will boot into Safe Mode and execute the CrowdStrikeFix.ps1 script.
Once the issue is fixed, the script will revert the boot settings to normal mode.
