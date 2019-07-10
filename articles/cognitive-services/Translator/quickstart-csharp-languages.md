---
title: 'Szybki start: Uzyskiwanie listy obsługiwanych języków, C# — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start uzyskasz listę języków obsługiwanych na potrzeby tłumaczenia, transliteracji i wyszukiwania w słowniku przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 4a1be3cd71a32210db8b3d5ef835af7b2d50667f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705668"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-c"></a>Szybki start: Uzyskiwanie listy obsługiwanych języków za pomocą interfejsu API tłumaczenia tekstu w usłudze Translator przy użyciu języka C#

W tym przewodniku Szybki start uzyskasz listę języków obsługiwanych na potrzeby tłumaczenia, transliteracji i wyszukiwania w słowniku przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.

>[!TIP]
> Jeśli chcesz od razu zobaczyć cały kod, kod źródłowy dla tego przykładu znajduje się na [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw SDK platformy .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pakiet NuGet platformy Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów

## <a name="create-a-net-core-project"></a>Tworzenie projektu platformy .NET Core

Otwórz nowy wiersz polecenia (lub sesję terminalową), a następnie uruchom następujące polecenia:

```console
dotnet new console -o languages-sample
cd languages-sample
```

Pierwsze polecenie wykonuje dwie czynności. Tworzy nową aplikację konsolową platformy .NET i katalog o nazwie `languages-sample`. Drugie polecenie zmienia katalog na katalog projektu.

Następnie należy zainstalować element Json.Net. Z poziomu katalogu projektu uruchom:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Dodawanie wymaganych obszarów nazw do projektu

Uruchomione wcześnie polecenie `dotnet new console` spowodowało utworzenie projektu obejmującego plik `Program.cs`. W tym pliku umieścisz kod aplikacji. Otwórz plik `Program.cs` i zastąp istniejące instrukcje using. Te instrukcje gwarantują dostęp do wszystkich typów wymaganych do skompilowania i uruchomienia przykładowej aplikacji.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-get-a-list-of-languages"></a>Tworzenie funkcji w celu uzyskania listy języków

W klasie `Program` utwórz funkcję o nazwie `GetLanguages`. Ta klasa hermetyzuje kod używany do wywołania zasobu Languages i wyświetla wynik w konsoli.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-host-name-and-path"></a>Ustawianie nazwy hosta i ścieżki

Dodaj następujące wiersze do funkcji `GetLanguages`.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
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
* Dodawanie wymaganych nagłówków
* Wykonywanie żądania asynchronicznego
* Wyświetlanie odpowiedzi

Dodaj następujący kod do elementu `HttpRequestMessage`:

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(host + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Jeśli używasz subskrypcji do wielu usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` w parametry żądania. [Dowiedz się więcej o uwierzytelnianiu przy użyciu wielu usług subskrypcji](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Aby wydrukować odpowiedzi z "Pretty Print" (formatowanie odpowiedzi), należy dodać tę funkcję do klasy Program:
```
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wywołanie instrukcji `GetLanguages()` w funkcji `Main`. Znajdź kod `static void Main(string[] args)` i dodaj następujące wiersze:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
dotnet run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

Znajdź skrót kraju/regionu, w tym [listę języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Przyjrzyj się dokumentacja interfejsu API, aby dowiedzieć się wszystkiego, co można zrobić za pomocą interfejsu API tłumaczenia tekstu.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Zobacz także

* [Tłumaczenie tekstu](quickstart-csharp-translate.md)
* [Transliteracja tekstu](quickstart-csharp-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-csharp-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-csharp-dictionary.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-csharp-sentences.md)
