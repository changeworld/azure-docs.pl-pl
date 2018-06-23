---
title: Aby wyświetlić odpowiedzi sieci web przy użyciu klasyfikacji | Dokumentacja firmy Microsoft
description: Pokazuje, jak używać klasyfikacji do wyświetlenia odpowiedzi, które zwraca interfejs API wyszukiwania usługi Bing sieci Web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 750146f3bb28b94594a71733b68f092880360c5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348997"
---
# <a name="using-ranking-to-display-results"></a>Aby wyświetlić wyniki przy użyciu klasyfikacji  

Każda odpowiedź wyszukiwania zawiera [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpowiedzi, który określa sposób musisz wyświetlić wyniki wyszukiwania. Odpowiedź klasyfikacji grupuje wyniki przez mainline i paska bocznego zawartości dla strony wyników wyszukiwania tradycyjnych. Jeśli nie są wyświetlane wyniki w tradycyjny połączeniach i format paska bocznego, musisz podać zawartości połączeniach nowszej widoczność niż zawartość paska bocznego.  
  
W każdej grupie (mainline lub paska bocznego), [elementów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tablicy identyfikuje zawartość musi występować w określonej kolejności. Każdy element udostępnia dwa sposoby do identyfikowania wyniku w odpowiedzi.  
  
-   `answerType` i `resultIndex` — `answerType` pole identyfikuje odpowiedzi (na przykład strony sieci Web lub wiadomości) i `resultIndex` identyfikuje wyniku w odpowiedzi (na przykład artykuł wiadomości). Indeks się od zera.  
  
-   `value` — `value` Pole zawiera identyfikator, który jest zgodny z Identyfikatorem odpowiedzi lub wyniku w odpowiedzi. Odpowiedź na pytanie lub wyniki zawierają identyfikator, ale nie oba.  
  
Przy użyciu Identyfikatora jest łatwiejsze do użycia, ponieważ konieczne jest niezgodny z Identyfikatorem klasyfikacji o identyfikatorze odpowiedzi lub jednego z jego wyniki. Jeśli obiekt odpowiedzi zawiera `id` pola, Wyświetl wyniki wszystkich odpowiedzi ze sobą. Na przykład jeśli `News` obiekt zawiera `id` pola, wyświetlać wszystkie artykuły ze sobą. Jeśli `News` nie ma obiektu `id` pola każdego artykułu wiadomości zawiera `id` pola i odpowiedzi klasyfikacji łączy artykuły z wyników z innych odpowiedzi.  
  
Przy użyciu `answerType` i `resultIndex` jest nieco bardziej skomplikowane. Możesz użyć `answerType` do identyfikowania odpowiedzi, który zawiera wyników do wyświetlenia. Następnie należy użyć `resultIndex` do indeksu za pomocą wyniki odpowiedź na pytanie, aby uzyskać wyników do wyświetlenia. ( `answerType` Wartość jest nazwą pola w [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) obiektu.) Jeśli jest powinien, aby wyświetlić wyniki wszystkich odpowiedzi ze sobą, odpowiedzi klasyfikacji elementu nie zawiera `resultIndex` pola.  

## <a name="ranking-response-example"></a>Klasyfikacja przykład odpowiedzi

Poniżej przedstawiono przykład [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Ponieważ nie ma odpowiedzi sieci Web `id` pola, czy wyświetlane pojedynczo oparte na klasyfikacji do wszystkich stron sieci Web (każdej strony sieci Web zawiera `id` pól). I dlatego dołączaj obrazów, klipów wideo i odpowiedzi wyszukiwań pokrewnych `id` pola, czy wyświetlane wyniki każdego z tych odpowiedzi razem oparte na klasyfikacji.
  
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
    },  
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
  
W oparciu o tę odpowiedź klasyfikacji, połączeniach wyświetla następujące wyniki wyszukiwania:  
  
-   Pierwszego wyniku strony sieci Web 
-   Wszystkie obrazy  
-   Wyniki drugiego i trzeciego strony sieci Web  
-   Wszystkie pliki wideo  
-   Wyniki strony sieci Web 4, 5 i 6.  
  
I paska bocznego wyświetla następujące wyniki wyszukiwania:  
  
-   Wszystkie powiązane wyszukiwania  
  

## <a name="next-steps"></a>Kolejne kroki

Uzyskać informacji o unranked wyników, zobacz [podwyższania poziomu odpowiedzi, które nie są klasyfikowane](./filter-answers.md#promoting-answers-that-are-not-ranked).

Aby dowiedzieć się, jak ograniczenie liczby uporządkowanej według rangi odpowiedzi w odpowiedzi, zobacz [ograniczenie liczby odpowiedzi w odpowiedzi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Np. C#, który używa klasyfikacji, aby wyświetlić wyniki, zobacz [klasyfikacji samouczek C#](./csharp-ranking-tutorial.md).