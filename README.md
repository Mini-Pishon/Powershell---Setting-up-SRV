# Server Configuration Script

This PowerShell script performs essential server setup tasks, including renaming the server, configuring IP settings, setting up DNS, renaming the network adapter, enabling Remote Desktop, and restarting the server if necessary.

## Features

### 1. Rename the Server
Prompts the user to enter a new computer name and applies the change.

```powershell
$ComputerName = Read-Host "Enter Computer's name"
Rename-Computer -NewName $ComputerName -Force
Write-Output "Name changed to $ComputerName"
```

### 2. Configure IP Settings
Prompts the user for the IP address, subnet prefix length, and default gateway, then applies the configuration.

```powershell
$IPAddress = Read-Host "Enter the IP Address (e.g., 192.168.37.240)"
$PrefixLength = Read-Host "Enter the Subnet Prefix Length (e.g., 24)"
$DefaultGateway = Read-Host "Enter the Default Gateway (e.g., 192.168.37.254)"
$InterfaceIndex = (Get-NetAdapter | Select-Object -First 1).ifIndex
Write-Output "Changing IP address..."
New-NetIPAddress -IPAddress $IPAddress -PrefixLength $PrefixLength -InterfaceIndex $InterfaceIndex -DefaultGateway $DefaultGateway
Write-Output "IP Address successfully changed to $IPAddress with Gateway $DefaultGateway"
```

### 3. Configure DNS
Prompts the user for the DNS server address and applies it to the active network adapter.

```powershell
$InterfaceAlias = (Get-NetAdapter | Select-Object -First 1).Name
$DnsServer = Read-Host "Enter DNS server (e.g., 8.8.8.8)"
Set-DnsClientServerAddress -InterfaceAlias $InterfaceAlias -ServerAddresses @($DnsServer)
Write-Output "DNS Changed"
```

### 4. Rename Network Adapter
Renames the first detected network adapter to "LAN."

```powershell
$AdapterName = (Get-NetAdapter | Select-Object -First 1).Name
Rename-NetAdapter -Name $AdapterName -NewName "LAN"
Write-Output "Renaming Network Adapter"
```

### 5. Enable Remote Desktop
Enables Remote Desktop and configures the firewall to allow RDP connections.

```powershell
Write-Output "Enabling Remote Desktop..."
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
Write-Output "Remote Desktop enabled and firewall rule applied."
```

### 6. Restart Option
Prompts the user to restart the computer to apply changes.

```powershell
$RestartConfirmation = Read-Host "Do you want to restart the computer to apply the changes? (Answer Y or N)"
if ($RestartConfirmation.ToUpper() -eq 'Y') {
    Write-Output "Computer will restart"
    Restart-Computer -Force
} else {
    Write-Output "Changes applied. Please restart the computer manually"
}
```

## How to Use

1. Save the script as a `.ps1` file (e.g., `ServerSetup.ps1`).
2. Open PowerShell as an Administrator.
3. Set the execution policy to allow script execution:
   ```powershell
   Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
   ```
4. Run the script:
   ```powershell
   .\ServerSetup.ps1
   ```

## Prerequisites

- PowerShell 5.0 or later.
- Administrator privileges.

---

Feel free to customize the script or README as needed for your environment. If you encounter issues, please raise them in the repository's Issues section.
