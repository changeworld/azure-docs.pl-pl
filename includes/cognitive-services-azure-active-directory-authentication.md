---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: f8d6e5de7f907ae78958b8c239649f55257bf7f2
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467515"
---
## <a name="authenticate-with-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

> [!IMPORTANT]
> Obecnie **tylko** interfejs API przetwarzania obrazów, interfejs API rozpoznawania twarzy, interfejs API analizy tekstu i czytnik immersyjny obsługują uwierzytelnianie przy użyciu usługi Azure Active Directory (AAD).

W poprzednich sekcjach pokazano, jak uwierzytelniać się w usłudze Azure Cognitive Services przy użyciu pojedynczego lub wielousługowego klucza subskrypcji. Chociaż te klucze zapewniają szybką i łatwą ścieżkę do rozpoczęcia programowania, są one krótkie w bardziej złożonych scenariuszach, które wymagają kontroli dostępu opartej na rolach. Spójrzmy na to, co jest wymagane do uwierzytelniania za pomocą usługi Azure Active Directory (AAD).

W poniższych sekcjach użyjesz środowiska Azure Cloud Shell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć poddomenę, przypisać role i uzyskać token okaziciela w celu wywołania Cognitive Services platformy Azure. W przypadku zablokowania linki są dostępne w każdej sekcji ze wszystkimi dostępnymi opcjami dla każdego polecenia w Azure Cloud Shell/interfejs wiersza polecenia platformy Azure.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Tworzenie zasobu z poddomeną niestandardową

Pierwszym krokiem jest utworzenie niestandardowej domeny podrzędnej.

1. Zacznij od otworzenia Azure Cloud Shell. następnie [Wybierz subskrypcję](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. Następnie [Utwórz zasób Cognitive Services](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) z poddomeną niestandardową. Nazwa poddomeny musi być globalnie unikatowa i nie może zawierać znaków specjalnych, takich jak: ".", "!", ",".

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Jeśli to się powiedzie, **punkt końcowy** powinien wyświetlić nazwę poddomeny unikatową dla zasobu.


### <a name="assign-a-role-to-a-service-principal"></a>Przypisywanie roli do nazwy głównej usługi

Teraz, gdy masz niestandardową poddomenę skojarzoną z zasobem, musisz przypisać rolę do jednostki usługi.

> [!NOTE]
> Należy pamiętać, że propagowanie przypisań ról usługi AAD może potrwać do 5 minut.

1. Najpierw Zarejestrujmy [aplikację usługi AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Będziesz potrzebować **aplikacji** w następnym kroku.

2. Następnie musisz [utworzyć nazwę główną usługi](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) dla aplikacji AAD.

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Po zarejestrowaniu aplikacji w Azure Portal ten krok zostanie ukończony.

3. Ostatnim krokiem jest [przypisanie roli "Cognitive Services użytkownika"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) do nazwy głównej usługi (w zakresie do zasobu). Przypisując rolę, przyznasz jednostce usługi dostęp do tego zasobu. Można przyznać tej samej jednostce usługi dostęp do wielu zasobów w ramach subskrypcji.
   >[!NOTE]
   > Identyfikator ObjectId nazwy głównej usługi jest używany, a nie ObjectId dla aplikacji.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Przykładowe żądanie

W tym przykładzie hasło jest używane do uwierzytelniania jednostki usługi. Podany token jest następnie używany do wywołania interfejs API przetwarzania obrazów.

1. Pobierz **TenantId**:
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Pobierz token:
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $password
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Wywołaj interfejs API przetwarzania obrazów:
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Alternatywnie można uwierzytelnić jednostkę usługi przy użyciu certyfikatu. Oprócz nazwy głównej usługi, podmiot zabezpieczeń jest również obsługiwany przez posiadanie uprawnień delegowanych za pomocą innej aplikacji usługi AAD. W takim przypadku zamiast haseł lub certyfikatów użytkownicy otrzymają monit o uwierzytelnienie dwuskładnikowe podczas uzyskiwania tokenu.
