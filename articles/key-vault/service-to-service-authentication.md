---
title: Usługa Usługa uwierzytelniania usługi Azure Key Vault przy użyciu platformy .NET
description: Użyj biblioteki Microsoft.Azure.Services.AppAuthentication do uwierzytelniania w usłudze Azure Key Vault przy użyciu platformy .NET.
keywords: lokalne poświadczenia uwierzytelniania usługi Azure key vault
author: bryanla
manager: mbaldwin
services: key-vault
ms.author: bryanla
ms.date: 11/27/2018
ms.topic: conceptual
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: 1eadea53dda60ef5ac8bbbc3d9e9cfe4b5b373dc
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423596"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Usługa Usługa uwierzytelniania usługi Azure Key Vault przy użyciu platformy .NET

Do uwierzytelniania usługi Azure Key Vault, potrzebujesz poświadczeń usługi Azure Active Directory (AD), certyfikatu lub wspólny klucz tajny. Zarządzanie tych poświadczeń, może być trudne i jest kuszące zbioru poświadczeń w aplikacji, umieszczając je w plikach źródłowych lub konfiguracji.

`Microsoft.Azure.Services.AppAuthentication` Dla biblioteki .NET upraszcza ten problem. Do uwierzytelniania podczas tworzenia lokalnego używane poświadczenia dla deweloperów. Gdy rozwiązanie jest później wdrożone na platformie Azure, biblioteka przełącza się do poświadczeń aplikacji.  

Podczas tworzenia lokalnego przy użyciu poświadczeń dewelopera jest bezpieczniejsza, ponieważ nie trzeba utworzyć poświadczenia usługi Azure AD lub udostępnić poświadczeń między deweloperów.

`Microsoft.Azure.Services.AppAuthentication` Biblioteki zarządza uwierzytelnianiem automatycznie, co z kolei pozwala na skoncentrowanie się na swoje rozwiązanie, a nie poświadczeń.

`Microsoft.Azure.Services.AppAuthentication` Biblioteka obsługuje lokalny rozwój za pomocą programu Microsoft Visual Studio, wiersza polecenia platformy Azure lub zintegrowane uwierzytelnianie usługi Azure AD. W przypadku wdrożenia usługi Azure App Service lub maszyny wirtualnej (maszyny Wirtualnej platformy Azure), biblioteka automatycznie używa [zarządzanych tożsamości dla usług platformy Azure](/azure/active-directory/msi-overview). Nie kodu lub zmiany konfiguracji są wymagane. Biblioteka obsługuje również bezpośredniemu wykorzystaniu usługi Azure AD [poświadczeń klienta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) gdy tożsamość zarządzana nie jest dostępna lub nie można ustalić kontekstu zabezpieczeń dla deweloperów podczas tworzenia lokalnego.

<a name="asal"></a>
## <a name="using-the-library"></a>Za pomocą biblioteki

Dla aplikacji .NET jest najprostszym sposobem, aby pracować z tożsamości zarządzanej `Microsoft.Azure.Services.AppAuthentication` pakietu. Oto jak rozpocząć pracę:

1. Dodaj odwołanie do [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) pakiet NuGet do aplikacji.

