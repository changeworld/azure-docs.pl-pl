---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70803010"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Uzyskiwanie tokenu usługi Azure Resource Manager
Usługa Azure Active Directory musi uwierzytelniać wszystkie zadania wykonywane na zasobach przy użyciu usługi Azure Resource Manager. W przykładzie pokazanym w tym miejscu użyto uwierzytelniania hasłem, w przypadku innych podejść zobacz [Uwierzytelnianie żądań usługi Azure Resource Manager][lnk-authenticate-arm].

1. Dodaj następujący kod do **Main** metody w Program.cs pobrać token z usługi Azure AD przy użyciu identyfikatora aplikacji i hasła.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Utwórz **Obiekt ResourceManagementClient,** który używa tokenu, dodając następujący kod na końcu **Metody głównej:**
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Utwórz lub uzyskaj odwołanie do używanej grupy zasobów:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx