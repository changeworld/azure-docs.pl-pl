---
title: Przewodnik Szybki start dla języka Go dotyczący tworzenia miniatur przy użyciu interfejsu API przetwarzania obrazów | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę na podstawie obrazu, korzystając z funkcji przetwarzania obrazów i języka Go w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 02ac30d295b0637b97187dbb7e1fbb1b10a2972b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772248"
---
# <a name="quickstart-generate-a-thumbnail---rest-go"></a>Szybki start: generowanie miniatur — REST, Go

W tym przewodniku Szybki start wygenerujesz miniaturę na podstawie obrazu, korzystając z interfejsu API przetwarzania obrazów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Żądanie Get Thumbnail

[Metoda Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) umożliwia wygenerowanie miniatury obrazu. Należy określić wysokość i szerokość, które mogą mieć inny współczynnik proporcji niż obraz wejściowy. Interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i wygenerować współrzędne przycinania na podstawie tego obszaru.

Aby uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zmień wartość `uriBase` na lokalizację, w której przechowywane są klucze subskrypcji, jeśli jest to konieczne.
1. Opcjonalnie możesz zmienić wartość `imageUrl` na obraz, który chcesz przeanalizować.
1. Zapisz plik z rozszerzeniem `.go`.
1. Otwórz wiersz polecenia na komputerze, na którym zainstalowano środowisko języka Go.
1. Skompiluj plik, na przykład za pomocą polecenia: `go build get-thumbnail.go`.
1. Uruchom plik, na przykład: `get-thumbnail`.

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
    // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
    const subscriptionKey = "<Subscription Key>"

    // You must use the same location in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URL below with "westus".
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the Http client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the Post request, passing the image URL in the request body
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

    // Parse the Json data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the Json result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="get-thumbnail-response"></a>Odpowiedź na żądanie Get Thumbnail

Po pomyślnym przetworzeniu żądania jest zwracana odpowiedź zawierająca dane binarne obrazu miniatury. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsami API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Poznaj interfejsy API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
