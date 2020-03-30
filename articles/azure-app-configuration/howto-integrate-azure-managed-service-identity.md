---
title: Uwierzytelnij się przy użyciu tożsamości zarządzanych platformy Azure
titleSuffix: Azure App Configuration
description: Uwierzytelnij się w konfiguracji aplikacji platformy Azure przy użyciu tożsamości zarządzanych platformy Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056835"
---
# <a name="integrate-with-azure-managed-identities"></a>Integracja z usługą tożsamości zarządzanych platformy Azure

Tożsamości zarządzane w usłudze Azure Active Directory upraszczają zarządzanie [wpisami tajnymi](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji w chmurze. Dzięki tożsamości zarządzanej kod może używać jednostki usługi utworzonej dla usługi platformy Azure, na której działa. Tożsamość zarządzana jest używana zamiast oddzielnych poświadczeń przechowywanych w usłudze Azure Key Vault lub w lokalnym ciągu połączenia. 

Usługa Azure App Configuration i jej biblioteki klienckie .NET Core, .NET Framework i Java Spring zarządzały wbudowaną obsługą tożsamości. Chociaż nie są wymagane do korzystania z niego, tożsamość zarządzana eliminuje potrzebę tokenu dostępu, który zawiera wpisy tajne. Kod można uzyskać dostęp do magazynu konfiguracji aplikacji przy użyciu tylko punktu końcowego usługi. Ten adres URL można osadzić bezpośrednio w kodzie bez ujawniania kodu tajnego.

W tym artykule pokazano, jak można korzystać z tożsamości zarządzanej, aby uzyskać dostęp do konfiguracji aplikacji. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem [utwórz aplikację ASP.NET Core przy czym najpierw możesz skonfigurować aplikację.](./quickstart-aspnet-core-app.md)

W tym artykule pokazano również, jak można używać tożsamości zarządzanej w połączeniu z odwołaniami do magazynu kluczy konfiguracji aplikacji. Dzięki jednej tożsamości zarządzanej można bezproblemowo uzyskiwać dostęp zarówno do wpisów tajnych z usługi Key Vault, jak i wartości konfiguracji z konfiguracji aplikacji. Jeśli chcesz zapoznać się z tą funkcją, zakończ [Korzystanie z odwołań do magazynu kluczy z ASP.NET Core.](./use-key-vault-references-dotnet-core.md)

Aby wykonać czynności opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) to doskonała opcja dostępna na platformach Windows, macOS i Linux.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Udziel dostępu do tożsamości zarządzanej konfiguracji aplikacji.
> * Skonfiguruj aplikację tak, aby używała tożsamości zarządzanej podczas łączenia się z konfiguracją aplikacji.
> * Opcjonalnie skonfiguruj aplikację tak, aby używała zarządzanej tożsamości podczas łączenia się z magazynem kluczy za pośrednictwem odwołania do usługi App Configuration Key Vault.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Usługa Azure Cloud Shell skonfigurowana](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Dodawanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną w portalu, należy najpierw utworzyć aplikację, a następnie włączyć funkcję.

1. Utwórz wystąpienie usługi App Services w [witrynie Azure portal,](https://portal.azure.com) jak zwykle. Przejdź do niego w portalu.

1. Przewiń w dół do grupy **Ustawienia** w lewym okienku i wybierz pozycję **Tożsamość**.

1. Na karcie **Przypisany system** przełącz **stan** **na Włączone** i wybierz pozycję **Zapisz**.

1. Odpowiedź **Tak** po wyświetleniu monitu o włączenie tożsamości zarządzanej przypisanej do systemu.

    ![Ustawianie tożsamości zarządzanej w usłudze App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udzielanie dostępu do usługi App Configuration

1. W [witrynie Azure portal](https://portal.azure.com)wybierz **pozycję Wszystkie zasoby** i wybierz sklep konfiguracji aplikacji utworzony w przewodniku Szybki start.

1. Wybierz **pozycję Kontrola dostępu (IAM)**.

1. Na karcie **Sprawdź dostęp** wybierz pozycję **Dodaj** w interfejsie użytkownika karty **przypisania roli Dodaj.**

1. W obszarze **Rola**wybierz pozycję **Czytnik danych konfiguracji aplikacji**. W obszarze **Przypisywanie dostępu do**wybierz pozycję **Usługa aplikacji** w obszarze **Tożsamość zarządzana przypisana systemowi**.

1. W obszarze **Subskrypcja**wybierz subskrypcję platformy Azure. Wybierz zasób usługi app service dla aplikacji.

1. Wybierz **pozycję Zapisz**.

    ![Dodawanie tożsamości zarządzanej](./media/add-managed-identity.png)

1. Opcjonalnie: Jeśli chcesz również udzielić dostępu do usługi Key Vault, postępuj zgodnie ze wskazówkami w [aplikacji Udostępnij uwierzytelnianie usługi Key Vault z zarządzaną tożsamością.](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

1. Dodaj odwołanie do pakietu *Azure.Identity:*

    ```cli
    dotnet add package Azure.Identity
    ```

1. Znajdź punkt końcowy w sklepie konfiguracji aplikacji. Ten adres URL jest wyświetlany na karcie **Klucze programu Access** dla magazynu w witrynie Azure portal.

1. Otwórz *plik appsettings.json*i dodaj następujący skrypt. Zastąp * \<service_endpoint>*, w tym nawiasy, adresem URL do sklepu konfiguracji aplikacji. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Otwórz *Program.cs*i dodaj odwołanie do `Azure.Identity` obszarów nazw i `Microsoft.Azure.Services.AppAuthentication` nazw:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Jeśli chcesz uzyskać dostęp tylko do wartości przechowywanych `CreateWebHostBuilder` bezpośrednio w `config.AddAzureAppConfiguration()` konfiguracji aplikacji, zaktualizuj metodę, zastępując metodę.

    > [!IMPORTANT]
    > `CreateHostBuilder`zastępuje `CreateWebHostBuilder` w .NET Core 3.0.  Wybierz poprawną składnię na podstawie środowiska.

    ### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Aby użyć zarówno wartości konfiguracji aplikacji, jak i odwołań do usługi Key Vault, zaktualizuj *Program.cs,* jak pokazano poniżej. Ten kod tworzy `KeyVaultClient` nowy `AzureServiceTokenProvider` przy użyciu i przekazuje `UseAzureKeyVault` to odwołanie do wywołania metody.

    ### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Teraz możesz uzyskać dostęp do odwołań do usługi Key Vault, tak jak do każdego innego klucza konfiguracji aplikacji. Dostawca konfiguracji użyje `KeyVaultClient` skonfigurowanych do uwierzytelniania w magazynie kluczy i pobierania wartości.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Wdrażanie z lokalnego wdrożenia narzędzia Git

Najprostszym sposobem włączenia lokalnego wdrożenia git dla aplikacji za pomocą serwera kompilacji Kudu jest użycie [usługi Azure Cloud Shell.](https://shell.azure.com)

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włączanie lokalnej usługi Git za pomocą serwera Kudu
Jeśli nie masz lokalnego repozytorium git dla aplikacji, musisz je zainicjować. Aby zainicjować lokalne repozytorium git, uruchom następujące polecenia z katalogu projektu aplikacji:

```cmd
git init
git add .
git commit -m "Initial version"
```

Aby włączyć lokalne wdrażanie git dla aplikacji za [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) pomocą serwera kompilacji Kudu, uruchom w usłudze Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

To polecenie daje coś podobnego do następującego wyjścia:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Wdrażanie projektu

W _oknie terminala lokalnego_dodaj pilota platformy Azure do lokalnego repozytorium Git. Zastąp _ \<adres URL>_ adresem URL pilota Git, który otrzymałeś od Włącz lokalny [Git z Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Po wyświetleniu monitu o podanie hasła wprowadź hasło utworzone w [obszarze Konfigurowanie użytkownika wdrożeniowego](#configure-a-deployment-user). Nie używaj hasła używanego do logowania się do witryny Azure portal.

```bash
git push azure master
```

Automatyzacja specyficzne dla środowiska uruchomieniowego w danych wyjściowych, takich jak `npm install` MSBuild dla `pip install` ASP.NET, dla Node.js i Python.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do aplikacji sieci web za pomocą przeglądarki, aby sprawdzić, czy zawartość jest wdrażana.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Używanie tożsamości zarządzanej w innych językach

Dostawcy usługi App Configuration dla programu .NET Framework i Java Spring również mają wbudowaną obsługę tożsamości zarządzanej. Można użyć punktu końcowego adresu URL sklepu zamiast jego pełnego ciągu połączenia podczas konfigurowania jednego z tych dostawców. 

Na przykład można zaktualizować aplikację konsoli .NET Framework utworzoną w przewodniku Szybki start, aby określić następujące ustawienia w pliku *App.config:*

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
W tym samouczku dodano tożsamość zarządzaną platformy Azure, aby usprawnić dostęp do konfiguracji aplikacji i poprawić zarządzanie poświadczeniami dla aplikacji. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
