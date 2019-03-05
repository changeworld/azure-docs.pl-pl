---
title: Pobieranie wartości do uwierzytelniania aplikacji — Azure SQL Database | Dokumentacja firmy Microsoft
description: Tworzenie jednostki usługi do uzyskiwania dostępu do bazy danych SQL z poziomu kodu.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 6c4c001fc538e5ad93a5c4fc3d6405209be7fc53
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309370"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Pobieranie wymaganych wartości do uwierzytelniania aplikacji dostęp do bazy danych SQL z kodu

Tworzenie i zarządzanie usługą SQL Database z kodu, należy zarejestrować aplikację w domenie usługi Azure Active Directory (AAD) w ramach subskrypcji, w której zostały utworzone zasoby platformy Azure.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Tworzenie jednostki usługi dostępu do zasobów z aplikacji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Musisz mieć najnowsze [programu Azure PowerShell](/powershell/azure) zainstalowana i uruchomiona. Aby uzyskać szczegółowe informacje, zobacz [sposobu instalowania programu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt środowiska PowerShell tworzy aplikację usługi Active Directory (AD) oraz jednostkę usługi wymaganą do uwierzytelnienia aplikacji w języku C#. Skrypt generuje wartości wyjściowe potrzebne w poprzednim przykładzie w języku C#. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie usługi podmiotu używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Zobacz także
* [Tworzenie bazy danych SQL w języku C#](sql-database-get-started-csharp.md)
* [Łączenie z bazą danych SQL przy użyciu uwierzytelniania usługi Azure Active Directory](sql-database-aad-authentication.md)

