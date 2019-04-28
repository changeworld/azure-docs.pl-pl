---
title: Biblioteki zarządzania usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Zarządzaj przestrzeniami nazw magistrali usług i jednostki z .NET do obsługi komunikatów.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: d6c2cd813e96b40fc9f95785a1fd28e324a0437b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315882"
---
# <a name="service-bus-management-libraries"></a>Biblioteki zarządzania usługi Service Bus

Biblioteki zarządzania usługi Azure Service Bus można dynamicznie aprowizować przestrzeni nazw usługi Service Bus i jednostki. Umożliwia to złożonych wdrożeń i scenariusze dotyczące przesyłania komunikatów oraz sprawia, że można programowo określić, jakich jednostek do aprowizowania. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Namespace tworzenia, aktualizacji, usunięcia
* Tworzenie kolejki, aktualizowanie, usuwanie
* Tworzenie tematu, aktualizowanie, usuwanie
* Tworzenie subskrypcji, aktualizowanie, usuwanie

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, przy użyciu biblioteki zarządzania usługi Service Bus, musi uwierzytelniać się przy użyciu usługi Azure Active Directory (Azure AD). Usługa Azure AD wymaga, aby uwierzytelniać się jako nazwy głównej usługi, która zapewnia dostęp do zasobów platformy Azure. Aby uzyskać informacji na temat tworzenia jednostki usługi Zobacz jedną z następujących artykułów:  

* [Tworzenie aplikacji usługi Active Directory i jednostki usługi, które mogą uzyskiwać dostęp do zasobów za pomocą witryny Azure portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Te samouczki zawierają z `AppId` (identyfikator klienta), `TenantId`, i `ClientSecret` (klucz uwierzytelniania), które są używane do uwierzytelnienia za pomocą biblioteki zarządzania. Konieczne jest posiadanie **właściciela** uprawnienia dla grupy zasobów, na którym chcesz uruchomić.

## <a name="programming-pattern"></a>Wzorzec programowania

Wzorzec do manipulowania dowolny zasób usługi Service Bus następujące wspólny protokół:

1. Uzyskaj token z usługi Azure AD przy użyciu **Microsoft.IdentityModel.Clients.ActiveDirectory** biblioteki:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Utwórz `ServiceBusManagementClient` obiektu:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Ustaw `CreateOrUpdate` parametry podane wartości:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Wykonaj następujące wywołanie:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>Kolejne kroki

* [Przykład programu .NET zarządzania](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Dokumentacja interfejsu API Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
