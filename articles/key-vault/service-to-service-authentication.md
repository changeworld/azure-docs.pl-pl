---
title: Usługa Usługa uwierzytelniania usługi Azure Key Vault przy użyciu platformy .NET
description: Użyj biblioteki Microsoft.Azure.Services.AppAuthentication do uwierzytelniania w usłudze Azure Key Vault przy użyciu platformy .NET.
keywords: lokalne poświadczenia uwierzytelniania usługi Azure key vault
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 03/05/2019
ms.topic: conceptual
ms.service: key-vault
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: 642c30c4df233476a8f649f7b5f30d0538b0e83f
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629853"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Usługa Usługa uwierzytelniania usługi Azure Key Vault przy użyciu platformy .NET

Do uwierzytelniania usługi Azure Key Vault, potrzebujesz poświadczeń usługi Azure Active Directory (AD), certyfikatu lub wspólny klucz tajny. Zarządzanie tych poświadczeń, może być trudne i jest kuszące zbioru poświadczeń w aplikacji, umieszczając je w plikach źródłowych lub konfiguracji.

`Microsoft.Azure.Services.AppAuthentication` Dla biblioteki .NET upraszcza ten problem. Do uwierzytelniania podczas tworzenia lokalnego używane poświadczenia dla deweloperów. Gdy rozwiązanie jest później wdrożone na platformie Azure, biblioteka przełącza się do poświadczeń aplikacji.  

Podczas tworzenia lokalnego przy użyciu poświadczeń dewelopera jest bezpieczniejsza, ponieważ nie trzeba utworzyć poświadczenia usługi Azure AD lub udostępnić poświadczeń między deweloperów.

`Microsoft.Azure.Services.AppAuthentication` Biblioteki zarządza uwierzytelnianiem automatycznie, co z kolei pozwala na skoncentrowanie się na swoje rozwiązanie, a nie poświadczeń.

`Microsoft.Azure.Services.AppAuthentication` Biblioteka obsługuje lokalny rozwój za pomocą programu Microsoft Visual Studio, wiersza polecenia platformy Azure lub zintegrowane uwierzytelnianie usługi Azure AD. Podczas wdrażania zasobów platformy Azure, który obsługuje tożsamości zarządzanej, biblioteka automatycznie używa [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/msi-overview.md). Nie kodu lub zmiany konfiguracji są wymagane. Biblioteka obsługuje również bezpośredniemu wykorzystaniu usługi Azure AD [poświadczeń klienta](../azure-resource-manager/resource-group-authenticate-service-principal.md) gdy tożsamość zarządzana nie jest dostępna lub nie można ustalić kontekstu zabezpieczeń dla deweloperów podczas tworzenia lokalnego.

## <a name="using-the-library"></a>Za pomocą biblioteki

Dla aplikacji .NET jest najprostszym sposobem, aby pracować z tożsamości zarządzanej `Microsoft.Azure.Services.AppAuthentication` pakietu. Oto jak rozpocząć pracę:

1. Dodaj odwołania do [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) pakiety NuGet do aplikacji. 

