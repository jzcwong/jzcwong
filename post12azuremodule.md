# General notes on Azure

Using PowerShell and Azure Module

1. Install Azure Module for PowerShell

```PowerShell
Install-Module -Name Az -AllowClobber -Repository PSGallery -Force

```

Check the version of Azure module installed. You can have multiple Azure module versions, and by default PowerShell will use the latest one.

```PowerShell
Get-InstalledModule -Name Az -AllVersions | Select-Object -Property Name, Version


```

2. Connect to your Azure account. Note that the TenantId will come in if you use MFA in your Azure subscription. If you attempt to connect to Azure without specifying TenantID and if MFA is enabled, the error message will tell you the exact TenantID to use. 

```PowerShell

Connect-AzAccount -TenantId XXXXXXXXX

```

To find the TenantID from the webUI, go to Azure Active Directory > Overview

![tenantID](tenantID.png)

3. Switching between Azure subscriptions if you have multiple ones

```PowerShell


PS /Users/chwong> Get-AzSubscription

PS /Users/chwong> $context = Get-AzSubscription -SubscriptionId XXXXXXXX

PS /Users/chwong> Set-AzContext $context


```



