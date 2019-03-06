---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą Azure Functions | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z platformą Azure Functions.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5f28e213a5f824562df62a05b98f0f92f71bc591
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957440"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Szybki start: Tworzenie funkcji platformy Azure przy użyciu usługi App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Dzięki niej możesz łatwo przechowywać wszystkie ustawienia aplikacji w jednym miejscu oddzielonym od kodu i zarządzać tymi ustawieniami. W tym przewodniku Szybki start pokazano, jak zintegrować usługę z funkcją platformy Azure. 

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

![Przewodnik Szybki start ukończony, wersja lokalna](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, zainstaluj [program Visual Studio 2017](https://visualstudio.microsoft.com/vs) (i upewnij się, że zainstalowano również pakiet roboczy **Programowanie na platformie Azure**) oraz [najnowsze narzędzia usługi Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>Łączenie z magazynem konfiguracji aplikacji

1. Otwórz plik *Function1.cs* i dodaj odwołanie do dostawcy konfiguracji platformy .NET Core dla usługi App Configuration.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Zaktualizuj metodę `Run`, tak aby używała usługi App Configuration przez wywołanie elementu `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, wykonaj następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zastosowanie zmiany:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli używasz systemu macOS lub Linux, wykonaj następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby przetestować funkcję, naciśnij klawisz **F5**. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania zestawu narzędzi **Azure Functions Core (CLI)**. Może także być konieczne włączenie wyjątku zapory, aby umożliwić narzędziom obsługę żądań HTTP.

3. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Debugowanie funkcji w programie VS z przewodnika Szybki start](./media/quickstarts/function-visual-studio-debugging.png)

4. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Na poniższym obrazie przedstawiono wyświetloną w przeglądarce odpowiedź na lokalne żądanie GET zwróconą przez funkcję:

    ![Lokalne uruchamianie funkcji z przewodnika Szybki start](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony nowy magazyn konfiguracji aplikacji i użyto go z funkcją platformy Azure. Aby dowiedzieć się więcej o korzystaniu z usługi App Configuration, przejdź do następnego samouczka, w którym zaprezentowano uwierzytelnianie.

> [!div class="nextstepaction"]
> [Tożsamości zarządzane na potrzeby integracji zasobów platformy Azure](./integrate-azure-managed-service-identity.md)
