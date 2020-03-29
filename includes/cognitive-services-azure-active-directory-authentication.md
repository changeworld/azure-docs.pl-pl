---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262597"
---
## <a name="authenticate-with-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

> [!IMPORTANT]
> 1. Obecnie **tylko** interfejs API przetwarzania obrazów komputerowych, interfejs API twarzy, interfejs API analizy tekstu, program Immersive Reader, aparat rozpoznawania formularzy, detektor anomalii i wszystkie usługi Bing z wyjątkiem uwierzytelniania usługi Bing Custom Search przy użyciu usługi Azure Active Directory (AAD).
> 2. Uwierzytelnianie AAD musi być zawsze używane razem z niestandardową nazwą poddomeny zasobu platformy Azure. [Regionalne punkty końcowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) nie obsługują uwierzytelniania AAD.

W poprzednich sekcjach pokazano, jak uwierzytelnić się w usługach Azure Cognitive Services przy użyciu klucza subskrypcji z jedną lub wieloma usługami. Klucze te zapewniają szybką i łatwą ścieżkę do rozpoczęcia programowania, ale nie są one w bardziej złożonych scenariuszach, które wymagają kontroli dostępu opartego na rolach. Przyjrzyjmy się, co jest wymagane do uwierzytelniania przy użyciu usługi Azure Active Directory (AAD).

W poniższych sekcjach użyjesz środowiska usługi Azure Cloud Shell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć poddomenę, przypisać role i uzyskać token nośnika, aby wywołać usługi Azure Cognitive Services. Jeśli utkniesz, łącza są dostarczane w każdej sekcji ze wszystkimi dostępnymi opcjami dla każdego polecenia w usłudze Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Tworzenie zasobu z niestandardową poddomeną

Pierwszym krokiem jest utworzenie niestandardowej poddomeny. Jeśli chcesz użyć istniejącego zasobu usług Cognitive Services, który nie ma niestandardowej nazwy poddomeny, postępuj zgodnie z instrukcjami w [subdomenach niestandardowych usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) aby włączyć niestandardową poddomenę zasobu.

1. Zacznij od otwarcia powłoki chmury azure. Następnie [wybierz subskrypcję:](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Następnie [utwórz zasób usług Cognitive Services](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) z niestandardową poddomeną. Nazwa poddomeny musi być unikatowa globalnie i nie może zawierać znaków specjalnych, takich jak: ".", "!", ",".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Jeśli zakończy się powiedzie, **punkt końcowy** powinien wyświetlać nazwę poddomeny unikatową dla zasobu.


### <a name="assign-a-role-to-a-service-principal"></a>Przypisywanie roli do jednostki usługi

Teraz, gdy masz niestandardową poddomenę skojarzoną z zasobem, musisz przypisać rolę do jednostki usługi.

> [!NOTE]
> Należy pamiętać, że ich propagowanie zadań związanych z rolami usługi AAD może potrwać do pięciu minut.

1. Najpierw zarejestrujmy aplikację [AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Będziesz potrzebować **ApplicationId** w następnym kroku.

2. Następnie należy [utworzyć jednostkę usługi](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) dla aplikacji AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Jeśli zarejestrujesz aplikację w witrynie Azure portal, ten krok zostanie ukończony dla Ciebie.

3. Ostatnim krokiem jest [przypisanie roli "Użytkownik usług Cognitive Services"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) do jednostki usługi (o zakresie do zasobu). Przypisując rolę, udzielasz użytkownikowi usługi głównego dostępu do tego zasobu. Można udzielić tego samego dawcy usługi dostęp do wielu zasobów w ramach subskrypcji.
   >[!NOTE]
   > ObjectId jednostki usługi jest używany, a nie ObjectId dla aplikacji.
   > ACCOUNT_ID będzie identyfikator zasobu platformy Azure utworzonego konta usług Cognitive Services. Identyfikator zasobu platformy Azure można znaleźć z "właściwości" zasobu w witrynie Azure portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Przykładowe żądanie

W tym przykładzie hasło jest używane do uwierzytelniania jednostki usługi. Dostarczony token jest następnie używany do wywoływania interfejsu API przetwarzania obrazów komputerowych.

1. Pobierz identyfikator **najemcy:**
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Zdobądź token:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Wywołanie interfejsu API przetwarzania:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Alternatywnie jednostki usługi można uwierzytelnić za pomocą certyfikatu. Oprócz jednostki usługi, podmiotu zabezpieczeń użytkownika jest również obsługiwany przez uprawnienia delegowane za pośrednictwem innej aplikacji usługi AAD. W takim przypadku zamiast haseł lub certyfikatów użytkownicy będą monitowani o uwierzytelnianie dwuskładnikowe podczas uzyskiwania tokenu.
