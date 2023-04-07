# Searching-Azure-AD-audit-Logs-with-PowerShell (DRAFT)
A few examples of PowerShell command lines to search AD Audit Logs - Requires AzureADPreview module

```powershell
# Searching Azure AD audit logs with PowerShell

# You need the AzureADPreview module to be able to use the *-AzureADAuditDirectoryLogs
# https://learn.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview#directory-auditing

# Audit logs search using the Azure AD Portal: https://learn.microsoft.com/en-us/azure/active-directory/reports-monitoring/concept-audit-logs

# To use powershell to search Azure AD audit logs, you need the AzureADPreview - the install-module has to be done only once
install-module AzureADPreview

# On future Powershell sessions, you will only need to import the module
import-module AzureADPreview


# Verify you have the Azure AD commands:
Get-Command -Module AzureADPreview

# Connect AzureAD
Connect-AzureAD

# Example getting Audit Logs from a aspecific date:
Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2023-03-20" | fl Category, REsult, TargetResources


<# Sample Output object:

Id                  : Directory_e74dd833-1a9f-41ae-b7fc-32bb9c6103d1_CFTA9_32888399
Category            : UserManagement
CorrelationId       : e74dd833-1a9f-41ae-b7fc-32bb9c6103d1
Result              : success
ResultReason        : 
ActivityDisplayName : Update user
ActivityDateTime    : 3/22/2023 5:29:56 PM
LoggedByService     : Core Directory
OperationType       : Update
InitiatedBy         : class DirectoryAuditLogObjectInitiatedBy {
                        User: class InitiatedByUser {
                        Id: 011d1565-c81c-410c-bd24-b899cb4b0af6
                        DisplayName: 
                        IpAddress: 
                        UserPrincipalName: Sam@CanadaDrey.ca
                      }
                      
                        App: 
                      }
                      
TargetResources     : {class TargetResource {
                        Id: ac5fa2bf-8fa5-4eb8-a68c-49d339150177
                        DisplayName: 
                        Type: User
                        UserPrincipalName: Alain.Posteur@CanadaDrey.ca
                        GroupType: 
                        ModifiedProperties: class ModifiedProperties {
                      }
                      
                      }
                      }
AdditionalDetails   : {class AdditionalDetail {
                        Key: UserType
                        Value: Member
                      }
                      }

#>


Get-AzureADAuditDirectoryLogs -Filter "targetResources/any(tr:tr/userprincipalname eq 'Alain.Posteur@CanadaDrey.ca')" | ft @{Label = 'TargetResource';Expression={$_.TargetResources.UserPrincipalName}},@{Label = 'TargetResource';Expression={$_.TargetResources.UserPrincipalName}},Category, Result, ActivityDisplayName, ActivityDateTime, @{Label = 'InitiatedBy';Expression= {$_.InitiatedBy.User.UserPrincipalName}}


Get-AzureADAuditDirectoryLogs -Filter "targetResources/any(tr:tr/userprincipalname eq 'Alain.Posteur@CanadaDrey.ca')" | select -last 1 | Select -ExpandProperty InitiatedBy | fl


Get-AzureADUser


Get-module | ? {$_.Name -like "*Azure*"}


#NOTE: you can also use Unified Audit Log search

Connect-ExchangeOnline -Prefix EXOSAM

Search-EXOSAMUnifiedAuditLog -ObjectIds "Alain.Posteur@CanadaDrey.ca" -StartDate 3/21/2023 -EndDate 3/23/2023

```
