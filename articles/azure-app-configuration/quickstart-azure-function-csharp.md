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
ms.openlocfilehash: 914fe30e0b474f5d2c3ca14be6a6eb2e13cfdbf8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706440"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Szybki start: Tworzenie funkcji platformy Azure przy użyciu usługi App Configuration

Usługa Azure App Configuration to zarządzana usługa konfiguracji na platformie Azure. Umożliwia on łatwe przechowywać i zarządzać wszystkie ustawienia aplikacji w jednym miejscu, który jest oddzielony od kodu. Ten przewodnik Szybki Start pokazano, jak zintegrować usługę z funkcji platformy Azure. 

Wykonaj kroki w tym przewodniku Szybki Start, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

![Lokalny kompletny przewodnik Szybki Start](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start, instaluje [programu Visual Studio 2017](https://visualstudio.microsoft.com/vs). Upewnij się, że obciążenie **programowanie na platformie Azure** jest również instalowane. Zainstaluj również [najnowsze narzędzia usługi Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **Explorer klucz/wartość** > **+ Utwórz** można dodać następujące pary klucz wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pozostaw **etykiety** i **typu zawartości** puste, aby teraz.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Łączenie do sklepu z aplikacjami konfiguracji

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na **Przeglądaj** kartę, wyszukiwanie i dodaj następujące pakiety NuGet do projektu. Jeśli nie można go znaleźć, wybrać **Uwzględnij wersję wstępną** pole wyboru.

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 1.0.0 preview or later
    ```

2. Otwórz *Function1.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji programu .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Zaktualizuj metodę `Run`, tak aby używała usługi App Configuration przez wywołanie elementu `builder.AddAzureAppConfiguration()`.

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

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Korzystając z wiersza polecenia Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby umożliwić zmiana zaczęła obowiązywać:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Jeśli korzystasz z systemem macOS lub Linux, uruchom następujące polecenie:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Aby przetestować funkcję, naciśnij klawisz F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania zestawu narzędzi **Azure Functions Core (CLI)**. Również może być konieczne włączenie wyjątku zapory, aby umożliwić narzędziom obsługę żądań HTTP.

3. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Debugowanie funkcji w programie VS z przewodnika Szybki start](./media/quickstarts/function-visual-studio-debugging.png)

4. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedzi w przeglądarce, aby lokalne żądanie GET zwróconą przez funkcję.

    ![Lokalne uruchamianie funkcji z przewodnika Szybki start](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start został utworzony nowy magazyn konfiguracji aplikacji i jej za pomocą funkcji platformy Azure. Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do następnego samouczka, który demonstruje uwierzytelniania.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanych](./howto-integrate-azure-managed-service-identity.md)
