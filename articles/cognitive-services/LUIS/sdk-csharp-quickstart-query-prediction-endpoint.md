---
title: 'Szybki start: C#Punkt końcowy przewidywania zapytań zestawu SDK — LUIS'
titleSuffix: Azure Cognitive Services
description: Użyj zestawu SDK języka C# w celu wysłania wypowiedzi użytkownika do usługi LUIS i odebrania przewidywania.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: c0b534848232d60929722e2036f69f4b6e670a4a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563161"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Szybki start: Wysyłanie zapytań do punktu końcowego przewidywania za pomocą zestawu SDK języka C# .NET

Użyj zestawu SDK platformy .NET, znajdującego się w pakiecie [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), aby wysłać wypowiedź użytkownika do usługi Language Understanding (LUIS) i odebrać przewidywanie intencji użytkownika. 

Ten przewodnik Szybki start wysyła wypowiedź użytkownika, taką jak `turn on the bedroom light`, do publicznej aplikacji usługi Language Understanding, a następnie odbiera przewidywanie i wyświetla najwyżej ocenianą intencję `HomeAutomation.TurnOn` oraz jednostkę `HomeAutomation.Room` znalezioną w wypowiedzi. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Język programowania C# (dołączony do programu VS Community 2017)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> Kompletne rozwiązanie jest dostępne w repozytorium GitHub [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime).

Szukasz dodatkowej dokumentacji?

 * [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Pobieranie klucza usług Cognitive Services lub usługi Language Understanding

Aby móc używać aplikacji publicznej do automatyzacji domu, potrzebny jest prawidłowy klucz dla przewidywań punktu końcowego. Możesz skorzystać z klucza usług Cognitive Services (utworzonego poniżej za pomocą interfejsu wiersza polecenia platformy Azure), który jest ważny w przypadku wielu usług poznawczych, lub z klucza usługi `Language Understanding`. 

Użyj następującego [polecenia interfejsu wiersza polecenia platformy Azure, aby utworzyć klucz usług Cognitive Services](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Tworzenie projektu platformy .NET Core

Utwórz projekt konsoli .NET Core w programie Visual Studio Community 2017.

1. Otwórz program Visual Studio Community 2017.
1. Utwórz nowy projekt: w sekcji **Visual C#** wybierz pozycję **Console App (.NET Core)** (Aplikacja konsoli — .Net Core).
1. Wprowadź nazwę projektu: `QueryPrediction`. Pozostałe wartości pozostaw domyślne i wybierz przycisk **OK**.
    Spowoduje to utworzenie prostego projektu z plikiem kodu podstawowego o nazwie **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Dodawanie zestawu SDK za pomocą pakietu NuGet

1. W **Eksploratora rozwiązań** w widoku drzewa wybierz projekt o nazwie **QueryPrediction**, a następnie kliknij prawym przyciskiem myszy. W menu wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj** i wprowadź ciąg `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Kiedy zostaną wyświetlone informacje o pakiecie, wybierz pozycję **Instaluj**, aby zainstalować pakiet w projekcie. 
1. Dodaj następujące instrukcje _using_ w górnej części pliku **Program.cs**. Nie usuwaj istniejącej instrukcji _using_ dla elementu `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Tworzenie nowej metody dla przewidywania

Utwórz nową metodę, `GetPrediction`, aby wysłać zapytanie do punktu końcowego przewidywania zapytania. Ta metoda utworzy i skonfiguruje wszystkie potrzebne obiekty, a następni zwróci element `Task` z wynikami przewidywania [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet). 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Tworzenie obiektu poświadczeń

Dodaj następujący kod do metody `GetPrediction`, aby utworzyć poświadczenia klienta za pomocą klucza usług Cognitive Services.

Zastąp ciąg `<REPLACE-WITH-YOUR-KEY>` za pomocą regionu klucza usług Cognitive Services. Klucz znajduje się w witrynie [Azure Portal](https://portal.azure.com) na stronie Klucze dla tego zasobu.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Tworzenie klienta usługi Language Understanding

W metodzie `GetPrediction`, za poprzednim kodem, dodaj następujący kod, aby użyć nowych poświadczeń, tworząc obiekt klienta [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___). 

Zastąp ciąg `<REPLACE-WITH-YOUR-KEY-REGION>` regionem swojego klucza, na przykład `westus`. Region klucza znajduje się w witrynie [Azure Portal](https://portal.azure.com) na stronie Przegląd dla tego zasobu.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Ustawianie parametrów zapytania

W metodzie `GetPrediction`, za poprzednim kodem, dodaj następujący kod, aby ustawić parametry zapytania.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Punkt końcowy przewidywania zapytania

W metodzie `GetPrediction`, za poprzednim kodem, dodaj następujący kod, aby ustawić parametry zapytania:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Wyświetlanie wyników przewidywania

Zmień metodę **Main**, aby wywoływać nową metodę `GetPrediction` i zwracać wyniki przewidywania:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Uruchamianie projektu

Skompiluj projekt w programie Studio i uruchom go, aby wyświetlić wyniki zapytania:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zestawie SDK platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) i zapoznaj się z [dokumentacją referencyjna platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Samouczek: Tworzenie aplikacji usługi LUIS umożliwiającej określanie intencji użytkownika](luis-quickstart-intents-only.md) 