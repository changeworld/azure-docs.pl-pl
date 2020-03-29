---
title: Bezpieczne dostarczanie elementu WebHook z usługą Azure AD w usłudze Azure Event Grid
description: W tym artykule opisano sposób dostarczania zdarzeń do punktów końcowych HTTPS chronionych przez usługę Azure Active Directory przy użyciu usługi Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931554"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publikowanie zdarzeń w chronionych punktach końcowych usługi Azure Active Directory

W tym artykule opisano, jak korzystać z usługi Azure Active Directory w celu zabezpieczenia połączenia między subskrypcją zdarzeń a punktem końcowym elementu webhook. Aby zapoznać się z omówieniem aplikacji usługi Azure AD i podmiotów świadczących usługi, zobacz [omówienie platformy tożsamości firmy Microsoft (w wersji 2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

W tym artykule używa portalu Azure do demonstracji, jednak funkcja może być również włączona przy użyciu interfejsu wiersza polecenia, programu PowerShell lub zestawów SDK.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Tworzenie aplikacji usługi Azure AD

Rozpocznij od utworzenia aplikacji usługi Azure AD dla chronionego punktu końcowego. Zobacz: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Skonfiguruj chroniony interfejs API, który ma być wywoływany przez aplikację demona.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Włącz siatkę zdarzeń, aby używać aplikacji usługi Azure AD

Użyj skryptu programu PowerShell poniżej, aby utworzyć zasadę roli i usługi w aplikacji usługi Azure AD. Identyfikator dzierżawy i identyfikator obiektu z aplikacji usługi Azure AD będą potrzebne:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Zmodyfikuj $myTenantId skryptu programu PowerShell, aby użyć identyfikatora dzierżawy usługi Azure AD.
1. Modyfikowanie $myAzureADApplicationObjectId skryptu programu PowerShell w celu użycia identyfikatora obiektu aplikacji usługi Azure AD
1. Uruchom zmodyfikowany skrypt.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Konfigurowanie subskrypcji zdarzeń

W przepływie tworzenia subskrypcji zdarzeń wybierz typ punktu końcowego "Web Hook". Po podaniu identyfikatora URI punktu końcowego kliknij kartę dodatkowe funkcje u góry bloku subskrypcji zdarzeń tworzenia.

![Wybieranie elementu webhook typu punktu końcowego](./media/secure-webhook-delivery/select-webhook.png)

Na karcie Funkcje dodatkowe zaznacz pole wyboru "Użyj uwierzytelniania AAD" i skonfiguruj identyfikator dzierżawy i identyfikator aplikacji:

* Skopiuj identyfikator dzierżawy usługi Azure AD z danych wyjściowych skryptu i wprowadź go w polu Identyfikator dzierżawy usługi AAD.
* Skopiuj identyfikator aplikacji usługi Azure AD z danych wyjściowych skryptu i wprowadź go w polu Identyfikator aplikacji usługi AAD.

    ![Akcja Bezpiecznego elementu Webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące monitorowania dostaw zdarzeń, zobacz [Monitorowanie dostarczania komunikatów w sieci zdarzeń](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
