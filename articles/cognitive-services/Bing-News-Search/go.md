---
title: 'Szybki start: Wiadomości przy użyciu interfejsu API REST wyszukiwania wiadomości Bing i języka Go'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak uzyskać wyniki wiadomości z interfejsu API wyszukiwania wiadomości Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: rosh
ms.openlocfilehash: 295c32c1e14dc6a69a37040f92d27a6862359228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861356"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Szybki start: Pobierz wyniki wiadomości przy użyciu interfejsu API REST wyszukiwania wiadomości Bing i języka Go

Ten przewodnik Szybki Start używa języka Go do wywołania interfejsu API wyszukiwania wiadomości Bing. Wyniki obejmują nazwy i adresy URL wiadomości źródeł identyfikowane przez ciąg zapytania.

## <a name="prerequisites"></a>Wymagania wstępne
* Zainstaluj [Przejdź plików binarnych](https://golang.org/dl/)
* Zainstaluj bibliotekę spew z rzeczywistym użyciem dla niego pretty drukarki, aby wyświetlić wyniki
    * Zainstaluj ten libarary: `$ go get -u https://github.com/davecgh/go-spew`

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-libraries"></a>Tworzenie projektu i Importuj biblioteki

Utwórz nowy projekt z rzeczywistym użyciem w IDE lub edytora. Następnie zaimportuj `net/http` w przypadku żądań `ioutil` odczytać odpowiedzi i `encoding/json` do obsługi tekstu JSON wyników. Biblioteka spew z rzeczywistym użyciem jest potrzebny do analizowania danych JSON. 

```
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>Tworzenie struktury, aby sformatować wyniki wyszukiwania wiadomości

Struktura `NewsAnswer` formatuje dane zawarte w odpowiedzi. Odpowiedź JSON jest kategoryzowanie i dość złożony.  Następującą implementacją obejmuje podstawowe elementy.

```
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
        } `json: "image"` 
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Deklarowanie funkcji main i definiowanie zmiennych  

Poniższy kod deklaruje funkcję main i przypisuje zmienne wymagane. Upewnij się, że punkt końcowy jest poprawny, i zamień wartość `token` na odpowiedni klucz subskrypcji ze swojego konta platformy Azure.

```
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>Query i header

Dodaj zapytanie ciągu i dostępu do kluczy nagłówka

```
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>Pobieranie żądania

Tworzenie klienta i Wyślij żądanie Get. 

```
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>Wysyłanie żądania

Wyślij żądanie i przeczytaj wyniki za pomocą `ioutil`.

```
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
resbody, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>Obsługa odpowiedzi

`Unmarshall` Funkcja wyodrębniania informacji z tekstu JSON zwracana przez interfejs API wyszukiwania wiadomości.  Można wyświetlać węzły z wyników za pomocą `go-spew` pretty drukarki.

```
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>Wyniki

Wyniki zawierają nazwę i adres URL każdego wyniku.

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Co to jest wyszukiwanie wiadomości Bing](search-the-web.md)
