---
title: Samouczek dotyczący korzystania z konfiguracji dynamicznej konfiguracji aplikacji platformy Azure w aplikacji usługi Azure Functions | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracyjne dla aplikacji usługi Azure Functions
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74185450"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Samouczek: Używanie konfiguracji dynamicznej w aplikacji usługi Azure Functions

Dostawca konfiguracji .NET Standard konfiguracji konfiguracji obsługuje buforowanie i odświeżanie konfiguracji dynamicznie napędzany przez działanie aplikacji. W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się na aplikacji usługi Azure Functions wprowadzone w przewodnikach Szybki start. Przed kontynuowaniem należy najpierw ukończyć [tworzenie aplikacji azure functions przy konfigurowaniu aplikacji platformy Azure.](./quickstart-azure-functions-csharp.md)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację usługi Azure Functions, aby zaktualizować jej konfigurację w odpowiedzi na zmiany w sklepie konfiguracji aplikacji.
> * Wstrzyknąć najnowszą konfigurację do wywołań usługi Azure Functions.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) z obciążeniem **dewelopera platformy Azure**
- [Narzędzia usługi Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Kończenie [przewodnika](./quickstart-azure-functions-csharp.md) Szybki start Tworzenie aplikacji funkcji platformy Azure za pomocą konfiguracji aplikacji platformy Azure

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Otwórz *Function1.cs*. `static` Oprócz `Configuration`właściwości , dodaj nową `static` `ConfigurationRefresher` właściwość, aby zachować wystąpienie `IConfigurationRefresher` singleton, które będą używane do sygnalizowania aktualizacji konfiguracji podczas wywołania funkcji później.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Zaktualizuj konstruktora i użyj metody, `ConfigureRefresh` aby określić ustawienie, które ma zostać odświeżone ze sklepu konfiguracji aplikacji. Wystąpienie `IConfigurationRefresher` jest pobierane `GetRefresher` przy użyciu metody. Opcjonalnie możemy również zmienić okno czasu wygaśnięcia pamięci podręcznej konfiguracji do 1 minuty od domyślnego 30 sekund.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Zaktualizuj metodę i sygnał, `Run` aby odświeżyć konfigurację `Refresh` przy użyciu metody na początku wywołania Functions. Będzie to no-op, jeśli okno czasu wygaśnięcia pamięci podręcznej nie zostanie osiągnięte. Usuń `await` operatora, jeśli wolisz, aby konfiguracja była odświeżana bez blokowania.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i uruchom ponownie wiersz polecenia, aby umożliwić zastosowanie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz systemu macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby przetestować funkcję, naciśnij klawisz F5. Jeśli zostanie wyświetlony monit, zaakceptuj żądanie z programu Visual Studio, aby pobrać i zainstalować narzędzia **programu Azure Functions Core (CLI).** Może być również konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP.

3. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Debugowanie funkcji w programie VS z przewodnika Szybki start](./media/quickstarts/function-visual-studio-debugging.png)

4. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce na lokalne żądanie GET zwrócone przez funkcję.

    ![Lokalne uruchamianie funkcji z przewodnika Szybki start](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **pozycję Wszystkie zasoby**i wybierz wystąpienie sklepu konfiguracja aplikacji utworzone w przewodniku Szybki start.

6. Wybierz **pozycję Eksplorator konfiguracji**i zaktualizuj wartości następującego klucza:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

7. Odśwież przeglądarkę kilka razy. Gdy ustawienie buforowane wygasa po minucie, strona pokazuje odpowiedź wywołania Functions ze zaktualizowaną wartością.

    ![Funkcja Szybki start odświeża lokalnie](./media/quickstarts/dotnet-core-function-refresh-local.png)

Przykładowy kod użyty w tym samouczku można pobrać z [repozytorium GitHub konfiguracji aplikacji](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację usługi Azure Functions, aby dynamicznie odświeżać ustawienia konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak użyć tożsamości zarządzanej platformy Azure, aby usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
