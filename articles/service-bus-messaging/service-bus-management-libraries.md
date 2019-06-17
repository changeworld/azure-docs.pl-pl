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
ms.date: 06/05/2019
ms.author: aschhab
ms.openlocfilehash: bd2a594bfd7fbac53deacc767ace3cd44484798e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058107"
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

## <a name="complete-code-to-create-a-queue"></a>Kompletny kod, aby utworzyć kolejkę
Oto kompletny kod można utworzyć kolejki usługi Service Bus: 

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
                    "https://management.core.windows.net/",
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
> Aby uzyskać kompletny przykład, zobacz [przykład zarządzania platformy .NET w witrynie GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Kolejne kroki
[Dokumentacja interfejsu API Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
