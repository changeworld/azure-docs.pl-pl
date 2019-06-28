---
title: 'Szybki start: Konwertowanie skryptu tekstowego, C# — tłumaczenie tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak transliterować (konwertować) tekst z jednego skryptu na inny przy użyciu platformy .NET Core i interfejsu API REST tłumaczenia tekstu w usłudze Translator. W tym przykładzie tekst w języku japońskim jest transliterowany na alfabet łaciński.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: f09f9081dd535762afd2e26e5e86476eb06f5133
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445252"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-c"></a>Szybki start: Korzystanie z interfejsu API tłumaczenia tekstu w usłudze Translator do transliterowania tekstu z użyciem języka C#

W tym przewodniku Szybki Start dowiesz się, jak transliteracja tekstu (konwersja) z jednego skryptu do innego za pomocą platformy .NET Core (C#), C# 7.1 lub nowszy i interfejsu API tłumaczenia tekstu REST. W tym udostępnionym przykładzie tekst w języku japońskim jest transliterowany na alfabet łaciński.

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
dotnet new console -o transliterate-sample
cd transliterate-sample
```

Pierwsze polecenie wykonuje dwie czynności. Tworzy nową aplikację konsolową platformy .NET i katalog o nazwie `transliterate-sample`. Drugie polecenie zmienia katalog na katalog projektu.

Następnie należy zainstalować element Json.Net. Z poziomu katalogu projektu uruchom:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Wybierz C# wersja językowa

Ten przewodnik Szybki Start wymaga C# 7.1 lub nowszej. Istnieje kilka sposobów, aby zmienić C# wersji dla projektu. W tym przewodniku pokażemy ci jak dostosować `transliterate-sample.csproj` pliku. Dostępne opcje, takie jak zmiana języka w programie Visual Studio, można znaleźć [wybierz C# wersji językowej](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otwórz swój projekt, a następnie otwórz `transliterate-sample.csproj`. Upewnij się, że `LangVersion` jest ustawiony do wersji 7.1 lub nowszej. Jeśli nie ma grupy właściwości w wersji językowej, Dodaj następujące wiersze:

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
public class TransliterationResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}
```

## <a name="create-a-function-to-transliterate-text"></a>Tworzenie funkcji na potrzeby transliteracji tekstu

W ramach `Program` klasy, należy utworzyć funkcję asynchroniczną o nazwie `TransliterateTextRequest()`. Ta funkcja przyjmuje cztery argumenty: `subscriptionKey`, `host`, `route`, i `inputText`.

```csharp
static public async Task TransliterateTextRequest(string subscriptionKey, string host, string route, string inputText)
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
* Wyświetlanie odpowiedzi

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
TransliterationResult[] deserializedOutput = JsonConvert.DeserializeObject<TransliterationResult[]>(result);
// Iterate over the deserialized results.
foreach (TransliterationResult o in deserializedOutput)
{
    Console.WriteLine("Transliterated to {0} script: {1}", o.Script, o.Text);
}
```

Jeśli używasz subskrypcji do wielu usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` w parametry żądania. [Dowiedz się więcej o uwierzytelnianiu przy użyciu wielu usług subskrypcji](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wywołanie instrukcji `TransliterateTextRequest()` w funkcji `Main`. W tym przykładzie jest firma Microsoft transliterating z japoński łaciński skryptu. Znajdź `static void Main(string[] args)` i Zastąp ten kod:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
    string textToTransliterate = @"こんにちは";
    await TransliterateTextRequest(subscriptionKey, host, route, textToTransliterate);
}
```

Można zauważyć, że w `Main`, w przypadku deklarowania `subscriptionKey`, `host`, `route`, a skrypt na transliteracja `textToTransliterate`.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
dotnet run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

Po uruchomieniu przykładu, powinny zostać wyświetlone następujące wypisywane w terminalu:

```bash
Transliterated to latn script: Kon\'nichiwa
```

Ta wiadomość została stworzona od niesformatowanej strukturze JSON, który będzie wyglądać następująco:

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z kodem przykładowym tego przewodnika Szybki start i innych, w tym obejmującym tłumaczenie i identyfikację języka, jak również innymi projektami przykładowymi dla tłumaczenia tekstu w usłudze Translator dostępnymi w usłudze GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Zobacz też

* [Tłumaczenie tekstu](quickstart-csharp-translate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-csharp-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-csharp-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-csharp-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-csharp-sentences.md)
