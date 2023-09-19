# 01 Installing Workstation

1. Use `SConfig` to:
    - Change hostname
    - Change IP address to static
    - Change DNS server to own IP address

2. Install AD Windows Feature

```shell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```