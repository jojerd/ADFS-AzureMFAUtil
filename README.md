# ADFS-AzureMFAUtil
This utility will automate checking system prerequisites, new certificate generation, and renewal. It will also allow you to update TLS 1.2 registry keys to ensure cross-premise functionality
after the recent deprecation of TLS 1.0 and TLS 1.1  in Azure. https://techcommunity.microsoft.com/t5/azure/azure-active-directory-tls-1-0-tls-1-1-and-3des-deprecation/m-p/2148244

# Notice of Operation

This will have to be run on every ADFS server in the farm per instructions as the certificates that are generated are per server.

Ensure that these steps are performed on all AD FS servers in the farm. If you have multiple AD FS servers in your farm, you can perform the necessary configuration remotely using Azure AD PowerShell.
https://learn.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers

# Operation

When executing the script you will be presented with a number of options on how you would like to proceed:

![AzureMFAOptions](https://user-images.githubusercontent.com/55394498/208769872-230e394c-5236-4dcd-89fc-cd17dbebbd1c.jpg)

# Check ADFS Azure MFA Pre-Requisites
As the option suggest it will go through and check the systems prerequisites to make sure that it meets the minimum requirements listed here:
https://learn.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#pre-requisites
I also have it checking a few other things such as TLS 1.2 compliance as well as checking every IP that is returned for the required endpoints.

Say for example if one or 2 of the IP addresses fail to connect over port 443 you will see an output like this:

![BadIPs](https://user-images.githubusercontent.com/55394498/208770570-a6eefc20-7226-40cb-8adb-1aaffdc7db7a.jpg)

If the server meets all the requirements that it checks you will receive an output like this:

![GoodIPs](https://user-images.githubusercontent.com/55394498/208770812-ba68785d-fcb9-43fd-bdbc-d5f9fb4a0386.jpg)

You can then proceed with the other options.

# New ADFS Azure MFA Setup.

This will go through the process of asking you the tenant ID (Should be something like contoso.onmicrosoft.com). Do not use the GUID as it can complicate keeping track of the 
certificates used for the Service Principal Credentials. Once the tenant ID is entered, it will prompt you confirm the name you typed is correct and then generate a certificate
using that name. It will then prompt you to enter your Azure AD credentials so that the certificate can be uploaded to the Azure Multi-Factor Client Service.
Once the upload is complete another popup will be presented (if ran from the ADFS Primary server) to enable MFA functionality for the ADFS environment. After that it will
then ask you to restart the ADFS service.

# Renew Azure MFA Certificate

Will renew the Azure MFA service principal credentials and ask you if the certificate is expired or not. If the certificate is currently expired it will not proceed as you will have to 
delete your current certificates and go through the New ADFS Azure MFA Setup to generate new certificates. However if the certificate is not currently expired it will generate a new
certificate and prompt you for the Azure AD credentials to upload the new Service Principal Credential to the Azure Multi-Factor Client Service.

# Fix TLS 1.2 Compliance

If during the prerequisites check TLS 1.2 fails the compliance check you can select this option to ensure that TLS 1.2 will be used by your ADFS servers in the farm. I also included
a function to be able to update all of the servers in the farm, it just requires a manual input by you the user of the server names to update.
Once the TLS 1.2 registry keys have been updated it will prompt you to restart the server (local only, it will not prompt for remote servers) and if you select yes will reboot the server for you.
So be very careful if this is a production server so as to not interrupt your users. If you select no, a reboot will not happen you will just have to reboot at a later time for the registry changes 
to take affect.

# View and or Clean up Azure MFA Certificates.

This option will list the current certificates associated with your Azure Multi-Factor Client Service and will provide you an option to delete any certificates. Be EXTREMELY careful with this
option and only delete Service Principal Credential certificates that you are sure are expired and are no longer being used.

