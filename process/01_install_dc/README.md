# 01 Initial Setup

## 0. (Optional) Enable PSRemoting on your client
From Administrator Powershell on your Win11 client:

```powershell
# Start the WinRM service
Start-Service WinRM

# Test the service
ls wsman:\localhost\

# Add your Domain Controller IP address to the TrustedHosts list
Set-Item wsman:\localhost\Client\TrustedHosts -value <your_domain_controller_ip_address>

# Start a new Powershell session
New-PSSession -ComputerName <your_domain_controller_ip_address> -Credential (Get-Credential)

# Enter the session, session it should be generated after you run the New-PSSession command
Enter-PSSession <session_id>
```

## 1. Setting up Domain Controller using SConfig:
On your Domain Controller:
```powershell
# Start SConfig
SConfig

# Set Computer name with option 2, set to DC1
DC1

# Change Network settings with option 8
# Select Network Adapter 1 (there should only be one for now)
# Choose set network adapter address with option 1

(D)HCP or (S)tatic: S
Static IP address: <new_ip_address>
Subnet mask:
Default Gateway: <XXX.XXX.XXX.2>

# Head back to the client and update trusted hosts
Set-Item wsman:\localhost\Client\TrustedHosts -value <new_ip_address>
Enter-PSSession <new_ip_address> -Credential (Get-Credential)

# Verify your settings by running ipconfig /all
```

## 2. Install and configure Active Directory Service
On Domain Controller:
```powershell
# You need to set -IncludeManagementTools when running on cmdlet
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools

# Import ADDSDeployment to use ADDSForest
Import-Module ADDSDeployment
Install-ADDSForest
Domain Name: XXX.com
SafeModeAdministratorPassword: <your_password>
Confirm SafeModeAdministratorPassword: <yourpassword>
# Select Y when ask to restart

```

## 3. Set DNS server address
After restart, you will need to reconfigure DNS server. On your Domain Controller:
```powershell
Get-DNSClientServerAddress
# This will show you the InterfaceIndex and the ServerAddress

Set-DNSClientServerAddress -InterfaceIndex <your_index> -ServerAddresses <your_DC_IP>

# Run Get-DNSClientServerAddress to verify your changes
```

## 4. Add Clients to Domain Controller AD
Ensure that your domain controller is still running, on your client device:

1. In the search bar, look for 'Access work or school'.
2. Click on 'Connect'
3. Click on 'Join this device to a local Active Directory domain'
4. Enter the domain name 'XXX.com'

Alternatively, you can join a domain using Powershell
```powershell
Add-Computer -DomainName XXX.com -Credential XXX\Administrator -Force -Restart
```

After the restart, you can see the option to sign in with 'Other user'. It should say 'Sign in to: XXX'
