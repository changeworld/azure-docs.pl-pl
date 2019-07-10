---
title: 'Szybki start: Tłumaczenie tekstu, C# — tłumaczenie tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przetłumaczysz tekst z jednego języka na inny przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka C#.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 5b5451fc1c4b8127c4e2a561e25fe3eb730354a8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705641"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-c"></a>Szybki start: Tłumaczenie ciągu za pomocą interfejsu API tłumaczenia tekstu w usłudze Translator oraz języka C#

W tym przewodniku Szybki Start dowiesz się, jak przekształcać ciąg tekstowy z angielski, niemiecki, włoski, japoński i tajski przy użyciu platformy .NET Core C# 7.1 lub nowszy i interfejsu API tłumaczenia tekstu REST.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

>[!TIP]
> Jeśli chcesz od razu zobaczyć cały kod, kod źródłowy dla tego przykładu znajduje się na [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

## <a name="prerequisites"></a>Wymagania wstępne

* C#7.1 lub nowszej
* [Zestaw SDK platformy .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pakiet NuGet platformy Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-net-core-project"></a>Tworzenie projektu platformy .NET Core

Otwórz nowy wiersz polecenia (lub sesję terminalową), a następnie uruchom następujące polecenia:

```console
dotnet new console -o translate-sample
cd translate-sample
```

Pierwsze polecenie wykonuje dwie czynności. Tworzy nową aplikację konsolową platformy .NET i katalog o nazwie `translate-sample`. Drugie polecenie zmienia katalog na katalog projektu.

Następnie należy zainstalować element Json.Net. Z poziomu katalogu projektu uruchom:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Wybierz C# wersja językowa

Ten przewodnik Szybki Start wymaga C# 7.1 lub nowszej. Istnieje kilka sposobów, aby zmienić C# wersji dla projektu. W tym przewodniku pokażemy ci jak dostosować `translate-sample.csproj` pliku. Dostępne opcje, takie jak zmiana języka w programie Visual Studio, można znaleźć [wybierz C# wersji językowej](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otwórz swój projekt, a następnie otwórz `translate-sample.csproj`. Upewnij się, że `LangVersion` jest ustawiony do wersji 7.1 lub nowszej. Jeśli nie ma grupy właściwości w wersji językowej, Dodaj następujące wiersze:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Dodawanie wymaganych obszarów nazw do projektu

Uruchomione wcześnie polecenie `dotnet new console` spowodowało utworzenie projektu obejmującego plik `Program.cs`. W tym pliku umieścisz kod aplikacji. Otwórz plik `Program.cs` i zastąp istniejące instrukcje using. Te instrukcje gwarantują dostęp do wszystkich typów wymaganych do skompilowania i uruchomienia przykładowej aplikacji.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Tworzenie klasy dla odpowiedź w formacie JSON

Następnie zamierzamy utworzyć zestaw klas, które są używane podczas deserializacji odpowiedź JSON zwracane przez interfejs API tekstu usługi Translator.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TranslationResult
{
    public DetectedLanguage DetectedLanguage { get; set; }
    public TextResult SourceText { get; set; }
    public Translation[] Translations { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}

public class TextResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}

public class Translation
{
    public string Text { get; set; }
    public TextResult Transliteration { get; set; }
    public string To { get; set; }
    public Alignment Alignment { get; set; }
    public SentenceLength SentLen { get; set; }
}

public class Alignment
{
    public string Proj { get; set; }
}

public class SentenceLength
{
    public int[] SrcSentLen { get; set; }
    public int[] TransSentLen { get; set; }
}
```

## <a name="create-a-function-to-translate-text"></a>Tworzenie funkcji na potrzeby tłumaczenia tekstu

W ramach `Program` klasy, należy utworzyć funkcję asynchroniczną o nazwie `TranslateTextRequest()`. Ta funkcja przyjmuje cztery argumenty: `subscriptionKey`, `host`, `route`, i `inputText`.

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializować żądania tłumaczenia

Następnie musimy utworzyć i serializować obiekt JSON obejmujący tekst, który chcesz przetłumaczyć. Należy pamiętać, można przekazać więcej niż jeden obiekt w `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Tworzenie wystąpienia klienta i wysyłanie żądania

W tych wierszach są tworzone wystąpienia elementów `HttpClient` i `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Konstruowanie żądania i wyświetlanie odpowiedzi

Wewnątrz elementu `HttpRequestMessage` wykonasz następujące czynności:

* Deklarowanie metody HTTP
* Konstruowanie identyfikatora URI żądania
* Wstawianie treści żądania (serializowanego obiektu JSON)
* Dodawanie wymaganych nagłówków
* Wykonywanie żądania asynchronicznego
* Drukuj odpowiedzi, używając klasy, która została utworzona wcześniej

Dodaj następujący kod do elementu `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TranslationResult[] deserializedOutput = JsonConvert.DeserializeObject<TranslationResult[]>(result);
// Iterate over the deserialized results.
foreach (TranslationResult o in deserializedOutput)
{
    // Print the detected input language and confidence score.
    Console.WriteLine("Detected input language: {0}\nConfidence score: {1}\n", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    // Iterate over the results and print each translation.
    foreach (Translation t in o.Translations)
    {
        Console.WriteLine("Translated to {0}: {1}", t.To, t.Text);
    }
}
```

Jeśli używasz subskrypcji do wielu usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` w parametry żądania. [Dowiedz się więcej o uwierzytelnianiu przy użyciu wielu usług subskrypcji](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wywołanie instrukcji `TranslateTextRequest()` w funkcji `Main`. W tym przykładzie firma Microsoft jest tłumaczenia na język niemiecki (`de`), włoski (`it`), japoński (`ja`), a tajski (`th`). Znajdź `static void Main(string[] args)` i Zastąp ten kod:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, host, route, textToTranslate);
}
```

Można zauważyć, że w `Main`, w przypadku deklarowania `subscriptionKey`, `host`, i `route`. Ponadto w przypadku monitowania użytkownika o dane wejściowe z `Console.Readline()` i przypisywania wartości do `textToTranslate`.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
dotnet run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

Po uruchomieniu przykładu, powinny zostać wyświetlone następujące wypisywane w terminalu:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Ta wiadomość została stworzona od niesformatowanej strukturze JSON, który będzie wyglądać następująco:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      },
      {
        "text": "ハローワールド！",
        "to": "ja"
      },
      {
        "text": "หวัดดีชาวโลก!",
        "to": "th"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Przyjrzyj się dokumentacja interfejsu API, aby dowiedzieć się wszystkiego, co można zrobić za pomocą interfejsu API tłumaczenia tekstu.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Zobacz także

* [Transliteracja tekstu](quickstart-csharp-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-csharp-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-csharp-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-csharp-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-csharp-sentences.md)
