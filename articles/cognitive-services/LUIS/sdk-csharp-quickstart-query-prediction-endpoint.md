---
title: 'Szybki Start C# : punkt końcowy przewidywania zapytań zestawu SDK — Luis'
titleSuffix: Azure Cognitive Services
description: W C# tym przewodniku szybki start pokazano, jak za pomocą zestawu SDK wysyłać wypowiedź użytkownika do aplikacji Luis platformy Azure Cognitive Services i otrzymywać przewidywania.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 37e7224776efa63b39a671a3b3a79ea6c204a9dc
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611100"
---
# <a name="quickstart-query-v3-prediction-endpoint-with-c-net-sdk"></a>Szybki Start: zapytanie dotyczące punktu końcowego C# przewidywania v3 przy użyciu zestawu SDK platformy .NET

Użyj zestawu SDK platformy .NET, znajdującego się w pakiecie [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), aby wysłać wypowiedź użytkownika do usługi Language Understanding (LUIS) i odebrać przewidywanie intencji użytkownika.

Użyj biblioteki klienta przewidywania Language Understanding (LUIS) dla platformy .NET, aby:

* Pobierz prognozowanie według miejsca

[Dokumentacja referencyjna](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [ C# |  | ](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x) [kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)

## <a name="prerequisites"></a>Wymagania wstępne

* Language Understanding (LUIS) — konto portalu — [Utwórz je bezpłatnie](https://www.luis.ai)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

Szukasz dodatkowej dokumentacji?

 * [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

Przy użyciu klucza i nazwy zasobu Utwórz dwa zmienne środowiskowe do uwierzytelniania:

* `LUIS_PREDICTION_KEY` — klucz zasobu do uwierzytelniania żądań.
* `LUIS_ENDPOINT_NAME` — nazwa zasobu skojarzona z kluczem.

Skorzystaj z instrukcji dotyczących systemu operacyjnego.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edytuj `.bash_profile`i Dodaj zmienną środowiskową:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="create-a-new-c-application"></a>Utwórz nową C# aplikację

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia dotnet `new`, aby utworzyć nową aplikację konsolową o nazwie `language-understanding-quickstart`. To polecenie tworzy prosty projekt "Hello world" C# z pojedynczym plikiem źródłowym: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Zmień katalog na nowo utworzony folder aplikacji.

1. Aplikację można skompilować przy użyciu:

    ```dotnetcli
    dotnet build
    ```

    Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta przewidywania środowiska uruchomieniowego Language Understanding (LUIS) dla platformy .NET przy użyciu następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Klient predykcyjny w środowisku uruchomieniowym Language Understanding (LUIS) to obiekt [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure, który zawiera klucz zasobu.

Po utworzeniu klienta Użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Prognozowanie według [przemieszczania lub miejsca produktu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Prognozowanie według [wersji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej środowiska uruchomieniowego Language Understanding (LUIS) dla platformy .NET:

* [Prognozowanie według miejsca](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Zastąp istniejący kod `using` następującymi dyrektywami `using`:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

1. Utwórz zmienne dla klucza, nazwy i identyfikatora aplikacji:

    Zmienne służące do zarządzania kluczem przewidywania pobierane ze zmiennej środowiskowej o nazwie `LUIS_PREDICTION_KEY`. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie załadować Edytor, środowisko IDE lub powłokę, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

    Utwórz zmienną do przechowywania nazwy zasobu `LUIS_ENDPOINT_NAME`.

    Utwórz zmienną dla identyfikatora aplikacji jako zmienną środowiskową o nazwie `LUIS_APP_ID`. Ustaw zmienną środowiskową na publiczną aplikację IoT:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Utwórz obiekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) .

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Pobierz prognozowanie z środowiska uruchomieniowego

Dodaj następującą metodę, aby utworzyć żądanie do środowiska uruchomieniowego przewidywania.

Wypowiedź użytkownika jest częścią obiektu [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) .

Metoda **GetSlotPredictionAsync** wymaga kilku parametrów, takich jak identyfikator aplikacji, nazwa gniazda, obiekt żądania prognozowania w celu spełnienia żądania. Inne opcje, takie jak verbose, pokazują wszystkie intencje i dzienniki są opcjonalne.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Kod główny do prognozowania

Użyj następującej metody Main, aby powiązać zmienne i metody w celu uzyskania prognozowania.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia `dotnet run` z katalogu aplikacji.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po wykonaniu prognoz Wyczyść prace z tego przewodnika Szybki Start, usuwając plik program.cs i jego podkatalogi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zestawie SDK platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) i zapoznaj się z [dokumentacją referencyjna platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet).

> [!div class="nextstepaction"]
> [Samouczek: kompilowanie aplikacji LUIS w celu określenia zamiarów użytkownika](luis-quickstart-intents-only.md)