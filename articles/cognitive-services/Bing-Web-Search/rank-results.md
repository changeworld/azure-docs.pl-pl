---
title: Jak używać rankingów do wyświetlania wyników wyszukiwania - Interfejs API wyszukiwania w sieci Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać rankingu do wyświetlania wyników wyszukiwania w interfejsie API wyszukiwania w sieci Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 677f6089f649aae720a6303a7e1512e3c7ebeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390135"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Jak używać rankingu do wyświetlania wyników interfejsu API wyszukiwania w sieci Bing  

Każda odpowiedź wyszukiwania zawiera odpowiedź [RankingResponse,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) która określa sposób wyświetlania wyników wyszukiwania. Odpowiedzi na ranking grupują wyniki według zawartości linii głównej i zawartości paska bocznego dla tradycyjnej strony wyników wyszukiwania. Jeśli wyniki nie są wyświetlane w tradycyjnym formacie linii głównej i paska bocznego, należy zapewnić zawartość linii głównej większą widoczność niż zawartość paska bocznego.  

W ramach każdej grupy (linii głównej lub paska [bocznego)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) items tablicy identyfikuje kolejność, w których zawartość musi pojawić się w. Każdy element zawiera następujące dwa sposoby, aby zidentyfikować wynik w odpowiedzi.  

-   `answerType`oraz `resultIndex` — `answerType` pole identyfikuje odpowiedź (na przykład stronę sieci `resultIndex` Web lub Wiadomości) i identyfikuje wynik w odpowiedzi (na przykład artykuł z wiadomościami). Indeks jest oparty na wartości zero.  

-   `value`— `value` Pole zawiera identyfikator, który pasuje do identyfikatora odpowiedzi lub wyniku w odpowiedzi. Odpowiedź lub wyniki zawierają identyfikator, ale nie oba.  

Użycie identyfikatora jest prostsze w użyciu, ponieważ wystarczy dopasować identyfikator rankingu do identyfikatora odpowiedzi lub jednego z jej wyników. Jeśli obiekt odpowiedzi `id` zawiera pole, wyświetl wszystkie wyniki odpowiedzi razem. Jeśli na `News` przykład obiekt `id` zawiera pole, wyświetl wszystkie artykuły z wiadomościami razem. Jeśli `News` obiekt nie zawiera `id` pola, każdy artykuł `id` wiadomości zawiera pole i odpowiedzi rankingu miesza artykuły wiadomości z wynikami z innych odpowiedzi.  

Korzystanie `answerType` z `resultIndex` i jest nieco bardziej skomplikowane. Służy `answerType` do identyfikowania odpowiedzi, która zawiera wyniki do wyświetlenia. Następnie można `resultIndex` użyć do indeksowania za pomocą wyników odpowiedzi, aby uzyskać wynik do wyświetlenia. (Wartość `answerType` to nazwa pola w obiekcie [SearchResponse).](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Jeśli masz wyświetlać wszystkie wyniki odpowiedzi razem, element odpowiedzi rankingowej nie `resultIndex` zawiera tego pola.  

## <a name="ranking-response-example"></a>Przykład odpowiedzi na klasyfikację

Poniżej przedstawiono przykład [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Ponieważ odpowiedź sieci Web `id` nie zawiera pola, wszystkie strony sieci Web są wyświetlane indywidualnie na `id` podstawie rankingu (każda strona sieci Web zawiera pole). A ponieważ odpowiedzi na obrazy, filmy i `id` powiązane wyszukiwania zawierają pole, wyniki każdej z tych odpowiedzi zostaną wyświetlone razem na podstawie rankingu.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Na podstawie tej odpowiedzi rankingowej linia główna wyświetlałaby następujące wyniki wyszukiwania:  

-   Pierwszy wynik strony sieci Web
-   Wszystkie obrazy  
-   Wyniki drugiej i trzeciej strony internetowej  
-   Wszystkie filmy  
-   Wyniki 4, 5 i 6 strony internetowej  

Na pasku bocznym będą wyświetlane następujące wyniki wyszukiwania:  

-   Wszystkie powiązane wyszukiwania  


## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat promowania wyników nierankingowych, zobacz [Promowanie odpowiedzi, które nie są w rankingu](./filter-answers.md#promoting-answers-that-are-not-ranked).

Aby uzyskać informacje na temat ograniczania liczby odpowiedzi rankingowych w odpowiedzi, zobacz [Ograniczanie liczby odpowiedzi w odpowiedzi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Przykład C#, który używa rankingu do wyświetlania wyników, zobacz [samouczek klasyfikacji języka C#.](./csharp-ranking-tutorial.md)
