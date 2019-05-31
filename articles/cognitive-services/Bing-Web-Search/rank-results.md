---
title: Jak wyświetlić wyniki wyszukiwania — interfejs API wyszukiwania Bing w sieci Web za pomocą klasyfikacji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać funkcji Klasyfikacja można wyświetlić wyniki wyszukiwania z API wyszukiwania w Internecie Bing.
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
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390135"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Jak używać funkcji Klasyfikacja do wyświetlania wyników API wyszukiwania w Internecie Bing  

Każda odpowiedź wyszukiwania zawiera [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) odpowiedzi, który określa, jak należy wyświetlić wyniki wyszukiwania. Odpowiedź klasyfikacji grupuje wyniki według mainline i paska bocznego zawartości dla strona wyników wyszukiwania tradycyjnych. Jeśli nie są wyświetlane wyniki w przypadku tradycyjnych linii głównej i paska bocznego format, należy podać linii głównej zawartości bardziej widoczny niż zawartość pasku bocznym.  

W każdej grupie (mainline lub pasku bocznym), [elementów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) tablicy identyfikuje zawartość musi znajdować się w podanej kolejności. Każdy element zawiera następujące dwa sposoby, aby zidentyfikować wynik w odpowiedzi.  

-   `answerType` i `resultIndex` — `answerType` pole identyfikuje odpowiedzi (np. strony sieci Web lub wiadomości) i `resultIndex` identyfikuje wyniku w odpowiedzi (na przykład artykuł grup dyskusyjnych). Indeks się od zera.  

-   `value` — `value` Pole zawiera identyfikator, który jest zgodny z Identyfikatorem odpowiedzi lub wyniku w odpowiedzi. Odpowiedź na pytanie lub wyniki zawierają identyfikator, ale nie oba jednocześnie.  

Przy użyciu Identyfikatora jest łatwiejszy w obsłudze, ponieważ konieczne jest zgodny z Identyfikatorem klasyfikacji o identyfikatorze odpowiedzi lub jednego z jego wyniki. Jeśli obiekt odpowiedzi zawiera `id` pola, wyświetlić wyniki wszystkich odpowiedzi ze sobą. Na przykład jeśli `News` obiekt zawiera `id` pola, wyświetlić wszystkie artykuły z wiadomościami ze sobą. Jeśli `News` nie ma obiektu `id` pola, a następnie zawiera każdego artykułu z wiadomościami `id` pola i odpowiedzi klasyfikacji napisana artykuły z wiadomościami z wynikami z innych odpowiedzi.  

Za pomocą `answerType` i `resultIndex` jest nieco bardziej skomplikowane. Możesz użyć `answerType` do identyfikowania odpowiedzi, który zawiera wyników do wyświetlenia. Następnie należy użyć `resultIndex` do indeksu za pośrednictwem wyników odpowiedź na pytanie, aby uzyskać wynik do wyświetlenia. ( `answerType` Wartością jest nazwa pola w [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) obiektu.) Jeśli masz powinien wyświetlić wyników wszystkich odpowiedzi ze sobą, odpowiedź klasyfikacji elementu nie zawiera `resultIndex` pola.  

## <a name="ranking-response-example"></a>Klasyfikacja przykład odpowiedzi

Poniżej pokazano przykład [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Ponieważ nie ma odpowiedzi w sieci Web `id` pola, zostanie wyświetlony wszystkich stron sieci Web, które są indywidualnie oparte na klasyfikacji (każdej strony sieci Web obejmuje `id` pola). A ponieważ obrazy, wideo i powiązane wyszukiwania odpowiedzi zawiera `id` pola, będą wyświetlane wyniki każdego z tych odpowiedzi razem w oparciu o rangę.

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

Na podstawie tej klasyfikacji odpowiedzi, linii głównej będzie wyświetlane następujące wyniki wyszukiwania:  

-   Pierwszego wyniku strony sieci Web
-   Wszystkie obrazy  
-   Wyniki druga i trzecia strona sieci Web  
-   Wszystkie filmy wideo  
-   Wyniki strony sieci Web 4, 5 i 6.  

I paska bocznego będą wyświetlane następujące wyniki wyszukiwania:  

-   Powiązane wyszukiwania  


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacji na temat podwyższania poziomu unranked wyników, zobacz [podwyższania poziomu odpowiedzi, które nie są oceniane](./filter-answers.md#promoting-answers-that-are-not-ranked).

Aby dowiedzieć się, jak ograniczenie liczby rangi odpowiedzi w odpowiedzi, zobacz [ograniczenie liczby odpowiedzi w odpowiedzi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Dla języka C# przykładu, który używa klasyfikacji, aby wyświetlić wyniki, zobacz [klasyfikacji samouczek języka C#](./csharp-ranking-tutorial.md).
