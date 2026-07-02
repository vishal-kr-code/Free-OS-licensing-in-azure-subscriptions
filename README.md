# Free-OS-licensing-in-azure-subscriptions
Free OS licensing in azure subscriptions


Since this is a bulk request, it can only be handled through PS or CLI commands, not through the Portal. The PS commands below will return the OS type and indicate whether AHB (the no-licensing-cost option) is enabled. If the data is large, the customer can save the output to a file.
1. Open Windows PowerShell
•	Press Windows + R, type powershell, and press Enter.
•	It is recommended to Run PowerShell as Administrator.
________________________________________
2. ``Install or Import the Azure PowerShell Module``
   
If this is your first time using Azure PowerShell, install the Az module:
``Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force``

If the Az module is already installed, import it:
``Import-Module Az``
________________________________________
3. Sign in to Azure
Run the following command and sign in using your Azure account when prompted:
``Connect-AzAccount``
________________________________________
4. Select the Required Azure Subscription
List all available subscriptions:
``Get-AzSubscription``

Set the appropriate subscription context (replace <your-subscription-id> with your Azure Subscription ID):
``Set-AzContext -SubscriptionId "<your-subscription-id>"``
________________________________________
5. Generate the VM Licensing Report
Run the following command to export the virtual machine licensing information to a CSV file:

``Get-AzVM | Select-Object `
    Name,
    ResourceGroupName,
    Location,
    @{Name="OS_Type"; Expression={$_.StorageProfile.OsDisk.OsType}},
    @{Name="Publisher"; Expression={$_.StorageProfile.ImageReference.Publisher}},
    @{Name="Offer"; Expression={$_.StorageProfile.ImageReference.Offer}},
    @{Name="Sku"; Expression={$_.StorageProfile.ImageReference.Sku}},
    LicenseType |
Export-Csv -Path "C:\Temp\VM_Licensing_Report.csv" -NoTypeInformation``


Note: If the folder C:\Temp does not already exist, please create it before running the command or update the output path to a location that exists.
 
If you see windows server in licensing type, that means AHB is enabled and the customer should not be charged for the license. 
If the customer wants only AHB-enabled VMs, use the PS command below:
Get-AzVM | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
For more details, check the documents below


Explore Azure Hybrid Benefit for Windows VMs - Azure Virtual Machines | Microsoft Learn
Explore Azure Hybrid Benefit for Linux VMs - Azure Virtual Machines | Microsoft Learn
