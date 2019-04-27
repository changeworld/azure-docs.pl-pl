---
title: 'Szybki start: przeprowadzanie wyszukiwania w Internecie za pomocą języka Go — interfejs API REST wyszukiwania w sieci Web Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API wyszukiwania w sieci Web Bing przy użyciu języka Go i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.reviewer: nhoyadx@gmail.com, v-gedod, erhopf
ms.custom: seodec2018
ms.openlocfilehash: b7bf73c2948b302ec8294b7dc2ff2dc8f220c91b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648452"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-go"></a>Szybki start: Wyszukiwanie w Internecie za pomocą interfejsu API REST wyszukiwania w sieci Web Bing i języka Go

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania w Internecie Bing i odebranie odpowiedzi JSON. Tę aplikację z rzeczywistym użyciem wysyła żądanie wyszukiwania do interfejsu API i pokazuje odpowiedzi. Chociaż ta aplikacja jest napisana w języku Go, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Wymagania wstępne
Oto kilka rzeczy, które są potrzebne przed rozpoczęciem tego przewodnika Szybki start:

* [Pliki binarne języka Go](https://golang.org/dl/)
* Klucz subskrypcji

Ten przewodnik Szybki start wymaga jedynie bibliotek **podstawowych**, nie są używane żadne zależności zewnętrzne.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]  

## <a name="create-a-project-and-import-core-libraries"></a>Tworzenie projektu i importowanie bibliotek podstawowych

Utwórz nowy projekt Go w ulubionym środowisku IDE lub edytorze. Następnie zaimportuj pakiety: `net/http` do obsługi żądań, `ioutil` do odczytania odpowiedzi, `time` i `encoding/json` do obsługi formatu JSON oraz `fmt` do wyświetlenia danych wyjściowych.

```go
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)
```

## <a name="create-a-struct-to-format-the-search-results"></a>Tworzenie struktury do formatowania wyników wyszukiwania

Struktura `BingAnswer` formatuje dane zawarte w odpowiedzi.

```go
// This struct formats the answers provided by the Bing Web Search API.
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}
```

## <a name="declare-the-main-function-and-define-variables"></a>Deklarowanie funkcji main i definiowanie zmiennych  

Ten kod deklaruje funkcję main i ustawia wymagane zmienne. Upewnij się, że punkt końcowy jest poprawny, i zamień wartość `token` na odpowiedni klucz subskrypcji ze swojego konta platformy Azure. Możesz dostosować zapytanie wyszukiwania, zamieniając wartość `searchTerm`.

```go
// Declare the main function. This is required for all Go programs.
func main() {
// Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

// The remaining code in this quickstart goes in the main function.

}
```

## <a name="construct-a-request"></a>Konstruowanie żądania

Ten kod deklaruje żądanie HTTP, wstawia nagłówek i ładunek oraz tworzy klienta.

```go
// Declare a new GET request.
req, err := http.NewRequest("GET", endpoint, nil)
if err != nil {
    panic(err)
}

// Add the payload to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the request header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

// Instantiate a client.  
client := new(http.Client)
```

## <a name="make-a-request"></a>Wysyłanie żądania

Za pomocą tego kodu wywołaj interfejs API wyszukiwania w sieci Web Bing i po zwróceniu odpowiedzi zamknij połączenie.

```go
// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

// Close the connection.
defer resp.Body.Close()
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}
```

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Pamiętasz utworzoną wcześniej strukturę? Za jej pomocą sformatujemy odpowiedź i wyświetlimy wyniki wyszukiwania.

```go
// Create a new answer.  
ans := new(BingAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
// Iterate over search results and print the result name and URL.
for _, result := range ans.WebPages.Value {
    fmt.Println(result.Name, "||", result.URL)
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest zweryfikowanie kodu i uruchomienie go. Jeśli chcesz porównać swój kod z naszym, oto pełny program:

```go
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)

// The is the struct for the data returned by Bing.
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}

// Verify the endpoint URI and replace the token string with a valid subscription key.  
func main() {
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // Add the payload to the request.  
    param := req.URL.Query()
    param.Add("q", searchTerm)
    req.URL.RawQuery = param.Encode()

    // Insert the request header.  
    req.Header.Add("Ocp-Apim-Subscription-Key", token)

    // Create a new client.  
    client := new(http.Client)

    // Send the request to Bing.  
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    // Close the response.
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(body, &ans)
    if err != nil {
         fmt.Println(err)
    }

    // Iterate over search results and print the result name and URL.
    for _, result := range ans.WebPages.Value {
         fmt.Println(result.Name, "||", result.URL)
    }

}
```

## <a name="sample-response"></a>Przykładowa odpowiedź  

Odpowiedzi z interfejsu API wyszukiwania w sieci Web Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź został sformatowany przy użyciu `BingAnswer` struktury i pokazuje `result.Name` i `result.URL`.

```go
Microsoft Cognitive Services || https://www.microsoft.com/cognitive-services
Cognitive Services | Microsoft Azure || https://azure.microsoft.com/services/cognitive-services/
Cognitive Service Try experience | Microsoft Azure || https://azure.microsoft.com/try/cognitive-services/
What is Microsoft Cognitive Services? | Microsoft Docs || https://docs.microsoft.com/azure/cognitive-services/Welcome
Microsoft Cognitive Toolkit || https://www.microsoft.com/en-us/cognitive-toolkit/
Microsoft Customers || https://customers.microsoft.com/en-us/search?sq=%22Microsoft%20Cognitive%20Services%22&ff=&p=0&so=story_publish_date%20desc
Microsoft Enterprise Services - Microsoft Enterprise || https://enterprise.microsoft.com/en-us/services/
Microsoft Cognitive Services || https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236
Cognitive Services - msdn.microsoft.com || https://msdn.microsoft.com/magazine/mt742868.aspx  
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania w sieci Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
