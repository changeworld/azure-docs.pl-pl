---
title: Utwórz aplikację usługi Azure AD, aby uzyskać dostęp do interfejsu API usługi Azure Media Services za pomocą programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację usługi Azure Active Directory (Azure AD) i skonfigurować go do dostępu do interfejsu API usługi Azure Media Services za pomocą programu PowerShell.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 1da963f8aaf356988df2a7c9bf9923aafa186cca
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259335"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Tworzenie aplikacji usługi Azure AD za pomocą interfejsu API usługi Azure Media Services za pomocą programu PowerShell

Dowiedz się, jak utworzyć aplikację usługi Azure Active Directory (Azure AD) i jednostki usługi dostęp do zasobów usługi Azure Media Services za pomocą skryptu programu PowerShell.  

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Konto platformy Azure. Jeśli nie masz konta, skorzystaj z [bezpłatna wersja próbna platformy](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services w witrynie Azure portal](media-services-portal-create-account.md).

- Azure PowerShell. Aby uzyskać więcej informacji, zobacz [sposób używania programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Tworzenie aplikacji usługi Azure AD przy użyciu programu PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Zarządzanie kontrolą dostępu opartą na rolach przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Jak ręcznie skonfigurować aplikacje demona przy użyciu certyfikatów](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą [przekazywania plików na koncie](media-services-portal-upload-files.md).