2. Dodaj następujący kod:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` Klasa przechowuje w pamięci podręcznej tokenu w pamięci i przywraca je z usługi Azure AD tylko przed jego wygaśnięciem. W związku z tym, nie musisz już do sprawdzenia wygaśnięcia przed wywołaniem `GetAccessTokenAsync` metody. Po prostu Wywołaj metodę, gdy użytkownik chce użyć tokenu. 

`GetAccessTokenAsync` Metoda wymaga identyfikatora zasobu. Aby dowiedzieć się więcej, zobacz [którymi usługami platformy Azure obsługują zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Przykłady

Następujące przykłady show `Microsoft.Azure.Services.AppAuthentication` biblioteki w akcji:

1. [Korzystania z tożsamości zarządzanej można pobrać klucza tajnego z usługi Azure Key Vault w czasie wykonywania](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programowe wdrażanie szablonu usługi Azure Resource Manager z maszyny Wirtualnej platformy Azure za pomocą tożsamości zarządzanej](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Wywoływanie usług platformy Azure z maszyny Wirtualnej systemu Linux na platformie Azure za pomocą platformy .NET Core próbki i tożsamość zarządzaną](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Rozwoju lokalnego uwierzytelniania

Dla wdrożenia lokalnego istnieją dwa scenariusze uwierzytelniania podstawowego:

- [Uwierzytelniania do usług platformy Azure](#authenticating-to-azure-services)
- [Uwierzytelnianie na niestandardowych usług](#authenticating-to-custom-services)

W tym miejscu znajdziesz informacje dotyczące każdego scenariusza i obsługiwanych narzędzi.


### <a name="authenticating-to-azure-services"></a>Uwierzytelniania do usług platformy Azure

Lokalne maszyny nie obsługują zarządzanych tożsamości dla zasobów platformy Azure.  W rezultacie `Microsoft.Azure.Services.AppAuthentication` biblioteki wykorzystuje swoje poświadczenia dla deweloperów do uruchamiania w swojego lokalnego środowiska deweloperskiego. Po wdrożeniu rozwiązania na platformie Azure biblioteka używa tożsamości zarządzanej, aby przełączyć się do przepływie przyznawania poświadczeń klienta OAuth 2.0.  Oznacza to, że możesz przetestować ten sam kod lokalnie i zdalnie bez obaw.

Na potrzeby lokalnego programowania dla `AzureServiceTokenProvider` pobiera tokeny przy użyciu **programu Visual Studio**, **interfejsu wiersza polecenia platformy Azure** (CLI) lub **zintegrowane uwierzytelnianie usługi Azure AD**. Każda opcja zostanie podjęta próba sekwencyjnie i biblioteka używa pierwszej opcji, które zakończyło się pomyślnie. Jeśli opcja nie działa, `AzureServiceTokenProviderException` wyjątek wraz ze szczegółowymi informacjami.

### <a name="authenticating-with-visual-studio"></a>Uwierzytelnianie za pomocą programu Visual Studio

Aby korzystać z programu Visual Studio, należy zweryfikować:

1. Po zainstalowaniu [v15.5 programu Visual Studio 2017](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) lub nowszej.

2. [Rozszerzenie do uwierzytelniania aplikacji dla programu Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) jest zainstalowany.
 
3. Logowanie do programu Visual Studio i wybrano konto będzie używane dla rozwoju lokalnego. Użyj **narzędzia**&nbsp;>&nbsp;**opcje**&nbsp;>&nbsp;**uwierzytelnianie usługi platformy Azure**wybrać konto rozwoju lokalnego. 

Jeśli napotkasz problemy przy użyciu programu Visual Studio, takie jak błędy dotyczące pliku dostawcę tokenów, należy dokładnie przejrzeć następujące kroki. 

Również może być konieczne ponowne uwierzytelnianie tokenu programu dla deweloperów.  Aby to zrobić, przejdź do **narzędzia**&nbsp;>&nbsp;**opcje**>**Azure&nbsp;usługi&nbsp;uwierzytelniania**  i poszukaj **ponowne uwierzytelnienie** łącze w ramach wybranego konta.  Wybierz go do uwierzytelniania. 

### <a name="authenticating-with-azure-cli"></a>Uwierzytelnianie przy użyciu wiersza polecenia platformy Azure

Aby użyć wiersza polecenia platformy Azure dla wdrożenia lokalnego:

1. Zainstaluj [v2.0.12 wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub nowszej. Uaktualnianie wcześniejszych wersji. 

2. Użyj **az login** zalogować się do platformy Azure.

Użyj `az account get-access-token` Aby zweryfikować dostęp.  Jeśli otrzymasz komunikat o błędzie, sprawdź, czy krok 1 zakończyła się pomyślnie. 

Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure do domyślnego katalogu, może wystąpić błąd raporty `AzureServiceTokenProvider` nie można odnaleźć ścieżki dla wiersza polecenia platformy Azure.  Użyj **AzureCLIPath**zmiennej środowiskowej, aby określić folder instalacji wiersza polecenia platformy Azure. `AzureServiceTokenProvider` dodaje katalogu wskazanym na **AzureCLIPath** zmiennej środowiskowej, aby **ścieżki** zmiennej środowiskowej, gdy jest to konieczne.

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

- Usługi active directory w środowisku lokalnym [synchronizacje z usługą Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

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

<a name="msi"></a>
## <a name="running-the-application-using-managed-identity"></a>Uruchamianie aplikacji za pomocą tożsamości zarządzanych 

Gdy uruchamiasz swój kod w usłudze Azure App Service lub Maszynie wirtualnej platformy Azure za pomocą tożsamości zarządzanej włączono biblioteki automatycznie używa tożsamość zarządzaną. Wymagane są bez zmian w kodzie. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Uruchamianie aplikacji przy użyciu nazwy głównej usługi 

Może być konieczne utworzenie poświadczeń usługi Azure AD klienta do uwierzytelniania. Typowe przykłady obejmują:

1. Kod jest wykonywany na lokalne Środowisko deweloperskie, ale nie w ramach tożsamości dla deweloperów.  Usługa Service Fabric, na przykład używa [konto NetworkService](/azure/service-fabric/service-fabric-application-secret-management) dla wdrożenia lokalnego.
 
2. Kod jest wykonywany na lokalne Środowisko deweloperskie i uwierzytelniania użytkownika to usługa niestandardowa, więc nie możesz użyć swojej tożsamości dla deweloperów. 
 
3. Twój kod jest uruchomiony na zasób obliczeniowy systemu Azure, która nie obsługuje jeszcze zarządzanych tożsamości dla zasobów platformy Azure, takich jak Azure Batch.

Do korzystania z certyfikatu, aby zalogować się do usługi Azure AD:

1. Tworzenie [certyfikatu nazwy głównej usługi](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Wdrażanie certyfikatu do jednej *LocalMachine* lub *CurrentUser* przechowywania. 

3. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** do:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Zastąp *{AppId}*, *{identyfikator dzierżawy}*, i *{Thumbprint}* wartościami utworzoną w kroku 1. Zastąp *{CertificateStore}* z oboma `LocalMachine` lub `CurrentUser`zgodnie z planu wdrożenia usługi.

4. Uruchom aplikację. 

Aby zalogować się przy użyciu usługi Azure AD udostępnione wpisu tajnego poświadczeń:

1. Tworzenie [jednostki usługi przy użyciu hasła](/azure/azure-resource-manager/resource-group-authenticate-service-principal) i przyznać jej dostęp do usługi Key Vault. 

2. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** do:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Zastąp _{AppId}_, _{identyfikator dzierżawy}_, i _{ClientSecret}_ wartościami utworzoną w kroku 1.

3. Uruchom aplikację. 

Gdy wszystko jest skonfigurowane prawidłowo, żadne dalsze zmiany kodu są niezbędne.  `AzureServiceTokenProvider` używa zmiennej środowiskowej i certyfikat do uwierzytelniania usługi Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Obsługa ciągu połączenia

Domyślnie `AzureServiceTokenProvider` korzysta na wiele sposobów pobierania tokenu. 

Aby kontrolować ten proces, należy użyć ciągu połączenia przekazany do `AzureServiceTokenProvider` konstruktora lub został określony w *AzureServicesAuthConnectionString* zmiennej środowiskowej. 

Obsługiwane są następujące opcje:

| Połączenie&nbsp;ciąg&nbsp;opcji | Scenariusz | Komentarze|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokalne programowanie | AzureServiceTokenProvider używa codziennych w celu uzyskania tokenu. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokalne programowanie | AzureServiceTokenProvider używa programu Visual Studio w celu uzyskania tokenu. |
| `RunAs=CurrentUser;` | Lokalne programowanie | AzureServiceTokenProvider wykorzystuje zintegrowane uwierzytelnianie usługi Azure AD do pobrania tokenu. |
| `RunAs=App;` | zarządzanych tożsamości dla zasobów platformy Azure | AzureServiceTokenProvider korzysta z tożsamości zarządzanej można uzyskać tokenu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Jednostka usługi | `AzureServiceTokenProvider` używa certyfikatu, aby uzyskać token z usługi Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Jednostka usługi | `AzureServiceTokenProvider` Aby uzyskać token z usługi Azure AD, używa certyfikatu|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Jednostka usługi |`AzureServiceTokenProvider` używa tajnego klucza, aby uzyskać token z usługi Azure AD. |


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zarządzanych tożsamości dla zasobów platformy Azure](/azure/app-service/app-service-managed-service-identity).

- Informacje o różnych sposobach [uwierzytelnianie i autoryzowanie aplikacji](/azure/app-service/app-service-authentication-overview).

- Dowiedz się więcej o usłudze Azure AD [scenariusze uwierzytelniania](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).
