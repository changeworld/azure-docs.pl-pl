---
title: Projekt odpowiedzi fakt wyszukiwania - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Zapytania dotyczące faktów w wyszukiwaniu odpowiedzi projektu
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 0a9d1925d5ae26f40824676fbebdcb0ffc450c53
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348589"
---
# <a name="query-for-facts"></a>Zapytanie dla faktów

Jeśli zapytanie jest fakt, takie jak data lub do zidentyfikowania wiedzy, odpowiedź może zawierać `facts` odpowiedzi. Fakt odpowiedzi zawiera odpowiednie wyniki wyodrębniony z akapitów w dokumentach sieci web.  Te zapytania zawsze zwracają stron sieci Web, i [fakty](fact-queries.md) i/lub [jednostek](entity-queries.md) są zależne od zapytania.

Zapytania, takie jak valentines + 2016, gdy + jest + ramadan są traktowane jako zapytania związane z datą. Jeśli Bing Określa, że zapytanie jest związane z datą, odpowiedź zawiera `facts` odpowiedzi. 

Poniższy przykład jest związane z datą `facts` odpowiedzi. 

**Zapytanie:**
````
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

````

**Odpowiedź:** `subjectName` pole zawiera wersję wyświetlania zapytania użytkownika, które mogą służyć jako etykietę, gdy wyświetlanie fakt. Jeśli ciąg zapytania jest valentines + 2016, Bing może zmienić walentynkowe dniu 2016. Pole opisu zawiera fakt.

````
{   
    "_type" : "SearchResponse",   
    "queryContext" : {   
        "originalQuery" : "valentines 2016" 
    },   
    "facts" : {   
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",   
        "value" : [{   
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",   
            "subjectName" : "Valentine's Day 2016"   
        }]   
    },   
    "rankingResponse" : {   
        "mainline" : {   
            "items" : [{   
                "answerType" : "Facts",   
                "value" : {   
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"                   }   
            }]   
        }   
    }   
}   

````

Zapytanie "Dlaczego jest niebo niebieski?" Zwraca przykład odpowiedzi dotyczące wiedzy.

**Zapytanie:**

````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

````

**Odpowiedź:** `value/description` pole zawiera wiedzy lub informacji wymaganych przez zapytanie.

````
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

````

## <a name="tabular-data"></a>Dane tabelaryczne
W niektórych przypadkach może być zwracany faktów jako `_type: StructuredValue/TabularData`. Następujące zapytanie pobiera dane tabelaryczne kontrastujące informacje o kawy i zepołowy.

````
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us 

````
`facts` Wyniki obejmują następujące wiersze i komórki:
````
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "http://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

````

## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](c-sharp-quickstart.md)
- [Szybki Start Java](java-quickstart.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)
