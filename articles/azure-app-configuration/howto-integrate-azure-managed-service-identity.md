---
title: Integracja z tożsamościami zarządzanymi przez platformę Azure | Microsoft Docs
description: Dowiedz się, jak używać tożsamości zarządzanych przez platformę Azure do uwierzytelniania i uzyskiwania dostępu do konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035533"
---
# <a name="integrate-with-azure-managed-identities"></a>Integracja z tożsamościami zarządzanymi przez platformę Azure

Azure Active Directory [zarządzane tożsamości](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pomagają uprościć zarządzanie kluczami tajnymi aplikacji w chmurze. Za pomocą tożsamości zarządzanej można skonfigurować swój kod do korzystania z jednostki usługi, która została utworzona dla usługi obliczeniowej platformy Azure, na której działa. Użytkownik korzysta z tożsamości zarządzanej zamiast oddzielnego poświadczenia przechowywanego w Azure Key Vault lub lokalnych parametrów połączenia. 

Konfiguracja aplikacji platformy Azure i jej biblioteki klienckie .NET Core, .NET i Java sprężyny są dostarczane z wbudowaną tożsamością usługi zarządzanej (MSI). Chociaż nie jest to wymagane, plik MSI eliminuje konieczność korzystania z tokenu dostępu zawierającego wpisy tajne. Twój kod może uzyskać dostęp do magazynu konfiguracji aplikacji przy użyciu tylko punktu końcowego usługi. Możesz osadzić ten adres URL w kodzie bezpośrednio bez obaw o ujawnienie jakichkolwiek wpisów tajnych.

W tym samouczku pokazano, jak możesz korzystać z zalet tożsamości usługi zarządzanej w celu uzyskiwania dostępu do usługi App Configuration. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji ASP.NET Coreej z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

Ponadto w tym samouczku pokazano, jak można użyć MSI w połączeniu z odwołaniami Key Vault konfiguracji aplikacji. Dzięki temu można bezproblemowo uzyskać dostęp do wpisów tajnych przechowywanych w Key Vault oraz wartości konfiguracyjnych w konfiguracji aplikacji. Aby poznać tę możliwość, należy najpierw zakończyć [Korzystanie z Key Vault odwołań z ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przyznaj zarządzanej tożsamości dostęp do konfiguracji aplikacji.
> * Skonfiguruj aplikację do korzystania z tożsamości zarządzanej podczas łączenia się z konfiguracją aplikacji.
> * Opcjonalnie można skonfigurować aplikację do korzystania z tożsamości zarządzanej podczas łączenia się z Key Vault za pomocą Key Vault informacji o konfiguracji aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell skonfigurowany](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Dodawanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną w portalu, należy najpierw utworzyć aplikację jako normalną, a następnie włączyć tę funkcję.

1. Utwórz wystąpienie App Services w [Azure Portal](https://portal.azure.com) jak zwykle. Przejdź do niego w portalu.

2. Przewiń w dół do grupy **Ustawienia** w okienku po lewej stronie, a następnie wybierz pozycję **tożsamość**.

3. Na karcie **przypisane do systemu** Przełącz pozycję **stan** na **włączone** i wybierz pozycję **Zapisz**.

4. Odpowiedź **tak** po wyświetleniu monitu o włączenie tożsamości zarządzanej przypisanej przez system.

    ![Ustawianie tożsamości zarządzanej w usłudze App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udzielanie dostępu do usługi App Configuration

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby** i wybierz magazyn konfiguracji aplikacji utworzony w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Kontrola dostępu (IAM)** .

1. Na karcie **sprawdzanie dostępu** wybierz pozycję **Dodaj** w interfejsie użytkownika karty **Dodaj rolę** .

1. W obszarze **rola**wybierz pozycję **współautor**. W obszarze **Przypisz dostęp do**wybierz pozycję **App Service** w obszarze **system przypisanej tożsamości zarządzanej**.

1. W obszarze **subskrypcja**wybierz subskrypcję platformy Azure. Wybierz zasób App Service dla aplikacji.

1. Wybierz pozycję **Zapisz**.

    ![Dodawanie tożsamości zarządzanej](./media/add-managed-identity.png)

1. Opcjonalnie: Jeśli chcesz udzielić dostępu do Key Vault, postępuj zgodnie z instrukcjami w temacie [zapewnianie Key Vault uwierzytelniania przy użyciu tożsamości zarządzanej](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

1. Znajdź adres URL magazynu konfiguracji aplikacji, przechodząc do jego ekranu konfiguracji w Azure Portal, a następnie klikając kartę **klucze dostępu** .

1. Otwórz plik *appSettings. JSON*i Dodaj następujący skrypt. Zastąp *\<service_endpoint >* , w tym nawiasy, adresem URL Twojego magazynu konfiguracji aplikacji. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Jeśli chcesz uzyskać dostęp tylko do wartości przechowywanych bezpośrednio w konfiguracji aplikacji, Otwórz *program.cs*i zaktualizuj metodę `CreateWebHostBuilder`, zastępując metodę `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Jeśli chcesz użyć wartości konfiguracji aplikacji, a także odwołań Key Vault, Otwórz *program.cs*i zaktualizuj metodę `CreateWebHostBuilder`, jak pokazano poniżej. Spowoduje to utworzenie nowego `KeyVaultClient` przy użyciu `AzureServiceTokenProvider` i przekazuje to odwołanie do wywołania metody `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Teraz możesz uzyskiwać dostęp do Key Vault odwołań podobnie jak każdy inny klucz konfiguracji aplikacji. Dostawca konfiguracji użyje `KeyVaultClient` skonfigurowanego do uwierzytelniania, aby Key Vault i pobrać wartość.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Wdrażanie z lokalnego wdrożenia narzędzia Git

Najprostszym sposobem włączenia lokalnego wdrożenia narzędzia Git dla aplikacji przy użyciu serwera kompilacji kudu jest użycie Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włączanie lokalnej usługi Git za pomocą serwera Kudu
Jeśli nie masz lokalnego repozytorium git dla swojej aplikacji, musisz ją zainicjować. Aby to zrobić, uruchom następujące polecenia w katalogu projektu aplikacji:

```cmd
git init
git add .
git commit -m "Initial version"
```

Aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji przy użyciu serwera kompilacji kudu, uruchom [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) w Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Aby zamiast tego utworzyć aplikację z obsługą usługi git, uruchom [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) w Cloud Shell za pomocą parametru `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Polecenie `az webapp create` daje coś podobnego do następującego:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Wdrażanie projektu

W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp element _\<url>_ adresem URL zdalnego repozytorium Git uzyskanego po [włączeniu usługi Git dla Twojej aplikacji](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Po wyświetleniu monitu o podanie hasła wprowadź hasło utworzone w obszarze [Konfigurowanie użytkownika wdrożenia](#configure-a-deployment-user). Nie używaj hasła używanego do logowania się do Azure Portal.

```bash
git push azure master
```

W danych wyjściowych można zobaczyć automatyzację specyficzną dla środowiska uruchomieniowego, na przykład MSBuild for ASP.NET, `npm install` dla środowiska Node. js i `pip install` dla języka Python.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do aplikacji sieci Web przy użyciu przeglądarki, aby sprawdzić, czy zawartość została wdrożona.

```bash
http://<app_name>.azurewebsites.net
```

![Aplikacja działająca w App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Używanie tożsamości zarządzanej w innych językach

Dostawcy usługi App Configuration dla programu .NET Framework i Java Spring również mają wbudowaną obsługę tożsamości zarządzanej. W takich przypadkach należy użyć punktu końcowego adresu URL magazynu konfiguracji aplikacji zamiast jego pełnych parametrów połączenia podczas konfigurowania dostawcy. Na przykład w przypadku aplikacji konsolowej .NET Framework utworzonej w ramach przewodnika Szybki Start określ następujące ustawienia w pliku *App. config* :

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
