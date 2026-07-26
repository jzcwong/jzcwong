# How to use basic authentication to log into kudo/SCM(source control manager) console for App Service

There may be some situations where you will want to use basic authentication to log into kudo/SCM console for an App Service. By default, Azure AD authentication is used for this purpose and basic authentication is disabled. 

The steps involved are:
 - enable basic authentication for the App Service
 - reset or update the credentials used for basic authentication
 - access the kudo/SCM URL for basic authentication

## Enable basic authentication for the App Service via CLI, the second command below also enables FTP
```
az resource update --resource-group <resource-group-name> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<app-service-name> --set properties.allow=true
  
az resource update --resource-group <resource-group-name --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<app-service-name> --set properties.allow=true

```
You can check via the portal as well

![basicauthportal](basicauthappsvc.png)



## Reset or update credentials for basic auth via portal
Here you can reset or update the password for the User Scope credentials

![resetbasicauthportal](resetbasicauthappsvc.png)


## Log into the URL for kudo/SCM supporting basic authentication
The url is https://<appsvc_name>.scm.<asev3_name>.appserviceenvironment.net/basicauth

Useful links:
- https://learn.microsoft.com/en-us/azure/app-service/resources-kudu#access-kudu-for-your-app
- https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service#authentication

