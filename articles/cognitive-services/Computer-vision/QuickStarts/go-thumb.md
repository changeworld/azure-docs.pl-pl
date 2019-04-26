---
title: 'Szybki start: generowanie miniatur — REST, Go'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu języka Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4f13a384cb32596d191625c8f13bc914686c6297
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60408924"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-go-in-computer-vision"></a>Szybki start: generowanie miniatury przy użyciu interfejsu API REST i języka Go podczas przetwarzania obrazów

W tym przewodniku Szybki Start generowanie miniatur z obrazu przy użyciu interfejsu API REST przetwarzania obrazów. Należy określić wysokość i szerokość, które mogą różnić się w proporcjach z obrazu wejściowego. Interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i na jego podstawie wygenerować współrzędne przycinania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć zainstalowany język [Go](https://golang.org/dl/).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatnej wersji próbnej klucza z [spróbuj usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub, postępuj zgodnie z instrukcjami w [Tworzenie konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) subskrybować przetwarzania obrazów, i Uzyskaj klucz.

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie przykładowego kodu

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora tekstów.
1. W razie potrzeby wprowadź następujące zmiany w kodzie:
    1. Zastąp wartość `subscriptionKey` kluczem subskrypcji.
    1. W razie potrzeby zastąp wartość `uriBase` adresem URL punktu końcowego dla metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) z regionu platformy Azure, z którego uzyskano klucze subskrypcji.
    1. Opcjonalnie zastąp wartość `imageUrl` adresem URL innego obrazu, z którego chcesz wygenerować miniaturę.
1. Zapisz kod jako plik z rozszerzeniem `.go`. Na przykład `get-thumbnail.go`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia uruchom polecenie `go build`, aby skompilować pakiet na podstawie pliku. Na przykład `go build get-thumbnail.go`.
1. W wierszu polecenia uruchom skompilowany pakiet. Na przykład `get-thumbnail`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace <Subscription Key> with your valid subscription key.
    const subscriptionKey = "<Subscription Key>"

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź zawierająca dane binarne obrazu miniatury. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak.

## <a name="next-steps"></a>Kolejne kroki

Poznaj interfejs API przetwarzania obrazów do analizowania obrazu, wykrywanie osobistości i charakterystycznych elementów krajobrazu, tworzenie miniatur i wyodrębniania tekstu drukowanego lub ręcznie. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
