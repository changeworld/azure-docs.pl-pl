---
title: Biblioteki zarządzania — Usługi Azure Event Hubs| Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje na temat biblioteki, której można używać do zarządzania obszarami nazw i encjami usługi Azure Event Hubs z platformy .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60746662"
---
# <a name="event-hubs-management-libraries"></a>Biblioteki zarządzania usługi Event Hubs

Biblioteki zarządzania usługi Azure Event Hubs umożliwiają dynamiczne aprowizowanie obszarów nazw i jednostek centrum zdarzeń. Ten dynamiczny charakter umożliwia złożone wdrożenia i scenariusze obsługi wiadomości, dzięki czemu można programowo określić, jakie jednostki do aprowizowania. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Tworzenie, aktualizowanie, usuwanie obszaru nazw
* Tworzenie, aktualizowanie, usuwanie centrów zdarzeń
* Tworzenie, aktualizowanie, usuwanie grup odbiorców

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć korzystanie z bibliotek zarządzania usługi Event Hubs, należy uwierzytelnić się za pomocą usługi Azure Active Directory (AAD). Usługa AAD wymaga uwierzytelnienia jako jednostki usługi, która zapewnia dostęp do zasobów platformy Azure. Aby uzyskać informacje dotyczące tworzenia jednostki usługi, zobacz jeden z następujących artykułów:  

* [Użyj witryny Azure Portal do tworzenia aplikacji i jednostki usługi Active Directory, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Te samouczki zapewniają `AppId` (identyfikator klienta), `TenantId`i `ClientSecret` (klucz uwierzytelniania), z których wszystkie są używane do uwierzytelniania przez biblioteki zarządzania. Musisz mieć uprawnienia **Właściciela** dla grupy zasobów, w której chcesz uruchomić.

## <a name="programming-pattern"></a>Wzór programowania

Wzorzec do manipulowania dowolnym zasobem Centrum zdarzeń jest zgodny ze wspólnym protokołem:

1. Uzyskaj token z usługi AAD przy użyciu `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteki.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Utwórz `EventHubManagementClient` obiekt.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Ustaw `CreateOrUpdate` parametry na określone wartości.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Wykonaj wywołanie.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Następne kroki
* [Przykład zarządzania siecią .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Dokumentacja microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
