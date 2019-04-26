---
title: 'Szybki start: Laboratorium Project Answer Search — zapytania dotyczące faktów'
titlesuffix: Azure Cognitive Services
description: Wykonywanie zapytań dotyczących faktów przy użyciu laboratorium Project Answer Search
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: fca459291fbf25c5404427fdcfc96947c52a1e74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60408091"
---
# <a name="quickstart-query-for-facts"></a>Szybki start: Wykonywanie zapytań dotyczących faktów

Jeśli zapytanie dotyczy powszechnie znanego faktu, np. daty lub możliwej do zidentyfikowania informacji, zwracanym typem odpowiedzi może być `facts`. Odpowiedzi dotyczące faktów zawierają odpowiednie wyniki wyodrębnione z akapitów w dokumentach internetowych.  Te zapytania zawsze zwracają strony internetowe, [fakty](fact-queries.md) i/lub [jednostki](entity-queries.md). Jest to zależne od zapytania.

Zapytania, takie jak valentines+2016 (walentynki+2016) i when+is+ramadan (kiedy+jest+ramadan), są traktowane jako zapytania związane z datą. Jeśli usługa Bing ustali, że zapytanie jest związane z datą, zwracanym typem odpowiedzi jest `facts`.

Poniższy przykład dotyczy odpowiedzi typu `facts` związanej z datą.

**Zapytanie:**
```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

```

**Odpowiedź:** Pole `subjectName` zawiera wyświetlaną wersję zapytania użytkownika, które może posłużyć jako etykieta podczas wyświetlania faktu. Jeśli ciąg zapytania to valentines+2016 (walentynki+2016), usługa Bing może zmienić jego brzmienie na Valentine's Day 2016 (Walentynki 2016). Pole opisu zawiera stwierdzenie faktu.

```
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
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"
                }
            }]
        }
    }
}

```

Zapytanie „Why is the sky blue?” (Dlaczego niebo jest niebieskie?) zwraca przykład odpowiedzi opartej na powszechnie obowiązującej wiedzy.

**Zapytanie:**

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

```

**Odpowiedź:** Pole `value/description` zawiera odpowiedź opartą na wiedzy lub informację żądaną przez wysłane zapytanie.

```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "https://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "https://spaceplace.nasa.gov/blue-sky/en/"
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

```

## <a name="tabular-data"></a>Dane tabelaryczne
W niektórych przypadkach fakty mogą być zwracane w postaci `_type: StructuredValue/TabularData`. Następujące zapytanie pobiera dane tabelaryczne z kontrastującymi ze sobą informacjami na temat kawy i herbaty.

```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us

```
Wyniki dla odpowiedzi typu `facts` obejmują następujące wiersze i komórki:
```
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
            "url": "https://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

```

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki start dla języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
