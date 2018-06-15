---
title: Biblioteki zarządzania usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Zarządzanie przestrzeni nazw usługi Event Hubs i jednostek z platformy .NET
services: event-hubs
cloud: na
documentationcenter: na
author: sethmanheim
manager: timlt
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 2ae2f8f2006507284338fb4fa62e4942476cf2bc
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26780977"
---
# <a name="event-hubs-management-libraries"></a>Biblioteki zarządzania centra zdarzeń

Biblioteki zarządzania usługi Event Hubs można dynamicznie inicjują obsługę przestrzeni nazw usługi Event Hubs i jednostek. Tego rodzaju dynamiczne umożliwia złożone wdrożenia i scenariusze obsługi komunikatów, tak aby programowo można określić, jakie jednostki do udostępniania. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Namespace tworzenia, aktualizacji, usuwania
* Tworzenie centra zdarzeń, aktualizacji, usuwania
* Tworzenie grupy konsumentów, aktualizacji, usuwania

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć korzystanie z usługi Event Hubs biblioteki zarządzania musi uwierzytelniać usłudze Azure Active Directory (AAD). AAD wymaga, aby uwierzytelniać się jako nazwy głównej usługi, która zapewnia dostęp do zasobów platformy Azure. Informacji o tworzeniu usługę podmiotu zabezpieczeń zobacz następujące artykuły:  

* [Tworzenie aplikacji usługi Active Directory i nazwy głównej usługi, który ma dostęp do zasobów za pomocą portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Te samouczki oferuje `AppId` (identyfikator klienta), `TenantId`, i `ClientSecret` (klucz uwierzytelniania), które są używane do uwierzytelniania przez biblioteki zarządzania. Musi mieć **właściciela** uprawnienia dla grupy zasobów, na którym chcesz uruchomić.

## <a name="programming-pattern"></a>Wzorzec programowania

Wzorzec do manipulowania dowolnego zasobu usługi Event Hubs obejmuje wspólny protokół:

1. Uzyskać token z przy użyciu usługi AAD `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteki.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Utwórz `EventHubManagementClient` obiektu.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Ustaw `CreateOrUpdate` Twojego podanych wartości parametrów.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Wykonać wywołania.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Kolejne kroki
* [Przykładowe zarządzania .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Odwołanie Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
