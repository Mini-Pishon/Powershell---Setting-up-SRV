# Server Setup and Configuration Script

This PowerShell script automates the setup and configuration of a Windows server. It handles tasks like renaming the server, configuring network settings, enabling Remote Desktop, installing features, and setting up an Active Directory Forest.

## Features

### 1. Rename Server
Prompts the user to rename the server and applies the change.
```powershell
$ComputerName = Read-Host "Enter the computer's new name"
Rename-Computer -NewName $ComputerName -Force
Write-Output "Server renamed to $ComputerName."
```

### 2. Configure IP Address
Prompts the user for IP configuration details and applies them to the first network adapter.
```powershell
$IPAddress = Read-Host "Enter the IP Address (e.g., 192.168.37.240)"
$PrefixLength = Read-Host "Enter the Subnet Prefix Length (e.g., 24)"
$DefaultGateway = Read-Host "Enter the Default Gateway (e.g., 192.168.37.254)"
$InterfaceIndex = (Get-NetAdapter | Select-Object -First 1).ifIndex
New-NetIPAddress -IPAddress $IPAddress -PrefixLength $PrefixLength -InterfaceIndex $InterfaceIndex -DefaultGateway $DefaultGateway
Write-Output "IP Address set to $IPAddress with Gateway $DefaultGateway."
```

### 3. Configure DNS
Allows the user to set a DNS server for the first detected network adapter.
```powershell
$DnsServer = Read-Host "Enter the DNS server (e.g., 8.8.8.8)"
$InterfaceAlias = (Get-NetAdapter | Select-Object -First 1).Name
Set-DnsClientServerAddress -InterfaceAlias $InterfaceAlias -ServerAddresses @($DnsServer)
Write-Output "DNS Server set to $DnsServer."
```

### 4. Rename Network Adapter
Renames the first detected network adapter to "LAN."
```powershell
$AdapterName = (Get-NetAdapter | Select-Object -First 1).Name
Rename-NetAdapter -Name $AdapterName -NewName "LAN"
Write-Output "Network adapter renamed to 'LAN'."
```

### 5. Enable Remote Desktop
Enables Remote Desktop and allows it through the Windows Firewall.
```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
Write-Output "Remote Desktop enabled and firewall rule applied."
```

### 6. Prompt for Restart
Asks the user whether to restart the server to apply changes.
```powershell
$RestartConfirmation = Read-Host "Do you want to restart the computer to apply the changes? (Answer Y or N)"
if ($RestartConfirmation -eq "Y") {
    Restart-Computer -Force
}
```

### 7. Install ADDS, DNS, and RSAT Tools
Installs Active Directory Domain Services, DNS, and RSAT tools if they are not already installed.
```powershell
$FeatureList = @("RSAT-AD-Tools", "AD-Domain-Services", "DNS")
foreach ($Feature in $FeatureList) {
    Install-WindowsFeature -Name $Feature -IncludeManagementTools -IncludeAllSubFeature
    Write-Output "Feature $Feature installed successfully."
}
```

### 8. Configure ADDS Forest
Prompts for domain information and configures the Active Directory Forest.
```powershell
$DomainNameDNS = Read-Host "Enter the full domain name (e.g., domain.com)"
$DomainNameNetbios = Read-Host "Enter the NetBIOS domain name (e.g., DOMAIN)"
$ForestConfiguration = @{
    DomainName = $DomainNameDNS
    NetBIOSDomainName = $DomainNameNetbios
}
Install-ADDSForest @ForestConfiguration
Write-Output "ADDS Forest configuration completed successfully."
```

## How to Use

1. **Clone or Download**: Clone the repository or download the script file.
2. **Run as Administrator**: Open PowerShell with Administrator privileges.
3. **Set Execution Policy**: Allow script execution if required:
    ```powershell
    Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
    ```
4. **Run the Script**: Execute the script:
    ```powershell
    .\ServerSetup.ps1
    ```

## Prerequisites

- Windows Server environment.
- PowerShell 5.0 or later.
- Administrator privileges.

## Notes

- Ensure proper network configurations before running the script.
- Some tasks require a system restart to take effect.
- Verify DNS and domain settings before configuring the ADDS forest.

## Contributions

Contributions are welcome! Please submit a pull request or open an issue to suggest improvements.
