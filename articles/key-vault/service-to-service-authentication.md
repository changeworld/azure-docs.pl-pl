---
title: Do usługi uwierzytelniania usługi Azure Key Vault przy użyciu platformy .NET
description: Biblioteka Microsoft.Azure.Services.AppAuthentication służy do uwierzytelniania usługi Azure Key Vault przy użyciu platformy .NET.
keywords: lokalne poświadczenia uwierzytelniania magazyn kluczy Azure
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: db0b0ca72f41c68e19db6635d9ba0e9144183204
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31417579"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Do usługi uwierzytelniania usługi Azure Key Vault przy użyciu platformy .NET

Do uwierzytelniania usługi Azure Key Vault, potrzebne są poświadczenia usługi Azure Active Directory (AD), Wspólny klucz tajny lub certyfikatu. Zarządzanie tych poświadczeń może być trudne i jest kuszące pakietów poświadczeń w aplikacji, umieszczając je w plikach źródłowym lub konfiguracji.

`Microsoft.Azure.Services.AppAuthentication` Dla biblioteki .NET upraszcza ten problem. Używa dewelopera poświadczeń do uwierzytelniania podczas tworzenia lokalnego. Podczas późniejszego wdrażania rozwiązania na platformie Azure, biblioteki jest automatycznie zmienia poświadczenia aplikacji.  

Przy użyciu poświadczeń developer podczas tworzenia lokalnego jest bezpieczniejsza, ponieważ nie trzeba utworzyć poświadczenia usługi Azure AD lub udostępnić poświadczeń między deweloperów.

`Microsoft.Azure.Services.AppAuthentication` Biblioteki zarządza uwierzytelniania automatycznie, co z kolei pozwala skupić się na rozwiązaniu, a nie poświadczenia.

`Microsoft.Azure.Services.AppAuthentication` Biblioteki obsługuje tworzenie lokalnych za pomocą programu Microsoft Visual Studio, Azure CLI lub zintegrowane uwierzytelnianie usługi Azure AD. Po wdrożeniu usługi aplikacji Azure lub maszyny wirtualnej Azure (VM), biblioteka automatycznie używa [zarządzane tożsamość usługi](/azure/active-directory/msi-overview) (MSI). Zmiany nie kod lub konfiguracji są wymagane. Biblioteka obsługuje również bezpośredniego użycia usługi Azure AD [poświadczeń klienta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) gdy MSI jest niedostępny lub gdy dewelopera kontekstu zabezpieczeń nie można określić podczas tworzenia lokalnego.

<a name="asal"></a>
## <a name="using-the-library"></a>Za pomocą biblioteki

W przypadku aplikacji .NET Najprostszym sposobem, aby pracować z zarządzania usługi tożsamości (MSI) jest za pośrednictwem `Microsoft.Azure.Services.AppAuthentication` pakietu. Oto jak rozpocząć pracę:

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

`AzureServiceTokenProvider` Klasy buforuje token w pamięci i przywraca je z usługi Azure AD tylko przed jego wygaśnięciem. W rezultacie nie można już do sprawdzenia wygaśnięcia przed wywołaniem `GetAccessTokenAsync` metody. Po prostu Wywołaj metodę umożliwia korzystanie z tokenu. 

