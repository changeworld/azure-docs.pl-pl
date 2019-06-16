---
title: Bezpiecznie zapisywanie ustawień klucza tajnego aplikacji dla aplikacji sieci web — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: W jaki sposób można bezpiecznie zapisać ustawienia klucza tajnego aplikacji takich jak poświadczenia platformy Azure lub innych firm interfejsu API kluczy przy użyciu platformy ASP.NET core dostawcy magazynu kluczy, tajemnicy użytkownika lub .NET 4.7.1 Konstruktorzy konfiguracji
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: cawa
ms.openlocfilehash: 9763a14e84d88be1d6f09fb9f16b6b7c9eeffd2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65506425"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpiecznie zapisać ustawienia klucza tajnego aplikacji dla aplikacji sieci web

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak bezpiecznie zapisać ustawienia konfiguracji wpisu tajnego aplikacji dla aplikacji platformy Azure.

Tradycyjnie wszystkie sieci web konfiguracji aplikacji, które ustawienia są zapisywane w plikach konfiguracji, takich jak plik Web.config. Praktyka ta prowadzi do zaewidencjonowania ustawień tajnych, takich jak poświadczenia w chmurze dla systemów kontroli źródła publicznych, takich jak GitHub. W tym samym czasie może być trudno postępuj zgodnie z zaleceniami dotyczącymi zabezpieczeń ze względu na obciążenie, trzeba zmienić kod źródłowy i ponownie skonfigurować ustawienia środowiska deweloperskiego.

Aby upewnić się, że proces tworzenia aplikacji jest bezpieczna, narzędzia i struktury bibliotek są tworzone można zapisać ustawień klucza tajnego aplikacji bezpiecznego przy minimalnym i żadne zmiany kodu źródłowego.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET i .NET core z aplikacji

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Zapisuj ustawień tajnych w magazynie tajemnicy użytkownika, który znajduje się poza folder kontroli źródła
Jeśli macie szybkiego prototypu lub nie masz dostępu do Internetu, rozpoczynać przenoszenie ustawień klucza tajnego poza folder kontroli źródła do magazynu tajemnicy użytkownika. Magazynu wpisów tajnych użytkownika jest plikiem, który został zapisany w folderze Program profilujący użytkownika, więc klucze tajne nie zostały zaewidencjonowane do kontroli źródła. Poniższy diagram przedstawia sposób [tajemnicy użytkownika](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) działa.

![Tajemnicy użytkownika utrzymuje ustawień tajnych poza kontrolą źródła](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Jeśli korzystasz z aplikacji konsoli .NET core, użyj usługi Key Vault można bezpiecznie zapisać klucz tajny.

### <a name="save-secret-settings-in-azure-key-vault"></a>Zapisuj ustawień tajnych w usłudze Azure Key Vault
Jeśli tworzysz projekt i trzeba bezpiecznie udostępniać kod źródłowy, użyj [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Utwórz magazyn kluczy w subskrypcji platformy Azure. Wypełnij wszystkie wymagane pola w interfejsie użytkownika, a następnie kliknij *Utwórz* w dolnej części bloku

    ![Tworzenie usługi Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Udziel wszystkich członków zespołu, dostęp do usługi Key Vault. Jeśli masz duży zespół, można utworzyć [grupy usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) i Dodaj grupy zabezpieczeń, że dostęp do usługi Key Vault. W *uprawnienia klucza tajnego* listy rozwijanej wyboru *uzyskać* i *listy* w obszarze *operacji zarządzania wpisu tajnego*.

    ![Dodawanie zasad dostępu magazynu kluczy](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Dodaj klucz tajny do usługi Key Vault w witrynie Azure portal. Dla ustawień konfiguracji zagnieżdżonych, zastąp ":" z "—", nazwa wpisu tajnego usługi Key Vault jest prawidłowy. ":" nie może mieć nazwę usługi Key Vault wpisu tajnego.

    ![Dodawanie wpisu tajnego usługi Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE] 
    > Przed Visual Studio 2017 V15.6 użyliśmy zaleca się zainstalowanie rozszerzenia uwierzytelniania usług platformy Azure dla programu Visual Studio. Jest przestarzały, ale teraz funcionality jest zintegrowana w programie Visual Studio. Dlatego jeśli masz starszą wersję programu visual Studio 2017, zalecamy jej aktualizacji do co najmniej programu VS 2017 15.6 lub wyższej, dzięki czemu mogą natywnie korzystać z tej funkcji i uzyskać dostęp do usługi Key vault z przy użyciu programu Visual Studio logowania tożsamości sam.
    >
 
4. Dodaj następujące pakiety NuGet do projektu:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
5. Dodaj następujący kod do pliku Program.cs:

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
6. Dodaj adres URL usługi Key Vault do pliku launchsettings.json. Nazwa zmiennej środowiskowej *KEYVAULT_ENDPOINT* jest definiowana w kodzie dodanym w kroku 6.

    ![Dodaj adres URL magazynu Key jako zmienną środowiskową projektu](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Uruchom debugowanie projektu. Należy uruchomić pomyślnie.

## <a name="aspnet-and-net-applications"></a>Aplikacje ASP.NET i .NET

.NET 4.7.1 obsługuje Konstruktorzy konfiguracji magazynu kluczy oraz klucz tajny, który gwarantuje, że wpisy tajne można przenosić poza folder kontroli źródła bez zmian w kodzie.
Aby kontynuować, [Pobierz .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) i migracji aplikacji, jeśli wykorzystywana jest starsza wersja programu .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Zapisuj ustawień tajnych w pliku klucza tajnego, który znajduje się poza folder kontroli źródła
Jeśli piszesz szybkiego prototypu i nie chcesz aprowizowania zasobów platformy Azure, ta opcja zawsze pod ręką.

1. Zainstaluj następujący pakiet NuGet do projektu
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic
    ```

2. Utwórz plik, który jest podobny do działaniami. Zapisz go w lokalizacji poza folderem projektu.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Zdefiniuj plik wpisów tajnych do konstruktora konfiguracji w pliku Web.config. Umieść tę sekcję przed *appSettings* sekcji.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Określ sekcji appSettings jest za pomocą konstruktora tajny konfiguracji. Upewnij się, że istnieje wpis dla klucza tajnego ustawienia o wartości.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Debugowanie aplikacji. Należy uruchomić pomyślnie.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Zapisuj ustawień tajnych w usłudze Azure Key Vault
Wykonaj instrukcje z platformy ASP.NET core sekcji, aby skonfigurować usługę Key Vault dla Twojego projektu.

1. Zainstaluj następujący pakiet NuGet do projektu
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Definiowanie konstruktora konfiguracji usługi Key Vault w pliku Web.config. Umieść tę sekcję przed *appSettings* sekcji. Zastąp *vaultName* jako nazwę usługi Key Vault, jeśli usługi Key Vault jest w publicznej platformy Azure lub pełny identyfikator URI, jeśli używasz należących do suwerennej chmury.

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
3. Określ, że używa konstruktora konfiguracji usługi Key Vault w sekcji appSettings. Upewnij się, że istnieje wpis dla klucza tajnego ustawienia o wartości.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Uruchom debugowanie projektu. Należy uruchomić pomyślnie.
