---
title: Biblioteki zarządzania — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące biblioteki, która służy do zarządzania usługi Azure Event Hubs w przestrzeni nazw i jednostek z platformy .NET.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746662"
---
# <a name="event-hubs-management-libraries"></a>Biblioteki zarządzania usługi Event Hubs

Biblioteki zarządzania usługi Azure Event Hubs umożliwia dynamicznie aprowizuj przestrzeni nazw usługi Event Hubs i jednostek. Ten dynamiczny charakter umożliwia złożonych wdrożeń i scenariusze dotyczące przesyłania komunikatów, tak, aby programowo można określić, jakich jednostek do aprowizowania. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Namespace tworzenia, aktualizacji, usunięcia
* Tworzenie zdarzeń usługi Event Hubs, aktualizowanie, usuwanie
* Tworzenie grupy konsumentów, aktualizacji, usunięcia

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, przy użyciu biblioteki zarządzania usługi Event Hubs, wymagane jest uwierzytelnienie za pomocą usługi Azure Active Directory (AAD). Usługi AAD wymaga, aby uwierzytelniać się jako nazwy głównej usługi, która zapewnia dostęp do zasobów platformy Azure. Aby uzyskać informacji na temat tworzenia jednostki usługi Zobacz jedną z następujących artykułów:  

* [Tworzenie aplikacji usługi Active Directory i jednostki usługi, które mogą uzyskiwać dostęp do zasobów za pomocą witryny Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Te samouczki zawierają z `AppId` (identyfikator klienta), `TenantId`, i `ClientSecret` (klucz uwierzytelniania), które są używane do uwierzytelnienia za pomocą biblioteki zarządzania. Konieczne jest posiadanie **właściciela** uprawnienia dla grupy zasobów, na którym chcesz uruchomić.

## <a name="programming-pattern"></a>Wzorzec programowania

Wzorzec do manipulowania dowolny zasób usługi Event Hubs następujące wspólny protokół:

1. Uzyskaj token z usługi AAD za pomocą `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteki.
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

1. Ustaw `CreateOrUpdate` parametry do określonej wartości.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Wykonywanie wywołania.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Kolejne kroki
* [.NET — Zarządzanie próbki](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Odwołanie Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
