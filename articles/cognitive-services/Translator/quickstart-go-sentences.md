---
title: 'Szybki start: uzyskiwanie długości zdań — tłumaczenie tekstu w usłudze Translator, Go'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start określisz długości zdań w tekście przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka Go.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 34b30179d674a42f6c9c2c358c0feb6e083089f6
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129458"
---
# <a name="quickstart-get-sentence-lengths-with-go"></a>Szybki start: uzyskiwanie długości zdania przy użyciu języka Go

W tym przewodniku Szybki start odnajdziesz długość zdań w tekście przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zainstalować [dystrybucję języka Go](https://golang.org/doc/install), aby uruchomić ten kod. Kod przykładowy korzysta tylko z bibliotek **core**, a więc nie istnieją żadne zewnętrzne zależności.

Aby korzystać z interfejsu API tłumaczenia tekstu w usłudze Translator, potrzebny jest również klucz subskrypcji. Zobacz [How to sign up for the Translator Text API (Jak zarejestrować się w interfejsie API tłumaczenia tekstu w usłudze Translator)](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Żądanie BreakSentence

Poniższy kod dzieli tekst źródłowy na zdania przy użyciu metody [BreakSentence](./reference/v3-0-break-sentence.md).

1. Utwórz nowy projekt w języku Go w ulubionym edytorze kodu.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `subscriptionKey` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Zapisz plik z rozszerzeniem „go”.
5. Otwórz wiersz polecenia na komputerze, na którym zainstalowano środowisko języka Go.
6. Skompiluj plik, na przykład za pomocą polecenia „go build quickstart-sentences.go”.
7. Uruchom plik, na przykład „quickstart-sentences”.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/breaksentence?api-version=3.0"

    const uri = uriBase + uriPath

    const text = "How are you? I am fine. What did you do today?"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="breaksentence-response"></a>Odpowiedź na żądanie BreakSentence

Po pomyślnym przetworzeniu żądania jest zwracana odpowiedź w formacie JSON, jak pokazano na poniższym przykładzie:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pakietami w języku Go dla interfejsów API usług Cognitive Services na stronie [Azure SDK for Go (Zestaw Azure SDK dla języka Go)](https://github.com/Azure/azure-sdk-for-go) w repozytorium GitHub.

> [!div class="nextstepaction"]
> [Explore Go packages on GitHub (Zapoznaj się z pakietami dla języka Go w repozytorium GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
