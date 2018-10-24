---
title: Utwórz aplikację usługi Azure AD, aby uzyskać dostęp do interfejsu API usługi Azure Media Services za pomocą programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację usługi Azure Active Directory (Azure AD) i skonfigurować go do dostępu do interfejsu API usługi Azure Media Services za pomocą programu PowerShell.
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
ms.openlocfilehash: 2aa777cf0c2068a5ee2382a9e75fc65db8a27207
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957305"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Tworzenie aplikacji usługi Azure AD za pomocą interfejsu API usługi Azure Media Services za pomocą programu PowerShell

Dowiedz się, jak utworzyć aplikację usługi Azure Active Directory (Azure AD) i jednostki usługi dostęp do zasobów usługi Azure Media Services za pomocą skryptu programu PowerShell.  

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, skorzystaj z [bezpłatna wersja próbna platformy](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services w witrynie Azure portal](media-services-portal-create-account.md).
- Azure PowerShell w wersji 0.8.8 lub nowszej wersji. Aby uzyskać więcej informacji, zobacz [sposób używania programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Poleceń cmdlet usługi Azure Resource Manager.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Tworzenie aplikacji usługi Azure AD przy użyciu programu PowerShell  

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

- [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Zarządzanie kontrolą dostępu opartą na rolach przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Jak ręcznie skonfigurować aplikacje demona przy użyciu certyfikatów](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą [przekazywania plików na koncie](media-services-portal-upload-files.md).
