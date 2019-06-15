---
title: 'Szybki start: Uzyskiwanie długości zdań, C# — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak określać długość zdań przy użyciu platformy .NET Core i interfejsu API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: erhopf
ms.openlocfilehash: 00fe7ce5558672812be7949b8474a403499de94f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123396"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Szybki start: Korzystanie z interfejsu API tłumaczenia tekstu w usłudze Translator do określania długości zdań przy użyciu języka C#

W tym przewodniku Szybki Start dowiesz się, jak określić długości zdania przy użyciu platformy .NET Core C# 7.1 lub nowszej, a interfejs API tekstu usługi Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

* C#7.1 lub nowszej
* [Zestaw SDK platformy .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pakiet NuGet platformy Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-net-core-project"></a>Tworzenie projektu platformy .NET Core

Otwórz nowy wiersz polecenia (lub sesję terminalową), a następnie uruchom następujące polecenia:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Pierwsze polecenie wykonuje dwie czynności. Tworzy nową aplikację konsolową platformy .NET i katalog o nazwie `sentences-sample`. Drugie polecenie zmienia katalog na katalog projektu.

Następnie należy zainstalować element Json.Net. Z poziomu katalogu projektu uruchom:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Wybierz C# wersja językowa

Ten przewodnik Szybki Start wymaga C# 7.1 lub nowszej. Istnieje kilka sposobów, aby zmienić C# wersji dla projektu. W tym przewodniku pokażemy ci jak dostosować `sentences-sample.csproj` pliku. Dostępne opcje, takie jak zmiana języka w programie Visual Studio, można znaleźć [wybierz C# wersji językowej](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otwórz swój projekt, a następnie otwórz `sentences-sample.csproj`. Upewnij się, że `LangVersion` jest ustawiony do wersji 7.1 lub nowszej. Jeśli nie ma grupy właściwości w wersji językowej, Dodaj następujące wiersze:

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

Następnie zamierzamy utworzyć klasę, która jest używana podczas deserializacji odpowiedź JSON zwracane przez interfejs API tekstu usługi Translator.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>Tworzenie funkcji w celu określenia długości zdania

W klasie `Program` utwórz funkcję o nazwie `BreakSentence()`. Ta funkcja przyjmuje cztery argumenty: `subscriptionKey`, `host`, `route`, i `inputText`.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>Serializować żądania przerwania zdania

Następnie należy utworzyć i serializować obiekt JSON obejmujący tekst. Pamiętaj, że możesz przekazać więcej niż jeden obiekt w tablicy `body`.

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
* Wyświetlanie odpowiedzi

Dodaj następujący kod do elementu `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to POST
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
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wywołanie instrukcji `BreakSentenceRequest()` w funkcji `Main`. Znajdź `static void Main(string[] args)` i Zastąp ten kod:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, host, route, breakSentenceText);
}
```

Można zauważyć, że w `Main`, w przypadku deklarowania `subscriptionKey`, `host`, `route`i tekst, aby ocenić `breakSentenceText`.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
dotnet run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

Po uruchomieniu przykładu, powinny zostać wyświetlone następujące wypisywane w terminalu:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Ta wiadomość została stworzona od niesformatowanej strukturze JSON, który będzie wyglądać następująco:

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
    }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z kodem przykładowym z tego przewodnika Szybki start i innych, dotyczącym między innymi transliteracji i rozpoznawania języka, a także z innymi projektami przykładowymi dotyczącymi tłumaczenia tekstów w usłudze Translator w repozytorium GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Zobacz też

* [Tłumaczenie tekstu](quickstart-csharp-translate.md)
* [Transliteracja tekstu](quickstart-csharp-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-csharp-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-csharp-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-csharp-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-csharp-sentences.md)