2. Dodaj następujący kod:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` Klasa przechowuje w pamięci podręcznej tokenu w pamięci i przywraca je z usługi Azure AD tylko przed jego wygaśnięciem. W związku z tym, nie musisz już do sprawdzenia wygaśnięcia przed wywołaniem `GetAccessTokenAsync` metody. Po prostu Wywołaj metodę, gdy użytkownik chce użyć tokenu. 

`GetAccessTokenAsync` Metoda wymaga identyfikatora zasobu. Aby dowiedzieć się więcej, zobacz [którymi usługami platformy Azure obsługują zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/msi-overview.md).

## <a name="samples"></a>Przykłady

Następujące przykłady show `Microsoft.Azure.Services.AppAuthentication` biblioteki w akcji:

1. [Korzystania z tożsamości zarządzanej można pobrać klucza tajnego z usługi Azure Key Vault w czasie wykonywania](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programowe wdrażanie szablonu usługi Azure Resource Manager z maszyny Wirtualnej platformy Azure za pomocą tożsamości zarządzanej](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Wywoływanie usług platformy Azure z maszyny Wirtualnej systemu Linux na platformie Azure za pomocą platformy .NET Core próbki i tożsamość zarządzaną](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="local-development-authentication"></a>Rozwoju lokalnego uwierzytelniania

Dla wdrożenia lokalnego istnieją dwa scenariusze uwierzytelniania podstawowego:

- [Uwierzytelniania do usług platformy Azure](#authenticating-to-azure-services)
- [Uwierzytelnianie na niestandardowych usług](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Uwierzytelniania do usług platformy Azure

Lokalne maszyny nie obsługują zarządzanych tożsamości dla zasobów platformy Azure.  W rezultacie `Microsoft.Azure.Services.AppAuthentication` biblioteki wykorzystuje swoje poświadczenia dla deweloperów do uruchamiania w swojego lokalnego środowiska deweloperskiego. Po wdrożeniu rozwiązania na platformie Azure biblioteka używa tożsamości zarządzanej, aby przełączyć się do przepływie przyznawania poświadczeń klienta OAuth 2.0.  Oznacza to, że możesz przetestować ten sam kod lokalnie i zdalnie bez obaw.

Na potrzeby lokalnego programowania dla `AzureServiceTokenProvider` pobiera tokeny przy użyciu **programu Visual Studio**, **interfejsu wiersza polecenia platformy Azure** (CLI) lub **zintegrowane uwierzytelnianie usługi Azure AD**. Każda opcja zostanie podjęta próba sekwencyjnie i biblioteka używa pierwszej opcji, które zakończyło się pomyślnie. Jeśli opcja nie działa, `AzureServiceTokenProviderException` wyjątek wraz ze szczegółowymi informacjami.

### <a name="authenticating-with-visual-studio"></a>Uwierzytelnianie za pomocą programu Visual Studio

Uwierzytelnianie za pomocą programu Visual Studio ma następujące wymagania wstępne:

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) lub nowszej.

2. [Rozszerzenie do uwierzytelniania aplikacji dla programu Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354)dostępna jako osobne rozszerzenie dla programu Visual Studio 2017 Update 5 i powiązane z produktu w aktualizacji 6 lub nowszy. Aktualizacji 6 lub nowszym instalacja rozszerzenia uwierzytelniania aplikacji można sprawdzić, wybierając pozycję Narzędzia programowanie na platformie Azure w Instalatorze programu Visual Studio.
 
Zaloguj się do programu Visual Studio i użyć **narzędzia**&nbsp;>&nbsp;**opcje**&nbsp;>&nbsp;**usługi platformy Azure Uwierzytelnianie** wybierz konto dla wdrożenia lokalnego. 

Jeśli napotkasz problemy przy użyciu programu Visual Studio, takie jak błędy dotyczące pliku dostawcę tokenów, należy dokładnie przejrzeć następujące kroki. 

Również może być konieczne ponowne uwierzytelnianie tokenu programu dla deweloperów. Aby to zrobić, przejdź do **narzędzia**&nbsp;>&nbsp;**opcje**>**Azure&nbsp;usługi&nbsp;uwierzytelniania**  i poszukaj **ponowne uwierzytelnienie** łącze w ramach wybranego konta.  Wybierz go do uwierzytelniania. 

### <a name="authenticating-with-azure-cli"></a>Uwierzytelnianie przy użyciu wiersza polecenia platformy Azure

Aby użyć wiersza polecenia platformy Azure dla wdrożenia lokalnego:

1. Zainstaluj [v2.0.12 wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub nowszej. Uaktualnianie wcześniejszych wersji. 

2. Użyj **az login** zalogować się do platformy Azure.

Użyj `az account get-access-token` Aby zweryfikować dostęp.  Jeśli otrzymasz komunikat o błędzie, sprawdź, czy krok 1 zakończyła się pomyślnie. 

Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure do domyślnego katalogu, może wystąpić błąd raporty `AzureServiceTokenProvider` nie można odnaleźć ścieżki dla wiersza polecenia platformy Azure.  Użyj **AzureCLIPath** zmiennej środowiskowej, aby określić folder instalacji wiersza polecenia platformy Azure. `AzureServiceTokenProvider` dodaje katalogu wskazanym na **AzureCLIPath** zmiennej środowiskowej, aby **ścieżki** zmiennej środowiskowej, gdy jest to konieczne.

Jeśli użytkownik jest zalogowany do wiersza polecenia platformy Azure przy użyciu wielu kont lub konto użytkownika ma dostęp do wielu subskrypcji, należy określić określoną subskrypcję do użycia.  Aby to zrobić, należy użyć:

```
az account set --subscription <subscription-id>
```

To polecenie generuje dane wyjściowe tylko w przypadku niepowodzenia.  Aby sprawdzić ustawienia bieżącego konta, należy użyć:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Uwierzytelnianie przy użyciu uwierzytelniania usługi Azure AD Integracja

Aby użyć uwierzytelniania usługi Azure AD, upewnij się, że:

- Usługi active directory w środowisku lokalnym [synchronizacje z usługą Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- Twój kod jest uruchomiony na komputerze przyłączonym do domeny.


### <a name="authenticating-to-custom-services"></a>Uwierzytelnianie na niestandardowych usług

Podczas wywołania usług usługi platformy Azure, poprzednie kroki działać, ponieważ usługi platformy Azure, Zezwalaj na dostęp do użytkowników i aplikacji.  

Podczas tworzenia usługi, który wywołuje to usługa niestandardowa, należy użyć poświadczeń klienta usługi Azure AD do uwierzytelniania rozwoju lokalnego.  Dostępne są dwie opcje: 

1.  Zaloguj się do platformy Azure za pomocą nazwy głównej usługi:

    1.  [Tworzenie jednostki usługi](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Zaloguj się przy użyciu wiersza polecenia platformy Azure:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Ponieważ nazwy głównej usługi nie mogą mieć dostęp do subskrypcji, użyj `--allow-no-subscriptions` argumentu.

2.  Użyj zmiennych środowiskowych, aby określić szczegóły jednostki usługi.  Aby uzyskać więcej informacji, zobacz [uruchamiania aplikacji przy użyciu nazwy głównej usługi](#running-the-application-using-a-service-principal).

Po zalogowaniu do platformy Azure, `AzureServiceTokenProvider` używa nazwy głównej usługi można pobrać tokenu dla wdrożenia lokalnego.

Dotyczy to tylko rozwoju lokalnego. Po wdrożeniu rozwiązania na platformie Azure biblioteki przełącza się do zarządzanych tożsamości do uwierzytelniania.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Uruchamianie aplikacji za pomocą tożsamości zarządzanej lub tożsamości przypisanych przez użytkownika 

Gdy uruchamiasz swój kod w usłudze Azure App Service lub Maszynie wirtualnej platformy Azure za pomocą tożsamości zarządzanej włączono biblioteki automatycznie używa tożsamość zarządzaną. Wymagane są bez zmian w kodzie. 

Alternatywnie można uwierzytelnić przy użyciu tożsamości przypisanych przez użytkownika. Aby uzyskać więcej informacji o tożsamości przypisanych przez użytkownika, zobacz [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Ciąg połączenia jest określona w [Obsługa ciągu połączenia](#connection-string-support) poniższej sekcji.

## <a name="running-the-application-using-a-service-principal"></a>Uruchamianie aplikacji przy użyciu nazwy głównej usługi 

Może być konieczne utworzenie poświadczeń usługi Azure AD klienta do uwierzytelniania. Typowe przykłady obejmują:

1. Kod jest wykonywany na lokalne Środowisko deweloperskie, ale nie w ramach tożsamości dla deweloperów.  Usługa Service Fabric, na przykład używa [konto NetworkService](../service-fabric/service-fabric-application-secret-management.md) dla wdrożenia lokalnego.
 
2. Kod jest wykonywany na lokalne Środowisko deweloperskie i uwierzytelniania użytkownika to usługa niestandardowa, więc nie możesz użyć swojej tożsamości dla deweloperów. 
 
3. Twój kod jest uruchomiony na zasób obliczeniowy systemu Azure, która nie obsługuje jeszcze zarządzanych tożsamości dla zasobów platformy Azure, takich jak Azure Batch.

Do korzystania z certyfikatu, aby zalogować się do usługi Azure AD:

1. Tworzenie [certyfikatu nazwy głównej usługi](../azure-resource-manager/resource-group-authenticate-service-principal.md). 

2. Wdrażanie certyfikatu do jednej *LocalMachine* lub *CurrentUser* przechowywania. 

3. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** do:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Zastąp *{AppId}*, *{identyfikator dzierżawy}*, i *{Thumbprint}* wartościami utworzoną w kroku 1. Zastąp *{CertificateStore}* z oboma `LocalMachine` lub `CurrentUser`zgodnie z planu wdrożenia usługi.

4. Uruchom aplikację. 

Aby zalogować się przy użyciu usługi Azure AD udostępnione wpisu tajnego poświadczeń:

1. Tworzenie [jednostki usługi przy użyciu hasła](../azure-resource-manager/resource-group-authenticate-service-principal.md) i przyznać jej dostęp do usługi Key Vault. 

2. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** do:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Zastąp _{AppId}_, _{identyfikator dzierżawy}_, i _{ClientSecret}_ wartościami utworzoną w kroku 1.

3. Uruchom aplikację. 

Gdy wszystko jest skonfigurowane prawidłowo, żadne dalsze zmiany kodu są niezbędne.  `AzureServiceTokenProvider` używa zmiennej środowiskowej i certyfikat do uwierzytelniania usługi Azure AD. 

## <a name="connection-string-support"></a>Obsługa ciągu połączenia

Domyślnie `AzureServiceTokenProvider` korzysta na wiele sposobów pobierania tokenu. 

Aby kontrolować ten proces, należy użyć ciągu połączenia przekazany do `AzureServiceTokenProvider` konstruktora lub został określony w *AzureServicesAuthConnectionString* zmiennej środowiskowej. 

Obsługiwane są następujące opcje:

| Połączenie&nbsp;ciąg&nbsp;opcji | Scenariusz | Komentarze|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokalne programowanie | AzureServiceTokenProvider używa codziennych w celu uzyskania tokenu. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokalne programowanie | AzureServiceTokenProvider używa programu Visual Studio w celu uzyskania tokenu. |
| `RunAs=CurrentUser` | Lokalne programowanie | AzureServiceTokenProvider wykorzystuje zintegrowane uwierzytelnianie usługi Azure AD do pobrania tokenu. |
| `RunAs=App` | [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider korzysta z tożsamości zarządzanej można uzyskać tokenu. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Tożsamości przypisanych przez użytkownika dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider używa tożsamości przypisanych przez użytkownika w celu uzyskania tokenu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`   | Jednostka usługi | `AzureServiceTokenProvider` używa certyfikatu, aby uzyskać token z usługi Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Jednostka usługi | `AzureServiceTokenProvider` Aby uzyskać token z usługi Azure AD, używa certyfikatu|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Jednostka usługi |`AzureServiceTokenProvider` używa tajnego klucza, aby uzyskać token z usługi Azure AD. |



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Dowiedz się więcej o [scenariusze uwierzytelniania usługi Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
