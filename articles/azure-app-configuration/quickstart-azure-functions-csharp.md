---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą Azure Functions | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z platformą Azure Functions.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 2f6efdad7ab0685e58d2edd73bc36b758e8dbae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245501"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Szybki start: tworzenie aplikacji usługi Azure Functions za pomocą konfiguracji aplikacji platformy Azure

W tym przewodniku Szybki start można włączyć usługę konfiguracji aplikacji platformy Azure do aplikacji usługi Azure Functions, aby scentralizować magazyn i zarządzanie wszystkimi ustawieniami aplikacji oddzielonymi od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) z obciążeniem **dewelopera platformy Azure.**
- [Narzędzia usługi Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **Eksplorator** > konfiguracji **+ Utwórz** > **wartość klucza,** aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykietę** i **typ zawartości** puste na razie.

7. Wybierz przycisk **Zastosuj**.

## <a name="create-a-functions-app"></a>Tworzenie aplikacji Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Łączenie się ze sklepem konfiguracji aplikacji

1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** wyszukaj `Microsoft.Extensions.Configuration.AzureAppConfiguration` i dodaj pakiet NuGet do projektu. Jeśli nie możesz go znaleźć, zaznacz pole wyboru **Dołącz wydanie wstępne.**

2. Otwórz *Function1.cs*i dodaj obszary nazw konfiguracji .NET Core i dostawcy konfiguracji konfiguracji aplikacji.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Dodaj `static` właściwość `Configuration` o nazwie, aby `IConfiguration`utworzyć wystąpienie singleton . Następnie dodaj `static` konstruktora, aby `AddAzureAppConfiguration()`połączyć się z konfiguracją aplikacji, wywołując program . Spowoduje to załadowanie konfiguracji raz przy uruchamianiu aplikacji. To samo wystąpienie konfiguracji będzie używane dla wszystkich wywołań funkcji później.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Zaktualizuj `Run` metodę odczytu wartości z konfiguracji.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i uruchom ponownie wiersz polecenia, aby umożliwić zastosowanie zmiany:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz systemu macOS lub Linux, uruchom następujące polecenie:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Naciśnij klawisz F5, aby przetestować swoją funkcję. Jeśli zostanie wyświetlony monit, zaakceptuj żądanie z programu Visual Studio, aby pobrać i zainstalować narzędzia **programu Azure Functions Core (CLI).** Może być również konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP.

3. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Debugowanie funkcji w programie VS z przewodnika Szybki start](./media/quickstarts/function-visual-studio-debugging.png)

4. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce na lokalne żądanie GET zwrócone przez funkcję.

    ![Lokalne uruchamianie funkcji z przewodnika Szybki start](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy sklep konfiguracji aplikacji i użyto go z aplikacją Usługi Azure za pośrednictwem [dostawcy konfiguracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2074664) Aby dowiedzieć się, jak skonfigurować aplikację usługi Azure Functions do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-azure-functions-csharp.md)
