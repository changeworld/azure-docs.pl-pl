---
title: Bezpiecznie zapisywanie ustawień tajne aplikacji dla aplikacji sieci web | Dokumentacja firmy Microsoft
description: Jak bezpiecznie zapisać ustawienia tajne aplikacji, takie jak poświadczenia platformy Azure lub innej interfejsu API klucze za pomocą platformy ASP.NET core dostawcy magazynu kluczy, klucz tajny użytkownika lub .NET 4.7.1 konstruktorów konfiguracji
services: visualstudio
documentationcenter: ''
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: ''
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: 61cb9fadd60ba35f11e2f3ade94bc647fac9ed72
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235927"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpiecznie zapisać ustawienia tajne aplikacji dla aplikacji sieci web

## <a name="overview"></a>Przegląd
W tym artykule opisano, jak bezpiecznie zapisać ustawienia konfiguracji tajne aplikacji dla aplikacji platformy Azure.

Tradycyjnie wszystkich sieci web konfigurację aplikacji, które ustawienia są zapisywane w plikach konfiguracji, takich jak plik Web.config. Takie rozwiązanie prowadzi do ewidencjonowanie tajny ustawienia, takie jak chmury poświadczeń dla systemów kontroli źródła publiczny, takich jak Github. W tym samym czasie może być trudne do postępuj zgodnie z zaleceniami dotyczącymi zabezpieczeń ze względu na obciążenie, aby zmienić kod źródłowy i ponownie skonfigurować ustawienia środowiska deweloperskiego.

Aby upewnij się, że proces tworzenia jest bezpieczne, biblioteki narzędzi i framework są tworzone można zapisać ustawień tajne aplikacji bezpiecznego z minimalnym i nie zmiany kodu źródłowego.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET i .NET core aplikacji

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Zapisz ustawienia tajnego w magazynie użytkownika klucz tajny, który znajduje się poza folderu kontroli źródła
Jeśli przeprowadzasz szybkie prototypu lub nie masz dostępu do Internetu, rozpoczynać przenoszenie ustawień tajny poza folderu kontroli źródła do magazynu klucz tajny użytkownika. Magazyn tajny użytkownika jest plik zapisany w folderze profilera użytkownika, więc klucze tajne nie są sprawdzane celu kontroli źródła. Poniższy diagram ilustruje sposób [hasło użytkownika](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager) działa.

![Hasło użytkownika przechowuje ustawienia tajny poza kontrolą źródła](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Jeśli korzystasz z aplikacji konsoli .NET core, należy użyć magazynu kluczy bezpiecznie zapisać klucz tajny.

### <a name="save-secret-settings-in-azure-key-vault"></a>Zapisz ustawienia tajnego w magazynie kluczy Azure
Jeśli opracowujesz projektu zespołowego i bezpieczne udostępnianie kodu źródłowego konieczne, użyj [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Utwórz magazyn kluczy w Twojej subskrypcji platformy Azure. Wypełnij wszystkie wymagane pola w interfejsie użytkownika, a następnie kliknij *Utwórz* w dolnej części bloku

    ![Tworzenie usługi Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Udziel użytkownik i członkowie zespołu dostępu do magazynu kluczy. Jeśli masz dużą zespołu, możesz utworzyć [grupy usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) i dodać tej grupy zabezpieczeń dostęp do magazynu kluczy. W *uprawnienia klucza tajnego* listy rozwijanej wyboru *uzyskać* i *listy* w obszarze *operacji zarządzania klucz tajny*.

    ![Dodaj zasady dostępu do magazynu kluczy](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Dodaj klucz tajny do usługi Key Vault w portalu Azure. Ustawień konfiguracji zagnieżdżonych, zastąp ":" z "--", nazwa klucza tajnego usługi Key Vault jest prawidłowy. ":" nie może być nazwach magazyn kluczy tajnych.

    ![Dodaj klucz tajny usługi Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Zainstaluj [rozszerzenie uwierzytelniania usługi Azure dla programu Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). Za pomocą tego rozszerzenia aplikacji ma dostęp do usługi Key Vault przy użyciu programu Visual Studio logowania tożsamości.

5. Dodaj następujące pakiety NuGet do projektu:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Dodaj następujący kod do pliku Program.cs:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Dodaj adres URL magazynu kluczy do pliku launchsettings.json. Nazwa zmiennej środowiskowej *KEYVAULT_ENDPOINT* jest zdefiniowany w kodzie dodanym w kroku 6.

    ![Dodaj adres URL magazynu kluczy jako zmienną środowiskową projektu](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Rozpocznij debugowanie projektu. Powinien działać prawidłowo.

## <a name="aspnet-and-net-applications"></a>Aplikacje programu ASP.NET i .NET

.NET 4.7.1 obsługuje konstruktorów konfiguracji magazynu kluczy i klucz tajny, który zapewnia, że klucze tajne można przenieść poza folder kontroli źródła bez zmian w kodzie.
Aby kontynuować, [Pobierz .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) i migracji aplikacji, jeśli używa starszej wersji programu .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Zapisz ustawienia tajny w pliku tajny, który znajduje się poza folderu kontroli źródła
Jeśli pisania szybkie prototypu i nie chcesz zainicjować obsługę administracyjną zasobów platformy Azure, przejdź przy użyciu tej opcji.

1. Zainstaluj następujący pakiet NuGet do projektu
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Utwórz plik, który jest podobny do podjęcia dalszych. Zapisz go w lokalizacji poza folderu projektu.

    ```xml

       <root>
              <secrets ver="1.0">
                     <secret name="secret1" value="foo_one" />
                        <secret name="secret2" value="foo_two" />
                       </secrets>
      </root>
      ```

3. Zdefiniuj plik tajnego do konstruktora konfiguracji w pliku Web.config. Umieść w tej sekcji, przed *appSettings* sekcji.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Określ sekcji appSettings jest przy użyciu narzędzia Konstruktor tajny konfiguracji. Upewnij się, że ma żadnych wpisu tajny ustawienie z wartością zastępczego.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Debugowanie aplikacji. Powinien działać prawidłowo.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Zapisz ustawienia tajnego w magazynie kluczy Azure
Wykonaj instrukcje z platformy ASP.NET core sekcji, aby skonfigurować magazyn kluczy dla projektu.

1. Zainstaluj następujący pakiet NuGet do projektu
```
Microsoft.Configuration.ConfigurationBuilders.UserSecrets.1.0.0-preview2.nupkg
```

2. Zdefiniuj konstruktora konfiguracji magazynu kluczy w pliku Web.config. Umieść w tej sekcji, przed *appSettings* sekcji. Zastąp *vaultName* mieć nazwę klucza magazynu, jeśli magazyn kluczy jest w publicznej Azure lub pełny identyfikator URI, jeśli używasz suwerennych chmury.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3.  Określ sekcji appSettings jest przy użyciu narzędzia Konstruktor konfiguracji magazynu kluczy. Upewnij się, że ma żadnych wpisu tajny ustawienie z wartością zastępczego.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Rozpocznij debugowanie projektu. Powinien działać prawidłowo.
