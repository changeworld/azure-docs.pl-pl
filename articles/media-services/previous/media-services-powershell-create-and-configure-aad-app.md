---
title: Utwórz aplikację usługi Azure AD na dostęp do interfejsu API Azure Media Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację usługi Azure Active Directory (Azure AD) i skonfigurowany do dostępu do interfejsu API Azure Media Services przy użyciu programu PowerShell.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 36f00c2b794730ae8191238c516cfedf871851bd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Utwórz aplikację usługi Azure AD, aby korzystać z interfejsu API Azure Media Services przy użyciu programu PowerShell

Dowiedz się, jak używać skryptu PowerShell do tworzenia aplikacji usługi Azure Active Directory (Azure AD) i nazwę główną usługi dostępu do zasobów usługi Azure Media Services.  

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, Rozpocznij od [Azure bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [utworzyć konto usługi Azure Media Services w portalu Azure](media-services-portal-create-account.md).
- Wersja programu PowerShell Azure 0.8.8 lub nowszym. Aby uzyskać więcej informacji, zobacz [sposobu korzystania z programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Polecenia cmdlet Menedżera zasobów systemu Azure.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Utwórz aplikację usługi Azure AD za pomocą programu PowerShell  

```powershell
Connect-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [Zarządzanie oparte na rolach kontroli dostępu przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Jak ręcznie skonfigurować demon aplikacji za pomocą certyfikatów](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z [przekazywanie plików do konta](media-services-portal-upload-files.md).
