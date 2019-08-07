---
title: Uwierzytelnianie między usługami Azure Key Vault przy użyciu platformy .NET
description: Użyj biblioteki Microsoft. Azure. Services. AppAuthentication do uwierzytelniania w celu Azure Key Vault przy użyciu platformy .NET.
keywords: poświadczenia lokalne uwierzytelniania magazynu kluczy Azure
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: d34c94ccca47d29afc4f3d83bec58db737be270c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840412"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Uwierzytelnianie między usługami Azure Key Vault przy użyciu platformy .NET

W celu uwierzytelnienia w Azure Key Vault jest potrzebne poświadczenie Azure Active Directory (AD), wspólny klucz tajny lub certyfikat. 

Zarządzanie takimi poświadczeniami może być trudne i zachęca do podzielenia poświadczeń do aplikacji przez uwzględnienie ich w plikach źródłowych lub konfiguracji.  Biblioteka `Microsoft.Azure.Services.AppAuthentication` for .NET upraszcza ten problem. Używa poświadczeń dewelopera do uwierzytelniania podczas lokalnego tworzenia oprogramowania. Gdy rozwiązanie zostanie wdrożone później na platformie Azure, biblioteka automatycznie przełączy się na poświadczenia aplikacji.    Korzystanie z poświadczeń dewelopera podczas lokalnego tworzenia jest bezpieczniejsze, ponieważ nie ma potrzeby tworzenia poświadczeń usługi Azure AD ani udostępniania poświadczeń między deweloperami.

`Microsoft.Azure.Services.AppAuthentication` Biblioteka zarządza uwierzytelnianiem automatycznie, co z kolei umożliwia skoncentrowanie się na rozwiązaniu, a nie na poświadczeniach.  Obsługuje ona lokalne Programowanie przy użyciu Microsoft Visual Studio, interfejsu wiersza polecenia platformy Azure lub zintegrowanego uwierzytelniania usługi Azure AD. W przypadku wdrożenia do zasobu platformy Azure, który obsługuje tożsamość zarządzaną, biblioteka automatycznie używa [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/msi-overview.md). Nie są wymagane żadne zmiany kodu ani konfiguracji. Biblioteka obsługuje również bezpośrednie użycie [poświadczeń klienta](../azure-resource-manager/resource-group-authenticate-service-principal.md) usługi Azure AD, gdy zarządzana tożsamość jest niedostępna, lub jeśli nie można określić kontekstu zabezpieczeń dewelopera podczas lokalnego tworzenia.

## <a name="using-the-library"></a>Korzystanie z biblioteki

W przypadku aplikacji .NET Najprostszym sposobem pracy z zarządzaną tożsamością jest `Microsoft.Azure.Services.AppAuthentication` pakiet. Oto jak rozpocząć pracę:

1. Dodaj odwołania do pakietów NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) 

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

`AzureServiceTokenProvider` Klasa buforuje token w pamięci i pobiera go z usługi Azure AD tuż przed wygaśnięciem. W związku z tym nie trzeba już sprawdzać wygaśnięcia przed wywołaniem `GetAccessTokenAsync` metody. Po prostu wywołaj metodę, aby użyć tokenu. 

