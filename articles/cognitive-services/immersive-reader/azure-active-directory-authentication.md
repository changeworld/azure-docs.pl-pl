---
title: Uwierzytelnianie Azure Active Directory (Azure AD)
titleSuffix: Azure Cognitive Services
description: Dokumentacja zestawu SDK czytnika immersyjny
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 8af56d0d7c0f320e607421f12effdb9b37d5f78d
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444616"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Używanie uwierzytelniania Azure Active Directory (Azure AD) z usługą czytnika immersyjny

W poniższych sekcjach użyjesz środowiska Azure Cloud Shell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć nowy zasób czytnika immersyjny z niestandardową domeną poddomeny, a następnie skonfigurować usługę Azure AD w dzierżawie platformy Azure. Po zakończeniu konfiguracji początkowej nastąpi wywołanie usługi Azure AD w celu uzyskania tokenu dostępu, podobnie jak w przypadku korzystania z zestawu SDK czytnika immersyjny. W przypadku zablokowania linki są dostępne w każdej sekcji ze wszystkimi dostępnymi opcjami dla każdego z poleceń interfejsu wiersza polecenia platformy Azure.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Tworzenie zasobu czytnika immersyjny przy użyciu niestandardowej domeny podrzędnej

1. Zacznij od otworzenia [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Następnie [Wybierz subskrypcję](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Następnie [Utwórz zasób czytnika immersyjny](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) z niestandardową poddomeną. 

   >[!NOTE]
   > Nazwa domeny podrzędnej jest używana w zestawie SDK immersyjny podczas uruchamiania czytnika przy użyciu funkcji launchAsync.

   -SkuName może być F0 (warstwa Bezpłatna) lub S0 (warstwa standardowa, również bezpłatna w publicznej wersji zapoznawczej). Warstwa S0 ma wyższy limit szybkości wywołań i nie ma miesięcznego limitu liczby wywołań.

   -Location może być dowolną z następujących: `eastus`, `westus`, `australiaeast` `centralindia` `japaneast`,,,, `northeurope``westeurope`
   
   -CustomSubdomainName musi być globalnie unikatowa i nie może zawierać znaków specjalnych, takich jak: ".", "!", ",".


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   Jeśli to się powiedzie, **punkt końcowy** zasobu powinien wyświetlić nazwę poddomeny unikatową dla zasobu.

   Tutaj przechwytywamy nowo utworzony obiekt zasobu do zmiennej **$Resource** , ponieważ będzie on używany później podczas udzielania dostępu do tego zasobu.


   >[!NOTE]
   > Jeśli utworzysz zasób w Azure Portal, zasób "name" jest używany jako poddomena niestandardowa. Nazwę domeny podrzędnej można sprawdzić w portalu, przechodząc do strony przegląd zasobów i wyszukając poddomenę w punkcie końcowym na liście, na przykład `https://[SUBDOMAIN].cognitiveservices.azure.com/`. Możesz również tutaj zaznaczyć, gdy chcesz uzyskać poddomenę do integracji z zestawem SDK.

   Jeśli zasób został utworzony w portalu, możesz również [uzyskać już istniejący zasób](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) .

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```   

## <a name="assign-a-role-to-a-service-principal"></a>Przypisywanie roli do nazwy głównej usługi

Teraz, gdy masz niestandardową poddomenę skojarzoną z zasobem, musisz przypisać rolę do jednostki usługi.

1. Najpierw [Utwórz aplikację usługi Azure AD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > Hasło, znane także jako "klucz tajny klienta", będzie używane podczas uzyskiwania tokenów uwierzytelniania.

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Tutaj przechwytywamy nowo utworzony obiekt aplikacji usługi Azure AD do zmiennej **$aadApp** do użycia w następnym kroku.   

2. Następnie musisz [utworzyć nazwę główną usługi](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) dla aplikacji usługi Azure AD.

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   W tym miejscu przechwytywano nowo utworzony obiekt jednostki usługi do zmiennej **$Principal** do użycia w następnym kroku.


3. Ostatnim krokiem jest [przypisanie roli "Cognitive Services użytkownika"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) do nazwy głównej usługi (w zakresie do zasobu). Przypisując rolę, przyznasz jednostce usługi dostęp do tego zasobu. Można przyznać tej samej jednostce usługi dostęp do wielu zasobów w ramach subskrypcji.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Ten krok należy wykonać dla każdego tworzonego zasobu czytnika immersyjny. Na przykład w przypadku tworzenia wielu zasobów dla różnych regionów globalnych należy wykonać ten krok dla każdego z tych zasobów regionalnych, aby uwierzytelnianie usługi Azure AD działało dla wszystkich z nich. W przypadku utworzenia nowego zasobu w późniejszym momencie należy również wykonać ten krok przypisania roli dla tego nowego zasobu.


## <a name="obtain-an-azure-ad-token"></a>Uzyskiwanie tokenu usługi Azure AD

W tym przykładzie Twoje hasło służy do uwierzytelniania jednostki usługi w celu uzyskiwania tokenu usługi Azure AD.

1. Pobierz **TenantId**:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Pobierz token:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > Zestaw SDK czytnika immersyjny używa właściwości AccessToken tokenu, np. $token. AccessToken. Szczegóły można znaleźć w [dokumentacji](reference.md) dotyczącej zestawu SDK i [przykłady](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples) kodu.

Alternatywnie można uwierzytelnić jednostkę usługi przy użyciu certyfikatu. Oprócz nazwy głównej usługi, podmioty zabezpieczeń są również obsługiwane przez posiadanie uprawnień delegowanych przez inną aplikację usługi Azure AD. W takim przypadku zamiast haseł lub certyfikatów użytkownicy otrzymają monit o uwierzytelnienie dwuskładnikowe podczas uzyskiwania tokenów.

## <a name="next-steps"></a>Następne kroki

* Wyświetl [samouczek](./tutorial.md) , aby zobaczyć, co jeszcze można zrobić za pomocą zestawu SDK czytnika immersyjny
* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/Microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md)