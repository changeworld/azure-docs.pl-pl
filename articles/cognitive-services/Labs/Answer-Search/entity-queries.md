---
title: 'Szybki Start: Zapytanie jednostki wyszukiwania odpowiedzi projektów'
titlesuffix: Azure Cognitive Services
description: Zapytania dotyczące jednostki przy użyciu projektu wyszukiwanie odpowiedzi
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: article
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: efb46fc7064bcad69b5ea84f9bdfe923d95ccbe6
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867582"
---
# <a name="quickstart-query-for-entities"></a>Szybki Start: Zapytań dotyczących jednostek

Jeśli zapytanie żąda informacji na temat osoby, miejsca lub rzeczy, odpowiedź może zawierać `entities` odpowiedzi.  Zapytania zawsze zwracają stron sieci Web, [fakty](fact-queries.md) i/lub [jednostek](entity-queries.md) są zależne od zapytania.

Jednostki obsługuje trzy scenariusze kwerendy: 
-   DominantEntity — istnieje tylko jedna jednostka, która pasuje do zapytania i intencji użytkownika. Na przykład zapytanie wskazówkę miejsca jest scenariusz DominantEntity. 
-   Uściślanie — istnieje więcej niż jednej jednostki, który odpowiada, zapytania i przeznaczenie użytkownika i zależy od użytkownika o wybranie prawidłowe jednostki. Na przykład zapytanie gry Thrones jest scenariusz uściślania, który zwraca serii książki i Pokaż telewizyjnych w Twoim regionie. 
-   List — istnieje wiele jednostek, które odpowiadają zapytania i intencji użytkownika. Na przykład zapytanie "Lista zagrożonych gatunków" jest scenariusz listy, który zwraca wartości tabelarycznych formatować w celu wyświetlenia w wiersze i komórki. 
 
Aby określić scenariusz zapytania, należy użyć `queryScenario` pole `entities` obiektu. Dane, które zawiera jednostkę zależy od typu jednostki. Chociaż jednostki zawierają te same podstawowe informacje, niektóre jednostki, takie jak atrakcji lub książki obejmują dodatkowe właściwości. Jednostek zawierających dodatkowe właściwości obejmują `_type` pola, które zawiera wskazówki, używany przez element serializujący. Następujące jednostki zawierają dodatkowe właściwości: 
-   Książki 
-   MusicRecording 
-   Person (Osoba) 
-   Przyciągania 
 
Aby określić typ jednostki, który zawiera odpowiedź, użyj `entityTypeHints` pola, jak pokazano w zapytaniu dla Billa Gatesa.
````
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
````
Oto zapytanie dotyczące wskazówkę miejsca:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
````
Odpowiedź zawiera `entities` odpowiedzi. Uwaga `entityScenario` i `entityTypeHints` pola. 
````
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
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
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
              "url": "http://en.wikipedia.org/wiki/Space_Needle"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg",
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
````

Zapytanie może zwracać listę, jeśli jest to potrzebne.

**Zapytanie:** poniższe zapytanie wyszukuje listę zagrożonych gatunków:

````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

````

**Odpowiedź:** odpowiedź zawiera listę do wyświetlenia w formacie tabelarycznym wartości:
````
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

````


## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki Start języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki Start języka Java](java-quickstart.md)
- [Przewodnik Szybki Start węzła](node-quickstart.md)
- [Przewodnik Szybki Start języka Python](python-quickstart.md)