`GetAccessTokenAsync` Metoda wymaga identyfikatora zasobu. Aby dowiedzieć się więcej, zobacz, [które usługi platformy Azure obsługują tożsamości zarządzane dla zasobów platformy Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Lokalne uwierzytelnianie programistyczne

W przypadku lokalnego projektowania istnieją dwa podstawowe scenariusze uwierzytelniania: [uwierzytelnianie w usługach platformy Azure](#authenticating-to-azure-services)i [uwierzytelnianie w usługach niestandardowych](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Uwierzytelnianie w usługach platformy Azure

Maszyny lokalne nie obsługują tożsamości zarządzanych dla zasobów platformy Azure.  W związku `Microsoft.Azure.Services.AppAuthentication` z tym Biblioteka używa poświadczeń dewelopera do uruchamiania w lokalnym środowisku programistycznym. Gdy rozwiązanie zostanie wdrożone na platformie Azure, biblioteka używa zarządzanej tożsamości w celu przełączenia się do przepływu przydzielenia poświadczeń klienta OAuth 2,0.  Oznacza to, że można testować ten sam kod lokalnie i zdalnie bez obaw.

W przypadku lokalnego programowania `AzureServiceTokenProvider` program pobiera tokeny przy użyciu **programu Visual Studio**, **interfejsu wiersza polecenia platformy Azure** lub **zintegrowanego uwierzytelniania usługi Azure AD**. Każda opcja jest podejmowana sekwencyjnie, a Biblioteka używa pierwszej opcji, która się powiedzie. Jeśli żadna opcja nie działa, `AzureServiceTokenProviderException` zostanie zgłoszony wyjątek ze szczegółowymi informacjami.

### <a name="authenticating-with-visual-studio"></a>Uwierzytelnianie za pomocą programu Visual Studio

Uwierzytelnianie za pomocą programu Visual Studio ma następujące wymagania wstępne:

1. [Program Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) lub nowszy.

2. [Rozszerzenie uwierzytelniania aplikacji dla programu Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), dostępne jako osobne rozszerzenie programu visual Studio 2017 Update 5 i powiązane z produktem w Update 6 lub nowszym. W przypadku aktualizacji Update 6 lub nowszej można zweryfikować instalację rozszerzenia uwierzytelnianie aplikacji, wybierając pozycję Narzędzia deweloperskie platformy Azure z poziomu Instalatora programu Visual Studio.
 
Zaloguj się do programu Visual Studio i&nbsp;Użyj**opcji**&nbsp;>&nbsp;narzędzia>uwierzytelnianie**usługi platformy Azure** , aby wybrać konto do lokalnego tworzenia.&nbsp; 

Jeśli wystąpią problemy z programem Visual Studio, np. błędy dotyczące pliku dostawcy tokenów, należy dokładnie zapoznać się z tymi krokami. 

Może być również konieczne ponowne uwierzytelnienie tokenu dewelopera. W tym celu przejdź do pozycji **Narzędzia**&nbsp;>&nbsp;**Opcje**>**uwierzytelnianieusługi&nbsp;AzureiWyszukajlinkponowneuwierzytelnianiewobszarze&nbsp;** wybrane koncie.  Wybierz go do uwierzytelnienia. 

### <a name="authenticating-with-azure-cli"></a>Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure

Aby użyć interfejsu wiersza polecenia platformy Azure do lokalnego tworzenia:

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.12 lub nowszej. Uaktualnij wcześniejsze wersje. 

2. Użyj **AZ login** , aby zalogować się do platformy Azure.

Służy `az account get-access-token` do weryfikowania dostępu.  Jeśli wystąpi błąd, sprawdź, czy krok 1 zakończył się pomyślnie. 

Jeśli interfejs wiersza polecenia platformy Azure nie jest zainstalowany w katalogu domyślnym, może zostać wyświetlony komunikat o `AzureServiceTokenProvider` błędzie, który nie może znaleźć ścieżki do interfejsu wiersza polecenia platformy Azure.  Użyj zmiennej środowiskowej **AzureCLIPath** , aby zdefiniować folder instalacyjny interfejsu wiersza polecenia platformy Azure. `AzureServiceTokenProvider`w razie potrzeby dodaje katalog określony w zmiennej środowiskowej **AzureCLIPath** do zmiennej środowiskowej **Path** .

Jeśli logujesz się do interfejsu wiersza polecenia platformy Azure przy użyciu wielu kont lub Twoje konto ma dostęp do wielu subskrypcji, musisz określić określoną subskrypcję, która ma zostać użyta.  W tym celu należy użyć:

```
az account set --subscription <subscription-id>
```

To polecenie generuje dane wyjściowe tylko w przypadku niepowodzenia.  Aby sprawdzić bieżące ustawienia konta, użyj:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>Uwierzytelnianie przy użyciu uwierzytelniania usługi Azure AD

Aby użyć uwierzytelniania usługi Azure AD, sprawdź, czy:

- Lokalna usługa Active Directory jest [synchronizowana z usługą Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- Twój kod jest uruchomiony na komputerze przyłączonym do domeny.


### <a name="authenticating-to-custom-services"></a>Uwierzytelnianie w usługach niestandardowych

Gdy usługa wywołuje usługi platformy Azure, poprzednie kroki działają, ponieważ usługi platformy Azure umożliwiają dostęp zarówno do użytkowników, jak i aplikacji.  

Podczas tworzenia usługi, która wywołuje usługę niestandardową, Użyj poświadczeń klienta usługi Azure AD na potrzeby lokalnego uwierzytelniania na potrzeby tworzenia oprogramowania.  Dostępne są dwie opcje: 

1.  Zaloguj się do platformy Azure przy użyciu nazwy głównej usługi:

    1.  [Utwórz nazwę główną usługi](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Ponieważ jednostka usługi może nie mieć dostępu do subskrypcji, użyj `--allow-no-subscriptions` argumentu.

2.  Użyj zmiennych środowiskowych, aby określić szczegóły jednostki usługi.  Aby uzyskać szczegółowe informacje, zobacz [Uruchamianie aplikacji przy użyciu nazwy głównej usługi](#running-the-application-using-a-service-principal).

Po zalogowaniu się do platformy Azure program `AzureServiceTokenProvider` korzysta z jednostki usługi w celu pobrania tokenu na potrzeby lokalnego tworzenia oprogramowania.

Dotyczy to tylko lokalnego projektowania. Gdy Twoje rozwiązanie zostanie wdrożone na platformie Azure, biblioteka przełączy się do zarządzanej tożsamości w celu uwierzytelnienia.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Uruchamianie aplikacji przy użyciu tożsamości zarządzanej lub przypisanej przez użytkownika 

Gdy uruchamiasz swój kod na Azure App Service lub na maszynie wirtualnej platformy Azure z włączoną tożsamością zarządzaną, biblioteka automatycznie używa zarządzanej tożsamości. 

Alternatywnie można uwierzytelniać się przy użyciu tożsamości przypisanej do użytkownika. Aby uzyskać więcej informacji na temat tożsamości przypisanych przez użytkownika, zobacz [Informacje o zarządzanych tożsamościach dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Aby uwierzytelnić się za pomocą tożsamości przypisanej do użytkownika, należy określić identyfikator klienta tożsamości przypisanej do użytkownika w parametrach połączenia. Parametry połączenia są określone w poniższej sekcji [Obsługa parametrów połączenia](#connection-string-support) .

## <a name="running-the-application-using-a-service-principal"></a>Uruchamianie aplikacji przy użyciu nazwy głównej usługi 

Może być konieczne utworzenie poświadczeń klienta usługi Azure AD w celu uwierzytelnienia. Typowe przykłady obejmują:

- Kod jest uruchamiany w lokalnym środowisku programistycznym, ale nie w ramach tożsamości dewelopera.  Service Fabric, na przykład używa [konta NetworkService](../service-fabric/service-fabric-application-secret-management.md) do lokalnego tworzenia.
 
- Kod jest uruchamiany w lokalnym środowisku programistycznym i jest uwierzytelniany w usłudze niestandardowej, dlatego nie można używać tożsamości dewelopera. 
 
- Kod jest uruchamiany w zasobie obliczeniowym platformy Azure, który nie obsługuje jeszcze zarządzanych tożsamości dla zasobów platformy Azure, takich jak Azure Batch.

Istnieją trzy podstawowe metody używania nazwy głównej usługi do uruchamiania aplikacji. Aby użyć dowolnego z nich, należy najpierw [utworzyć nazwę główną usługi](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Logowanie do usługi Azure AD przy użyciu certyfikatu w lokalnym magazynie kluczy

1. Utwórz certyfikat główny usługi przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) . 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Spowoduje to utworzenie pliku PEM (klucza prywatnego), który będzie przechowywany w katalogu macierzystym. Wdróż ten certyfikat w magazynie *LocalMachine* lub *CurrentUser* . 

    > [!Important]
    > Polecenie interfejsu wiersza polecenia generuje plik PEM, ale system Windows zapewnia natywną obsługę certyfikatów PFX. Aby zamiast tego wygenerować certyfikat PFX, Użyj poleceń programu PowerShell przedstawionych tutaj: [Utwórz nazwę główną usługi z certyfikatem z podpisem własnym](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Te polecenia powodują również automatyczne wdrożenie certyfikatu.

1. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Zastąp *{AppID}* , *{TenantId}* i *{odcisk palca}* wartościami wygenerowanymi w kroku 1. Zastąp *{CertificateStore}* wartością `LocalMachine` lub `CurrentUser`, w zależności od planu wdrożenia.

1. Uruchom aplikację. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Zaloguj się do usługi Azure AD przy użyciu poświadczeń wspólnego klucza tajnego

1. Utwórz certyfikat jednostki usługi za pomocą hasła za pomocą polecenia [AZ AD Sp Create-for-RBAC--Password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). 

2. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Zastąp zmienne _{AppID}_ , _{TenantId}_ i _{ClientSecret}_ wartościami wygenerowanymi w kroku 1.

3. Uruchom aplikację. 

Po poprawnym skonfigurowaniu wszystkiego nie są wymagane żadne dalsze zmiany w kodzie.  `AzureServiceTokenProvider`używa zmiennej środowiskowej i certyfikatu do uwierzytelniania w usłudze Azure AD. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Logowanie do usługi Azure AD za pomocą certyfikatu w Key Vault

Ta opcja umożliwia przechowywanie certyfikatu klienta jednostki usługi w Key Vault i używanie go na potrzeby uwierzytelniania nazwy głównej usługi. Można jej użyć w następujących scenariuszach:

* Uwierzytelnianie lokalne, w którym chcesz przeprowadzić uwierzytelnianie przy użyciu jawnej jednostki usługi, i chcę bezpiecznie zachować poświadczenia jednostki usługi w magazynie kluczy. Konto dewelopera musi mieć dostęp do magazynu kluczy. 
* Uwierzytelnianie z platformy Azure, w którym ma być używane jawne poświadczenie (np. scenariusze obejmujące wiele dzierżawców), i chcesz bezpiecznie zachować poświadczenia jednostki usługi w magazynie kluczy. Tożsamość zarządzana musi mieć dostęp do magazynu kluczy. 

Zarządzana tożsamość lub tożsamość dewelopera musi mieć uprawnienia do pobierania certyfikatu klienta z Key Vault. Biblioteka AppAuthentication używa pobranego certyfikatu jako poświadczenia klienta jednostki usługi.

Aby użyć certyfikatu klienta do uwierzytelniania jednostki usługi

1. Utwórz certyfikat główny usługi i automatycznie Zapisz go w magazynie kluczy przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for- <keyvaultname> RBAC--Certificate Certificate--CERT <certificatename> --Create-CERT---](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    Identyfikator certyfikatu będzie adresem URL w formacie`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Zastąp `{KeyVaultCertificateSecretIdentifier}` wartość w tym parametrach połączenia identyfikatorem certyfikatu:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Jeśli na przykład wystąpienie Twojego magazynu kluczy zostało wywołane "myKeyVault" i został utworzony certyfikat o nazwie "Moje certyfikaty", identyfikator certyfikatu będzie:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```


## <a name="connection-string-support"></a>Obsługa parametrów połączenia

Domyślnie program `AzureServiceTokenProvider` używa wielu metod do pobrania tokenu. 

Aby kontrolować proces, należy użyć parametrów połączenia przekazaną do `AzureServiceTokenProvider` konstruktora lub określonych w zmiennej środowiskowej *AzureServicesAuthConnectionString* . 

Obsługiwane są następujące opcje:

| Opcja parametrów połączenia | Scenariusz | Komentarze|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Programowanie lokalne | AzureServiceTokenProvider używa AzureCli do uzyskiwania tokenu. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Programowanie lokalne | AzureServiceTokenProvider używa programu Visual Studio, aby uzyskać token. |
| `RunAs=CurrentUser` | Programowanie lokalne | AzureServiceTokenProvider używa zintegrowanego uwierzytelniania usługi Azure AD w celu uzyskania tokenu. |
| `RunAs=App` | [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider używa tożsamości zarządzanej do uzyskiwania tokenu. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Tożsamość przypisana przez użytkownika dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider używa tożsamości przypisanej do użytkownika w celu uzyskania tokenu. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Niestandardowe uwierzytelnianie usług | KeyVaultCertificateSecretIdentifier = identyfikator wpisu tajnego certyfikatu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Jednostka usługi | `AzureServiceTokenProvider`używa certyfikatu w celu pobrania tokenu z usługi Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Jednostka usługi | `AzureServiceTokenProvider`Pobiera token z usługi Azure AD przy użyciu certyfikatu|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Jednostka usługi |`AzureServiceTokenProvider`Pobiera token z usługi Azure AD przy użyciu klucza tajnego. |

## <a name="samples"></a>Przykłady

Aby wyświetlić `Microsoft.Azure.Services.AppAuthentication` bibliotekę w działaniu, zapoznaj się z poniższymi przykładami kodu.

1. [Używanie tożsamości zarządzanej do pobierania klucza tajnego z Azure Key Vault w czasie wykonywania](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programistycznie Wdróż szablon Azure Resource Manager z maszyny wirtualnej platformy Azure z zarządzaną tożsamością](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Użyj przykładu .NET Core i zarządzanej tożsamości, aby wywoływać usługi platformy Azure z maszyny wirtualnej z systemem Linux na platformie Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Rozwiązywanie problemów z AppAuthentication

### <a name="common-issues-during-local-development"></a>Typowe problemy występujące podczas lokalnego tworzenia oprogramowania

#### <a name="azure-cli-is-not-installed-you-are-not-logged-in-or-you-do-not-have-the-latest-version"></a>Nie zainstalowano interfejsu wiersza polecenia platformy Azure, użytkownik nie jest zalogowany lub nie masz najnowszej wersji

Uruchom **AZ Account Get-Access-token** , aby sprawdzić, czy w interfejsie wiersza polecenia platformy Azure jest wyświetlany token. Jeśli komunikat nie zostanie znaleziony, zainstaluj [najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Jeśli zainstalowano go, może zostać wyświetlony monit o zalogowanie się. 
 
#### <a name="azureservicetokenprovider-cannot-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider nie może odnaleźć ścieżki dla interfejsu wiersza polecenia platformy Azure

AzureServiceTokenProvider szuka interfejsu wiersza polecenia platformy Azure w domyślnej lokalizacji instalacji. Jeśli nie można znaleźć interfejsu wiersza polecenia platformy Azure, Ustaw zmienną środowiskową **AzureCLIPath** na folder instalacyjny interfejsu wiersza polecenia platformy Azure. AzureServiceTokenProvider doda zmienną środowiskową do zmiennej środowiskowej PATH.
 
#### <a name="you-are-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Logujesz się do interfejsu wiersza polecenia platformy Azure przy użyciu wielu kont, to samo konto ma dostęp do subskrypcji w wielu dzierżawach lub otrzymujesz błąd odmowy dostępu podczas próby wywołania podczas lokalnego tworzenia

Korzystając z interfejsu wiersza polecenia platformy Azure, Ustaw domyślną subskrypcję na taką, która ma używane konto, i znajduje się w tej samej dzierżawie co zasób, do którego chcesz uzyskać dostęp: **AZ Account Set--Subscription [Subscription-ID]** . Jeśli dane wyjściowe nie są widoczne, a następnie powiodło się. Sprawdź, czy odpowiednie konto jest teraz domyślnie przy użyciu polecenia **AZ Account List**.

### <a name="common-issues-across-environments"></a>Typowe problemy w różnych środowiskach

#### <a name="unauthorized-access-access-denied-forbidden-etc-error"></a>Nieautoryzowany dostęp, odmowa dostępu, zabronione, itp.
 
Używany podmiot zabezpieczeń nie ma dostępu do zasobu, do którego próbuje uzyskać dostęp. W zależności od tego, czy korzystasz z przykładu na lokalnej maszynie deweloperskiej, czy wdrożonej na platformie Azure do App Service, możesz udzielić kontu użytkownika lub "współautora" "Współautor" App Service. Niektóre zasoby, takie jak magazyny kluczy, również mają własne [zasady dostępu](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) , które umożliwiają udzielanie dostępu do podmiotów zabezpieczeń (użytkowników, aplikacji, grup itp.).

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Typowe problemy występujące podczas wdrażania programu w celu Azure App Service

#### <a name="managed-identity-is-not-setup-on-the-app-service"></a>Tożsamość zarządzana nie jest skonfigurowana na App Service
 
Sprawdź zmienne środowiskowe MSI_ENDPOINT i MSI_SECRET istnieje za pomocą [konsoli debugowania kudu](https://azure.microsoft.com/en-us/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Jeśli te zmienne środowiskowe nie istnieją, zarządzana tożsamość nie jest włączona na App Service. 
 
### <a name="common-issues-when-deployed-locally-with-iis"></a>Typowe problemy występujące podczas wdrażania lokalnego przy użyciu usług IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Nie można pobrać tokenów podczas debugowania aplikacji w usługach IIS

Domyślnie AppAuth jest uruchamiany w innym kontekście użytkownika w usługach IIS i w związku z tym nie ma dostępu do korzystania z tożsamości dewelopera do pobierania tokenów dostępu. Można skonfigurować usługi IIS do uruchamiania z kontekstem użytkownika, wykonując następujące dwa kroki:
- Skonfiguruj pulę aplikacji dla aplikacji sieci Web do uruchamiania jako bieżące konto użytkownika. Zobacz więcej informacji [tutaj](https://docs.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Skonfiguruj wartość "setProfileEnvironment" jako "true". Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/en-us/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Przejdź do%windir%\System32\inetsrv\config\applicationHost.config
    - Wyszukaj ciąg "setProfileEnvironment". Jeśli jest ustawiona na wartość "false", Zmień ją na "true". Jeśli nie jest obecny, Dodaj ją jako atrybut do elementu processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) i ustaw ją na wartość "true".

- Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Dowiedz się więcej o [scenariuszach uwierzytelniania usługi Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
