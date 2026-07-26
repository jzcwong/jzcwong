# How to use Azure AD credentials to log into session host for Azure Virtual Desktop?

To use a user account from Azure AD to log into Azure Virtual Desktop session host, you must meet the following requirements:
 - the session hosts in the host pool must be joined to Azure AD, this is configured during the deployment of the session hosts in the host pool
 - the client from which you are using to access Azure Virtual Desktop needs to be joined to the same Azure AD as the session hosts as mentioned [here](https://learn.microsoft.com/en-gb/azure/virtual-desktop/azure-ad-joined-session-hosts#connect-using-the-windows-desktop-client) otherwise you need to add **targetisaadjoined:i:1** as a custom RDP property to the host pool
 - the Azure AD user account that you are using to log into Azure Virtual Desktop must be assigned **Virtual Machine User Login** role at the Resource Group level that contains the Azure Virtual Desktop resources
 - if you are using Windows client to access the Azure Virtual Desktop, make sure you use the [Remote Desktop Client](https://learn.microsoft.com/en-gb/azure/virtual-desktop/users/connect-windows?tabs=subscribe) and not the [Remote Desktop App](https://learn.microsoft.com/en-gb/azure/virtual-desktop/users/connect-microsoft-store) , the Remote Desktop App does not support Azure AD joined session hosts as mentioned [here](https://learn.microsoft.com/en-gb/azure/virtual-desktop/azure-ad-joined-session-hosts#known-limitations)

## Enable Azure AD Single Sign-on for host pool RDP properties
![avdrdpssoproperty](avdrdpssoproperty.png)

## Custom RDP property on host pool to allow log-in to Azure Virtual Desktop from non-domain joined clients
![avdcustomrdpl](avdhostpoolrdpproperties.png)

## User account given **Virtual Machine User Login** credentials at Resource Group containing AVD
![advvirtualmachinelogin](advvirtualmachinelogin.png)

For more information about creating your own custom images for Azure Virtual Desktop, see:
- [Creating golden images for Azure Virtual Desktop](https://learn.microsoft.com/en-us/azure/virtual-desktop/set-up-golden-image)
- [Generalizing the image](https://learn.microsoft.com/en-us/azure/virtual-machines/generalize)
