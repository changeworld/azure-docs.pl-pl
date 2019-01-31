---
title: 'Szybki start: uzyskiwanie obsługiwanych języków, Go — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start uzyskasz listę języków obsługiwanych na potrzeby tłumaczenia, transliteracji, wyszukiwania w słowniku oraz przykładów przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka Go.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: erhopf
ms.openlocfilehash: 45dcd87910e0dbfc57aa09751cbdaa7a043d7cf1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226659"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-go"></a>Szybki start: uzyskiwanie listy obsługiwanych języków za pomocą interfejsu API tłumaczenia tekstu w usłudze Translator przy użyciu języka Go

W tym przewodniku Szybki start dowiesz się, jak utworzyć żądanie GET, które zwraca listę obsługiwanych języków przy użyciu języka Go i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Go](https://golang.org/doc/install)
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt w języku Go przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `get-languages.go`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Tworzenie funkcji main

Ten przykładowy kod spróbuje odczytać klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator ze zmiennej środowiskowej `TRANSLATOR_TEXT_KEY`. Jeśli nie chcesz korzystać ze zmiennych środowiskowych, możesz ustawić element `subscriptionKey` jako ciąg i oznaczyć instrukcję warunkową jako komentarz.

Skopiuj ten kod do projektu:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our getLanguages function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    getLanguages(subscriptionKey)
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>Tworzenie funkcji w celu uzyskania listy obsługiwanych języków

Utwórzmy funkcję w celu uzyskania listy obsługiwanych języków. Ta funkcja będzie przyjmować jeden argument — Twój klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator.

```go
func getLanguages(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Następnie utwórzmy adres URL. Adres URL został utworzony przy użyciu metod `Parse()` i `Query()`.

Skopiuj ten kod do funkcji `getLanguages`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/languages?api-version=3.0")
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: Languages](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) (Interfejs API 3.0 tłumaczenia tekstu w usłudze Translator: języki).

## <a name="build-the-request"></a>Tworzenie żądania

Teraz, gdy treść żądania została zakodowana jako dane JSON, możesz utworzyć żądanie POST i wywołać interfejs API tłumaczenia tekstu w usłudze Translator.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>Obsługa i wyświetlanie odpowiedzi

Dodaj następujący kod do funkcji `getLanguages`, aby zdekodować odpowiedź JSON, a następnie sformatować i wyświetlić wynik.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
go run get-languages.go
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Przykładowa odpowiedź

Po pomyślnym przetworzeniu żądania jest zwracana odpowiedź w formacie JSON, jak pokazano na poniższym przykładzie:

```json
{
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
  },
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
  }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pakietami w języku Go dla interfejsów API usług Cognitive Services na stronie [Azure SDK for Go (Zestaw Azure SDK dla języka Go)](https://github.com/Azure/azure-sdk-for-go) w repozytorium GitHub.

> [!div class="nextstepaction"]
> [Explore Go packages on GitHub (Zapoznaj się z pakietami dla języka Go w repozytorium GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Zobacz też

Dowiedz się, jak używać interfejsu API tłumaczenia tekstu w usłudze Translator w następujących celach:

* [Tłumaczenie tekstu](quickstart-go-translate.md)
* [Transliteracja tekstu](quickstart-go-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-go-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-go-dictionary.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-go-sentences.md)
