---
title: Biblioteki zarządzania usługą Azure Service Bus| Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak używać bibliotek zarządzania usługi Azure Service Bus do dynamicznego aprowizowania obszarów nazw i jednostek usługi Service Bus.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: d0e90d9278ede97de04ad8efeaa59d94a4567f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756270"
---
# <a name="service-bus-management-libraries"></a>Biblioteki zarządzania usługi Service Bus

Biblioteki zarządzania usługi Azure Service Bus można dynamicznie aprowizować obszarów nazw usługi Service Bus i jednostek. Umożliwia to złożone wdrożenia i scenariusze obsługi wiadomości i umożliwia programowe określenie, jakie jednostki do aprowizowania. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Tworzenie, aktualizowanie, usuwanie obszaru nazw
* Tworzenie, aktualizowanie, usuwanie kolejek
* Tworzenie tematów, aktualizowanie, usuwanie
* Tworzenie subskrypcji, aktualizowanie, usuwanie

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć korzystanie z bibliotek zarządzania usługą Service Bus, należy uwierzytelnić się za pomocą usługi Azure Active Directory (Azure AD). Usługa Azure AD wymaga uwierzytelnienia jako jednostki usługi, która zapewnia dostęp do zasobów platformy Azure. Aby uzyskać informacje dotyczące tworzenia jednostki usługi, zobacz jeden z następujących artykułów:  

* [Użyj witryny Azure Portal do tworzenia aplikacji i jednostki usługi Active Directory, która może uzyskiwać dostęp do zasobów](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Te samouczki zapewniają `AppId` (identyfikator klienta), `TenantId`i `ClientSecret` (klucz uwierzytelniania), z których wszystkie są używane do uwierzytelniania przez biblioteki zarządzania. Musisz mieć uprawnienia **Właściciela** dla grupy zasobów, w której chcesz uruchomić.

## <a name="programming-pattern"></a>Wzór programowania

Wzorzec do manipulowania dowolnym zasobem usługi Service Bus jest zgodny ze wspólnym protokołem:

1. Uzyskaj token z usługi Azure AD przy użyciu biblioteki **Microsoft.IdentityModel.Clients.ActiveDirectory:**
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Utwórz `ServiceBusManagementClient` obiekt:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Ustaw `CreateOrUpdate` parametry na określone wartości:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Wykonaj wywołanie:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Ukończ kod, aby utworzyć kolejkę
Oto pełny kod do utworzenia kolejki usługi Service Bus: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Pełny przykład można znaleźć w [przykładzie zarządzania .NET w usłudze GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Następne kroki
[Odwołanie interfejsu API usługi Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
