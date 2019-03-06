---
title: Samouczek dotyczący integracji z usługą tożsamości zarządzanych platformy Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak korzystać z usługi tożsamości zarządzanych platformy Azure do uwierzytelniania i uzyskiwania dostępu do usługi Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957355"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Samouczek: Integracja z usługą tożsamości zarządzanych platformy Azure

[Tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) usługi Azure Active Directory pomagają uprościć zarządzanie wpisami tajnymi dla Twojej aplikacji w chmurze. Za pomocą tożsamości zarządzanej możesz skonfigurować swój kod do użycia jednostki usługi, która została utworzona na potrzeby usługi obliczeniowej platformy Azure, na której ona działa, a nie oddzielnych poświadczeń przechowywanych w usłudze Azure Key Vault lub parametrów połączenia lokalnego. Usługa Azure App Configuration i jej biblioteki klienckie platformy .NET Core, .NET i Java Spring są dostarczane z wbudowaną obsługą tożsamości usługi zarządzanej. Chociaż nie trzeba jej używać, tożsamość usługi zarządzanej eliminuje potrzebę tokenu dostępu, który zawiera wpisy tajne. Twój kod musi jedynie znać punkt końcowy usługi dla magazynu konfiguracji aplikacji, aby uzyskać do niego dostęp, a ten adres URL możesz osadzić w kodzie bezpośrednio bez ujawniania jakiegokolwiek wpisu tajnego.

W tym samouczku pokazano, jak możesz korzystać z zalet tożsamości usługi zarządzanej w celu uzyskiwania dostępu do usługi App Configuration. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuacją należy ukończyć [tworzenie aplikacji platformy ASP.NET Core z użyciem usługi App Configuration](./quickstart-aspnet-core-app.md).

Do wykonania kroków tego samouczka możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udzielanie tożsamości zarządzanej dostępu do usługi App Configuration
> * Konfigurowanie aplikacji do korzystania z tożsamości zarządzanej podczas łączenia z usługą App Configuration

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Zestaw SDK dla platformy .NET Core](https://www.microsoft.com/net/download/windows)
* [Skonfigurowana usługa Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Dodawanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję.

1. Utwórz aplikację w witrynie [Azure Portal](https://aka.ms/azconfig/portal) w zwykły sposób. Przejdź do niej w portalu.

2. Przewiń w dół do grupy **Ustawienia** w obszarze nawigacji po lewej stronie i wybierz pozycję **Tożsamość**.

3. Na karcie **Przypisana przez system** przełącz pozycję **Stan** na **Wł.** i kliknij przycisk **Zapisz**.

    ![Ustawianie tożsamości zarządzanej w usłudze App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udzielanie dostępu do usługi App Configuration

1. W witrynie [Azure Portal](https://aka.ms/azconfig/portal) kliknij przycisk **Wszystkie zasoby**, a następnie magazyn konfiguracji aplikacji, który został utworzony w przewodniku Szybki start.

2. Wybierz pozycję **Kontrola dostępu (IAM)**.

3. Na karcie **Sprawdzanie dostępu** kliknij przycisk **Dodaj** w interfejsie użytkownika karty **Dodaj przypisanie roli**.

4. Ustaw wartość pozycji **Rola** na *Współautor*, a pozycji **Przypisz dostęp do** na usługę *App Service* (w obszarze *Tożsamość zarządzana przypisana przez system*).

5. Ustaw pozycję **Subskrypcja** na swoją subskrypcję platformy Azure i wybierz zasób usługi App Service dla Twojej aplikacji.

6. Kliknij pozycję **Zapisz**.

    ![Dodawanie tożsamości zarządzanej](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

1. Otwórz plik *appsettings.json*, dodaj poniższy kod i zastąp pozycję *< service_endpoint >* (razem z nawiasami) adresem URL magazynu konfiguracji aplikacji:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Otwórz plik *Program.cs* i zaktualizuj metodę `CreateWebHostBuilder`, zastępując metodę `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Wdrażanie z lokalnego wdrożenia narzędzia Git

Najprostszym sposobem włączenia lokalnego wdrożenia usługi Git dla Twojej aplikacji z serwerem kompilacji Kudu jest użycie usługi Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włączanie lokalnej usługi Git za pomocą serwera Kudu

Aby włączyć lokalne wdrożenie usługi Git dla Twojej aplikacji przy użyciu serwera kompilacji Kudu, uruchom polecenie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) w usłudze Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Aby zamiast tego utworzyć aplikację z obsługą usługi Git, uruchom w usłudze Cloud Shell polecenie [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) z parametrem `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Polecenie `az webapp create` powinno dać dane wyjściowe podobne do poniższych:

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

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy zostanie wyświetlony monit o podanie hasła, upewnij się, że wprowadzasz hasło utworzone podczas [konfiguracji użytkownika wdrożenia](#configure-a-deployment-user), a nie hasło, którego używasz do logowania się w witrynie Azure Portal.

```bash
git push azure master
```

W danych wyjściowych możesz zobaczyć automatyzację specyficzną dla środowiska uruchomieniowego, takiego jak MSBuild dla programu ASP.NET, `npm install` dla platformy Node.js i `pip install` dla języka Python.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Za pomocą przeglądarki przejdź do swojej aplikacji internetowej, aby sprawdzić, czy zawartość jest wdrożona.

```bash
http://<app_name>.azurewebsites.net
```

![aplikacja działająca w usłudze App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Używanie tożsamości zarządzanej w innych językach

Dostawcy usługi App Configuration dla programu .NET Framework i Java Spring również mają wbudowaną obsługę tożsamości zarządzanej. W takich przypadkach podczas konfigurowania dostawcy możesz po prostu użyć swojego adresu URL punktu końcowego magazynu konfiguracji aplikacji zamiast jego pełnego ciągu połączenia. Na przykład dla aplikacji konsoli .NET Framework utworzonej w przewodniku Szybki start należy określić następujące ustawienia w pliku *App.config*:

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

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej o korzystaniu z usługi App Configuration, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