`GetAccessTokenAsync` Metoda wymaga identyfikatorem zasobu. Aby dowiedzieć się więcej, zobacz [usług Azure która obsługuje zarządzane tożsamość usługi](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Przykłady

Następujące przykłady Pokaż `Microsoft.Azure.Services.AppAuthentication` biblioteki w akcji:

1. [Pobierz klucz tajny z usługi Azure Key Vault w czasie wykonywania za pomocą zarządzanych tożsamości usługi (MSI)](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programowe wdrażanie szablonu usługi Azure Resource Manager z maszyny Wirtualnej platformy Azure z Instalatora MSI](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Wywoływanie usług platformy Azure z maszyny Wirtualnej systemu Linux na platformie Azure za pomocą przykładowej .NET Core i MSI](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Lokalne działania projektowe uwierzytelniania

Dla wdrożenia lokalnego istnieją dwa scenariusze uwierzytelniania podstawowego:

- [Uwierzytelniania do usług platformy Azure](#authenticating-to-azure-services)
- [Do niestandardowej usługi uwierzytelniania](#authenticating-to-custom-services)

W tym miejscu możesz dowiedzieć się, wymagania dotyczące każdego scenariusza i obsługiwanych narzędzi.


### <a name="authenticating-to-azure-services"></a>Uwierzytelniania do usług platformy Azure

Lokalne maszyny nie obsługują zarządzane tożsamości usługi (MSI).  W związku z tym `Microsoft.Azure.Services.AppAuthentication` biblioteki używa poświadczeń developer do uruchomienia w środowisku projektowania lokalnego. Po wdrożeniu rozwiązania na platformie Azure biblioteka używa MSI, aby przełączyć się do przepływu przyznania poświadczeń klienta OAuth 2.0.  Oznacza to, że można przetestować tego samego kodu lokalnie i zdalnie bez obaw.

Dla wdrożenia lokalnego `AzureServiceTokenProvider` pobiera tokeny przy użyciu **programu Visual Studio**, **interfejsu wiersza polecenia platformy Azure** (CLI) lub **zintegrowane uwierzytelnianie usługi Azure AD**. Kolejno sprawdzane są poszczególne opcje, a biblioteka używa pierwsza opcja, które zakończyło się pomyślnie. Jeśli opcja nie działa, `AzureServiceTokenProviderException` wyjątek szczegółowe informacje.

### <a name="authenticating-with-visual-studio"></a>Uwierzytelnianie za pomocą programu Visual Studio

Aby użyć programu Visual Studio, sprawdź:

1. Po zainstalowaniu [v15.5 programu Visual Studio 2017](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) lub nowszym.

2. [Rozszerzenie uwierzytelniania aplikacji dla programu Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) jest zainstalowany.
 
3. Zarejestrowany w programie Visual Studio i wybrano konto będzie używane dla rozwoju lokalnych. Użyj **narzędzia**&nbsp;>&nbsp;**opcje**&nbsp;>&nbsp;**uwierzytelniania usługi Azure**wybrać konto lokalne działania projektowe. 

Jeśli wystąpiły problemy przy użyciu programu Visual Studio, takich jak błędy dotyczące pliku dostawcę tokenów, należy dokładnie przejrzeć następujące kroki. 

Również może być konieczne ponowne uwierzytelnianie developer token.  Aby to zrobić, przejdź do **narzędzia**&nbsp;>&nbsp;**opcje**>**Azure&nbsp;usługi&nbsp;uwierzytelniania**  i poszukaj **ponownego uwierzytelnienia** łącza w ramach wybranego konta.  Wybierz go do uwierzytelniania. 

### <a name="authenticating-with-azure-cli"></a>Uwierzytelnianiu przy użyciu interfejsu wiersza polecenia platformy Azure

Aby użyć wiersza polecenia platformy Azure dla rozwoju lokalnych:

1. Zainstaluj [v2.0.12 interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli) lub nowszym. Uaktualnienia wcześniejszych wersji. 

2. Użyj **logowania az** logować się do platformy Azure.

Użyj `az account get-access-token` Aby zweryfikować dostęp.  Jeśli wystąpi błąd, sprawdź, czy krok 1 zakończyła się pomyślnie. 

Jeśli nie zainstalowano wiersza polecenia platformy Azure do domyślnego katalogu, może zostać wyświetlony błąd informujący, że `AzureServiceTokenProvider` nie można odnaleźć ścieżki dla wiersza polecenia platformy Azure.  Użyj **AzureCLIPath**zmiennej środowiskowej, aby zdefiniować folder instalacyjny wiersza polecenia platformy Azure. `AzureServiceTokenProvider` Dodaje określony w katalogu **AzureCLIPath** zmienną środowiskową **ścieżki** zmiennej środowiskowej, gdy jest to konieczne.

Jeśli użytkownik jest zalogowany do wiersza polecenia platformy Azure przy użyciu wielu kont lub konto użytkownika ma dostęp do wielu subskrypcji, należy określić określonej subskrypcji do użycia.  Aby to zrobić, należy użyć:

```
az account set --subscription <subscription-id>
```

To polecenie generuje dane wyjściowe tylko w przypadku awarii.  Aby sprawdzić ustawienia bieżącego konta, należy użyć:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Uwierzytelnianie przy użyciu uwierzytelniania usługi Azure AD integracji

Aby używać uwierzytelniania usługi Azure AD, sprawdź, czy:

- Lokalne usługi active directory [jest synchronizowane z usługą Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- Kod jest uruchomiony na komputerze przyłączonym do domeny.


### <a name="authenticating-to-custom-services"></a>Do niestandardowej usługi uwierzytelniania

Podczas wywołania usługi usług platformy Azure, poprzednie kroki działać, ponieważ usług Azure zezwolić na dostęp do użytkowników i aplikacje.  

Podczas tworzenia usługi, która wywołuje usługę niestandardowe, należy użyć poświadczeń klienta usługi Azure AD do rozwoju lokalnego uwierzytelniania.  Dostępne są dwie opcje: 

1.  Aby zalogować się do usługi Azure, użyj nazwy głównej usługi:

    1.  [Tworzenie nazwy głównej usługi](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Zaloguj się za pomocą wiersza polecenia platformy Azure:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Ponieważ nazwy głównej usługi nie może mieć dostęp do subskrypcji, użyj `--allow-no-subscriptions` argumentu.

2.  Zmienne środowiskowe umożliwia określenie szczegółów główną usługi.  Aby uzyskać więcej informacji, zobacz [uruchamiania aplikacji przy użyciu nazwy głównej usługi](#running-the-application-using-a-service-principal).

Po zarejestrowaniu się na platformie Azure, `AzureServiceTokenProvider` używa nazwy głównej usługi w celu pobrania tokenu dla rozwoju lokalnych.

Dotyczy to tylko wdrożenia lokalnego. Po wdrożeniu rozwiązania Azure biblioteki zmienia się na uwierzytelnianie MSI.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Uruchomienie aplikacji przy użyciu tożsamości usługi zarządzania 

Po uruchomieniu kodu w usłudze Azure App Service lub maszynie Wirtualnej platformy Azure z włączoną MSI Biblioteka automatycznie używa zarządzane tożsamości usługi. Zmiany kodu, nie są wymagane. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Uruchomienie aplikacji przy użyciu nazwy głównej usługi 

Może być konieczne utworzenie poświadczeń usługi Azure AD klienta do uwierzytelniania. Typowe przykłady obejmują:

1. Kod działa na lokalne Środowisko deweloperskie, ale nie w ramach tożsamości dewelopera.  Usługa sieci szkieletowej, na przykład używa [konta Usługa sieciowa](/azure/service-fabric/service-fabric-application-secret-management) dla rozwoju lokalnych.
 
2. Kod działa na lokalne Środowisko deweloperskie i uwierzytelniania użytkownika usług niestandardowych, więc nie można używać tożsamości developer. 
 
3. Kod jest uruchomiony na zasobie obliczeń platformy Azure, która nie obsługuje jeszcze zarządzane tożsamość usługi, takie jak partii zadań Azure.

Do korzystania z certyfikatu, aby zalogować się do usługi Azure AD:

1. Utwórz [certyfikat główny usługi](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Wdrażanie certyfikatu jednej _LocalMachine_ lub _CurrentUser_ przechowywania. 

3. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** do:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Zastąp _{AppId}_, _{TenantId}_, i _{Thumbprint}_ wartościami utworzoną w kroku 1.

    **CertificateStoreLocation** musi być równa albo _CurrentUser_ lub _LocalMachine_zgodnie z planem wdrożenia.

4. Uruchom aplikację. 

Aby zalogować się przy użyciu usługi Azure AD udostępnionej tajnego poświadczeń:

1. Utwórz [nazwy głównej usługi przy użyciu hasła](/azure/azure-resource-manager/resource-group-authenticate-service-principal) i przyznać jej dostęp do magazynu kluczy. 

2. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** do:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Zastąp _{AppId}_, _{TenantId}_, i _{ClientSecret}_ wartościami utworzoną w kroku 1.

3. Uruchom aplikację. 

Gdy wszystko zostało skonfigurowane prawidłowo, żadne dalsze zmiany kodu są konieczne.  `AzureServiceTokenProvider` używa zmiennej środowiskowej i certyfikat do uwierzytelniania usługi Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Obsługa ciągu połączenia

Domyślnie `AzureServiceTokenProvider` używa wielu metod do pobrania tokenu. 

Aby kontrolować proces, użyj parametrów połączenia przekazany do `AzureServiceTokenProvider` konstruktora lub został określony w *AzureServicesAuthConnectionString* zmiennej środowiskowej. 

Obsługiwane są następujące opcje:

| Połączenie&nbsp;ciąg&nbsp;opcji | Scenariusz | Komentarze|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokalne działania projektowe | AzureServiceTokenProvider używa AzureCli w celu uzyskania tokenu. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokalne działania projektowe | AzureServiceTokenProvider używa programu Visual Studio w celu uzyskania tokenu. |
| `RunAs=CurrentUser;` | Lokalne działania projektowe | AzureServiceTokenProvider używa zintegrowanego uwierzytelniania systemu Azure AD w celu uzyskania tokenu. |
| `RunAs=App;` | Tożsamość usługi zarządzanej | AzureServiceTokenProvider używa zarządzane tożsamości usługi w celu uzyskania tokenu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Jednostka usługi | `AzureServiceTokenProvider` używa certyfikatu, aby uzyskać token z usługi Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Jednostka usługi | `AzureServiceTokenProvider` używa certyfikatu w celu uzyskania tokenu z usługi Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Jednostka usługi |`AzureServiceTokenProvider` używa hasła w celu uzyskania tokenu z usługi Azure AD. |


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zarządzane tożsamość usługi](/azure/app-service/app-service-managed-service-identity).

- Informacje o różnych sposobach [uwierzytelniania i autoryzacji aplikacji](/azure/app-service/app-service-authentication-overview).

- Dowiedz się więcej o usłudze Azure AD [scenariusze uwierzytelniania](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).