---
title: Integracja z platformą Azure zarządzać tożsamościami | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z platformy Azure zarządzanych tożsamości do uwierzytelniania i uzyskania dostępu do konfiguracji aplikacji usługi Azure
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
ms.openlocfilehash: c2afc96ca5f7e30d5474f66bdad0cfec88a89580
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700226"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Samouczek: Integracja z usługą Azure managed tożsamości

Usługa Azure Active Directory [zarządzanych tożsamości](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) uproszczenia zarządzania wpisami tajnymi dla aplikacji w chmurze. Za pomocą tożsamości zarządzanej możesz skonfigurować kodu, aby użyć jednostki usługi, który został utworzony dla usługi obliczeniowe platformy Azure, których ono działa. Tożsamość zarządzana jest użyć zamiast oddzielnych poświadczeń przechowywanych w usłudze Azure Key Vault lub parametrów połączenia lokalnego. 

Konfiguracja aplikacji platformy Azure i jej platformy .NET Core, .NET i Java Spring bibliotek klienta są dostarczane z usługa managed service identity (MSI) wbudowaną w ich. Mimo że nie są wymagane z niej korzystać, MSI eliminuje potrzebę tokenu dostępu, który zawiera wpisy tajne. Twój kod musi znać tylko punkt końcowy usługi dla konfiguracji aplikacji przechowywania, aby można było uzyskać do niego dostęp. Ten adres URL można osadzić w kodzie bezpośrednio zapewnieniu ujawnienia dowolnego klucza tajnego.

W tym samouczku pokazano, jak możesz korzystać z zalet tożsamości usługi zarządzanej w celu uzyskiwania dostępu do usługi App Configuration. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy zakończyć [tworzenie aplikacji platformy ASP.NET Core z konfiguracji aplikacji](./quickstart-aspnet-core-app.md) pierwszy.

Wykonaj kroki w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udzielanie dostępu tożsamość zarządzaną w konfiguracji aplikacji.
> * Konfigurowanie aplikacji do korzystania z tożsamości zarządzanej, podczas łączenia z konfiguracji aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Zestaw SDK dla platformy .NET core](https://www.microsoft.com/net/download/windows).
* [Usługa Azure Cloud Shell skonfigurowane](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Dodawanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną w portalu, możesz najpierw utworzyć aplikację w zwykły, a następnie włączyć tę funkcję.

1. Tworzenie aplikacji w [witryny Azure portal](https://aka.ms/azconfig/portal) w zwykły sposób. Przejdź do niego w portalu.

2. Przewiń w dół do **ustawienia** grupę w okienku po lewej stronie, a następnie wybierz pozycję **tożsamości**.

3. Na **przypisanej w systemie** kartę, Przełącz **stan** do **na** i wybierz **Zapisz**.

    ![Ustawianie tożsamości zarządzanej w usłudze App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udzielanie dostępu do usługi App Configuration

1. W [witryny Azure portal](https://aka.ms/azconfig/portal), wybierz opcję **wszystkie zasoby** i wybierz magazyn konfiguracji aplikacji, który został utworzony w przewodniku Szybki Start.

2. Wybierz pozycję **Kontrola dostępu (IAM)**.

3. Na **Sprawdź dostęp** zaznacz **Dodaj** w **Dodaj przypisanie roli** karty interfejsu użytkownika.

4. W obszarze **roli**, wybierz opcję **Współautor**. W obszarze **Przypisz dostęp do**, wybierz opcję **usługi App Service** w obszarze **zarządzanych tożsamości przypisanej przez System**.

5. W obszarze **subskrypcji**, wybierz swoją subskrypcję platformy Azure. Wybierz zasób usługi App Service dla aplikacji.

6. Wybierz pozycję **Zapisz**.

    ![Dodawanie tożsamości zarządzanej](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

1. Otwórz *appsettings.json*i Dodaj poniższy skrypt. Zastąp  *\<service_endpoint >*, razem z nawiasami, za pomocą adresu URL do sklepu z aplikacjami konfiguracji:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Otwórz *Program.cs*i zaktualizuj `CreateWebHostBuilder` metody, zastępując `config.AddAzureAppConfiguration()` metody.

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

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Wdrażanie z lokalnego wdrożenia narzędzia Git

Najprostszym sposobem, aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji przy użyciu serwer kompilacji Kudu jest używać usługi Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włączanie lokalnej usługi Git za pomocą serwera Kudu

Aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji przy użyciu serwer kompilacji Kudu, uruchom [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) w usłudze Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Aby zamiast tego utworzyć aplikację z obsługą usługi Git, uruchom [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) w usłudze Cloud Shell za pomocą `--deployment-local-git` parametru.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` Polecenie daje podobne do następujących danych wyjściowych:

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

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Po wyświetleniu monitu o hasło, wprowadź hasło utworzone w [konfiguracji użytkownika wdrożenia](#configure-a-deployment-user). Nie używaj hasło, którego używasz do logowania witrynie Azure Portal.

```bash
git push azure master
```

Może zostać wyświetlony automatyzacji specyficzne dla środowiska uruchomieniowego w danych wyjściowych, takiego Jakmsbuild programu ASP.NET, `npm install` dla środowiska Node.js, i `pip install` dla języka Python.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do aplikacji sieci web za pomocą przeglądarki, aby sprawdzić, czy zawartość jest wdrażana.

```bash
http://<app_name>.azurewebsites.net
```

![Aplikacja działająca w usłudze App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Używanie tożsamości zarządzanej w innych językach

Dostawcy usługi App Configuration dla programu .NET Framework i Java Spring również mają wbudowaną obsługę tożsamości zarządzanej. W takich przypadkach należy użyć punktu końcowego adresu URL magazynu konfiguracji aplikacji zamiast jego pełny ciąg połączenia po skonfigurowaniu dostawcy. Na przykład dla aplikacji konsoli .NET Framework, utworzone w przewodniku Szybki Start, określ poniższe ustawienia w *App.config* pliku:

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

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
