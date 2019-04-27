---
title: 'Szybki start: Laboratorium Project Answer Search — zapytanie o jednostki'
titlesuffix: Azure Cognitive Services
description: Wykonywanie zapytań dotyczących jednostek przy użyciu laboratorium Project Answer Search
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: c9f2a476353b1807bbb1c8c13dc8b8e2cdbb4ae4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688626"
---
# <a name="quickstart-query-for-entities"></a>Szybki start: Wykonywanie zapytań o jednostki

Jeśli zapytanie żąda informacji na temat osoby, miejsca lub rzeczy, w odpowiedzi mogą znaleźć się obiekty `entities`.  Zapytania zawsze zwracają strony internetowe, [fakty](fact-queries.md) i/lub [jednostki](entity-queries.md). Jest to zależne od zapytania.

Jednostki obsługują trzy scenariusze zapytań: 
-   DominantEntity — istnieje tylko jedna jednostka, która pasuje do zapytania i intencji użytkownika. Na przykład zapytanie „Space Needle” jest przykładem scenariusza DominantEntity. 
-   Disambiguation (Uściślanie) — istnieje więcej niż jedna jednostka pasująca do zapytania oraz intencji użytkownika i to użytkownik wybiera, która z nich jest prawidłowa. Na przykład zapytanie „Gra o tron” pasuje do scenariusza uściślania, ponieważ zwraca informacje o serialu telewizyjnym oraz o serii książek. 
-   List (Lista) — istnieje wiele jednostek, które pasują do zapytania i intencji użytkownika. Na przykład zapytanie „Lista zagrożonych gatunków” jest scenariuszem listy, ponieważ zwracane są wartości tabelaryczne sformatowane do wyświetlania w postaci wierszy i komórek. 
 
Aby określić scenariusz zapytania, użyj pola `queryScenario` obiektu `entities`. Dane, które zawiera jednostka, zależą od jej typu. Chociaż jednostki zawierają te same podstawowe informacje, niektóre jednostki, takie jak atrakcje turystyczne lub książki obejmują dodatkowe właściwości. Jednostki zawierające dodatkowe właściwości mają pole `_type` przeznaczone na wskazówkę używaną przez serializator. Następujące jednostki zawierają dodatkowe właściwości: 
-   Book (Książka) 
-   MusicRecording (Nagranie muzyczne) 
-   Person (Osoba) 
-   Attraction (Atrakcja) 
 
Aby określić typ jednostki, jaką zawiera odpowiedź, użyj pola `entityTypeHints`, jak pokazano w zapytaniu „Bill Gates”.
```
        },
        "description": "Bill Gates is an American business man and philanthropist, co-founder of Microsoft",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Person"
          ]
        },
        "bingId": "6d7d66a7-2cb8-0ae9-637c-f81fd749dc9a"
      }
```
Poniżej zamieszczono zapytanie o „Space Needle”:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
```
Odpowiedź zawiera element `entities`. Zwróć uwagę na pola `entityScenario` i `entityTypeHints`. 
```
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Space_Needle"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Space_Needle"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dSpace+Needle\u0026filters\u003dsid:%22f8dd5b08-206d-2554-6e4a-893f51f4de7e%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Space Needle",
        "image": {
          "name": "Space Needle",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA15d336cf119b9b5c7e0ab37e271421d3\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Space_Needle"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 152,
          "sourceHeight": 300
        },
        "description": "The Space Needle is an observation tower in Seattle, Washington, a landmark of the Pacific Northwest, and an icon of Seattle. It was built in the Seattle Center for the 1962 World\u0027s Fair, which drew over 2.3 million visitors, when nearly 20,000 people a day used its elevators.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Attraction"
          ]
        },
        "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e"
      }
    ]
  },
```

W odpowiednich sytuacjach zapytanie może zwrócić listę.

**Zapytanie:** poniższe zapytanie wyszukuje listę zagrożonych gatunków:

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

```

**Odpowiedź:** odpowiedź zawiera listę sformatowaną pod kątem wyświetlania w formie wartości w tabeli:
```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "seeMoreUrl": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "value": [
      {
        "subjectName": "Species Directory",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "Common name",
            "Scientific name",
            "Conservation status ↓"
          ],
          "rows": [
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Amur Leopard",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/amur-leopard"
                },
                {
                  "text": "Panthera pardus orientalis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Black Rhino",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/black-rhino"
                },
                {
                  "text": "Diceros bicornis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Bornean Orangutan",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/bornean-orangutan"
                },
                {
                  "text": "Pongo pygmaeus"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Cross River Gorilla",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/cross-river-gorilla"
                },
                {
                  "text": "Gorilla gorilla diehli"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "46 more rows",
            "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
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
