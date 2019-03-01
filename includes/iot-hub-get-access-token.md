---
author: robinsh
ms.author: robin.shahan
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: e2d6b6413d1e397eaa3c53f28394dcf321dac729
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011772"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Uzyskiwanie tokenu usługi Azure Resource Manager
Usługa Azure Active Directory musi uwierzytelniać wszystkie zadania, które wykonują zasobów przy użyciu usługi Azure Resource Manager. W poniższym przykładzie poniżej użyto uwierzytelniania hasła innych metod można znaleźć [żądań uwierzytelniania usługi Azure Resource Manager][lnk-authenticate-arm].

1. Dodaj następujący kod do **Main** metody w pliku Program.cs, aby pobrać token z usługi Azure AD przy użyciu identyfikatora aplikacji i hasła.
   
    ```
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
2. Tworzenie **ResourceManagementClient** obiekt, który używa tokenu, dodając następujący kod na końcu **Main** metody:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Utwórz lub uzyskaj odwołanie do grupy zasobów, której używasz:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx