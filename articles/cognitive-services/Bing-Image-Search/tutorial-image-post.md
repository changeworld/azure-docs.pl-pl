---
title: Przekazywanie obrazu Bing szczegółowe informacje o | Dokumentacja firmy Microsoft
description: Aplikacja konsolowa, która używa interfejsu API wyszukiwania usługi Bing obrazu do przekazywanie obrazu i uzyskać szczegółowe dane obrazu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346924"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Samouczek: Obraz Bing przekazać szczegółowe informacje o

Udostępnia interfejs API wyszukiwania usługi Bing obrazu `POST` opcja przekazywania obrazu i wyszukaj informacje dotyczące obrazu. Tę aplikację konsolową C# przesyła obraz przy użyciu punktu końcowego obraz wyszukiwania, aby uzyskać szczegółowe informacje dotyczące obrazu.
Wyniki są krótko mówiąc, obiektów JSON, takie jak następujące:

![[Wyniki JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

W tym samouczku wyjaśniono:

> [!div class="checklist"]
> * Należy użyć funkcji wyszukiwania obrazu `/details` punktu końcowego w `POST` żądania
> * Określ nagłówki żądania
> * Użyj parametrów adresu URL, aby określić wyników
> * Przekazywanie danych obrazu i wysyłać `POST` żądania
> * Drukowanie wyników JSON do konsoli

## <a name="app-components"></a>Składniki aplikacji

Samouczek aplikacja obejmuje trzy części:

> [!div class="checklist"]
> * Konfiguracja punktu końcowego do określenia punktu końcowego wyszukiwania usługi Bing obrazu i wymagane nagłówki
> * Przekazywanie pliku obrazu dla `POST` żądanie do punktu końcowego
> * Analizowanie wyników JSON, które znajdują się szczegółowe informacje zwrócone z `POST` żądania

## <a name="scenario-overview"></a>Omówienie scenariusza
Brak [trzy obraz wyszukiwania punktów końcowych](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). `/details` Punktu końcowego można użyć `POST` żądania z danymi obrazu w treści żądania.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
`modules` Następujący parametr adresu URL `/details?` Określa, jakiego rodzaju szczegóły wyniki zawierają:
* `modules=All`
* `modules=RecognizedEntities` (osób lub widoczne na obrazie miejsca)

Określ `modules=All` w `POST` żądanie pobrania tekst JSON, który zawiera poniższa lista:
* `bestRepresentativeQuery` -Bing kwerendę, która zwraca obrazy podobne do załadowanego obrazu
* `detectedObjects` przykład obwiedni lub punkty aktywne w obrazie
* `image` metadane
* `imageInsightsToken` -tokenu dla kolejnej `GET` żądania, który pobiera `RecognizedEntities` (osób lub widoczne na obrazie miejsca) 
* `imageTags`
* `pagesIncluding` -Stron sieci Web zawierających obrazu
* `relatedSearches`
* `visuallySimilarImages`

Określ `modules=RecognizedEntities` w `POST` żądanie pobrania tylko `imageInsightsToken`, która jest używana w kolejnej `GET` żądania. Identyfikuje osoby lub umieszcza widoczne na obrazie.

## <a name="webclient-and-headers-for-the-post-request"></a>Nagłówki dla żądania POST i WebClient żądania
Utwórz `WebClient` obiektu i ustawić nagłówki. Wszystkie żądania kierowane do interfejsu API wyszukiwania usługi Bing wymagają `Ocp-Apim-Subscription-Key`. A `POST` żądania można przekazać obrazu należy także określić `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Przekaż obraz i uzyskiwać wyniki z

`WebClient` Klasa zawiera `UpLoadFile` metody, które formatuje dane `POST` żądania. Formatuje go `RequestStream` i wywołania `HttpWebRequest`, unikając partii złożoności.
Wywołanie `WebClient.UpLoadFile` z `/details` punktu końcowego i przekazywanie pliku obrazu. Odpowiedź jest dane binarne, które łatwo jest konwertowana na format JSON. 

Użyj tekstu JSON, aby zainicjować wystąpienia `SearchResult` struktury (zobacz [kodu źródłowego aplikacji](tutorial-image-post-source.md) dla kontekstu).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Drukowanie wyników
Pozostałe kod wyniku JSON analizuje i wysłania go do konsoli.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>POBRAĆ żądania przy użyciu ImageInsightsToken
Aby użyć `ImageInsightsToken` zwracane z wyników `POST`, utworzyć `GET` żądanie, podobnie do następującej:
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Jeśli istnieją identyfikację osób lub miejsca w obrazie, to żądanie zwraca informacje o ich.
[Quickstarts](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) zawiera wiele przykładów kodu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API Bing obraz wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

