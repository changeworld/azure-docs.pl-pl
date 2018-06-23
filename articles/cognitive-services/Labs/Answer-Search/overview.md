---
title: Projekt Przegląd odpowiedzi wyszukiwania — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Wprowadzenie do wyszukiwania odpowiedzi projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d87cf1390970d2c815b94bcaee7e07c19bc03cce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348696"
---
# <a name="what-is-project-answer-search"></a>Co to jest wyszukiwania odpowiedzi projektu?
Interfejsu API Search odpowiedzi projektu używa punktu końcowego w wersji 7 Bing w celu uzyskania odpowiedzi na zapytania interrogative. A pytanie takich jak "Co to jest obwód ziemi?" zwraca odpowiedź z informacji opartych na faktach.  Zapytanie dotyczące osoby, miejsca lub element zwraca informacje dotyczące jednostki zidentyfikowane przez zapytanie. Te scenariusze mogą być przydatne w aplikacjach, takich jak robotów konwersacji, wiadomości w aplikacji, czytników itp.  

Kwerendy zwracać odpowiedzi, które są zależne od scenariusza zapytania: stron sieci Web są zawsze zwrócone, podczas gdy [fakty](fact-queries.md) i/lub [jednostek](entity-queries.md) są zwracane w razie potrzeby.

## <a name="endpoint"></a>Endpoint
Aby uzyskać odpowiedzi na pytanie lub informacje dotyczące osoby, miejsca lub operacją, Wyślij żądanie do punktu końcowego interfejsu API odpowiedzi wyszukiwania. Użyj nagłówków i parametry adresu URL dla różnych specyfikacji.  Obejmują *Ocp-Apim-subskrypcji — klucz* nagłówek z prawidłowym tokenem.  Wymagany jest parametr rynku. Tylko `en-us` rynku jest obecnie obsługiwany.

Następujące zapytanie pobiera odpowiedzi na pytanie: "Co to jest obwód ziemi?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Parametr adresu URL `q=` jest wymagana, aby określić obiekt wyszukiwania.

## <a name="response-object"></a>Obiekt odpowiedzi

Odpowiedź zawiera nagłówki HTTP, stron sieci Web, faktów lub jednostek.

````
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
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
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

````

## <a name="terms-of-use"></a>Warunki użytkowania
Wyszukiwanie odpowiedzi projektu i trendów wideo projektu podlegają [wyszukiwania usługi Bing oraz wymagania dotyczące wyświetlania](use-display-requirements.md).

Możesz lub innej firmy w Twoim imieniu może nie, Zachowaj, przechowywania, pamięci podręcznej, udział, albo dystrybucji żadnych danych z interfejsu API w wersji zapoznawczej adres URL na potrzeby testowania, tworzenie, uczenie, dystrybucja lub udostępnia usługi firmy Microsoft lub funkcji. 

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Autorstwa danych  

Odpowiedzi odpowiedzi wyszukiwania projektu zawiera informacje o posiadanych przez osoby trzecie. Ponosisz odpowiedzialność za zapewnienie, że używanie jest odpowiednia, na przykład przez zgodne z licencji creative commons przez użytkowników mogą polegać na.  
  
Jeśli odpowiedzi lub wynik zawiera `contractualRules`, `attributions`, lub `provider` pól, musi atrybutu danych. Jeśli odpowiedź nie zawiera żadnego z tych pól, przypisanie nie jest wymagana. Jeśli odpowiedź zawiera `contractualRules` pola i `attributions` i/lub `provider` pola, muszą używać zasad umownych atrybutu danych.  
  
W poniższym przykładzie przedstawiono jednostki, która zawiera regułę umownymi MediaAttribution i obraz, który zawiera `provider` pola. Reguła MediaAttribution identyfikuje obraz jako element docelowy reguły, więc będzie ignorować obrazu `provider` pola i zamiast tego użyj reguł MediaAttribution, aby zapewnić autorstwa.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Jeśli reguła umownymi zawiera `targetPropertyName` pola, ta reguła ma zastosowanie tylko do pola docelowego. W przeciwnym razie ta reguła ma zastosowanie do obiektu nadrzędnego, który zawiera `contractualRules` pola.  
  
  
W poniższym przykładzie `LinkAttribution` reguła zawiera `targetPropertyName` pola, więc ta reguła ma zastosowanie do `description` pola. Dla reguł, które dotyczą określonych pól należy wprowadzić wiersza od razu po wybranych danych, który zawiera hiperłącza do witryny sieci Web dostawcy. Na przykład atrybutu opis, obejmują linii natychmiast po tekst opisu, który zawiera hiperłącza do danych w witrynie sieci Web dostawcy, w tym przypadku utworzyć łącze do en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Przypisanie licencji  

Jeśli na liście reguł umownymi zawiera [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regułę, należy wyświetlić ogłoszenia w wierszu bezpośrednio po zawartości dotyczącej licencji. `LicenseAttribution` Zasada używa `targetPropertyName` pola, aby zidentyfikować właściwość, która dotyczy licencji.  
  
Poniżej przedstawiono przykład zawierający `LicenseAttribution` reguły.  
  
![Przypisanie licencji](./media/licenseattribution.png)  
  
Zawiadomienia licencji, które można wyświetlić musi zawierać hiperłącza do witryny sieci Web, który zawiera informacje o licencji. Zazwyczaj wprowadzeniu nazwy licencji hiperłącze. Na przykład, jeśli jest postanowień **tekstu w ramach licencji DW przez SA** i DW przez administratora systemu jest nazwą licencji, spowodowałoby DW przez administratora systemu hiperłącze.  
  
### <a name="link-and-text-attribution"></a>Łącze i autorstwa tekstu  

[LinkAttribution](reference.md#linkattribution) i [TextAttribution](reference.md#textattribution) zasady zwykle są używane do identyfikowania dostawcy danych. `targetPropertyName` Pole określa pole, które będzie stosowana ta reguła.  
  
Atrybutu dostawców, obejmują linii bezpośrednio po zawartości, które mają zastosowanie pierwsza na (na przykład pole docelowe). Wiersz należy wyraźnie etykietami do wskazania, że źródło danych dostawców. Na przykład "dane z: en.wikipedia.org". Aby uzyskać `LinkAttribution` zasady, należy utworzyć hiperłącza do witryny sieci Web dostawcy.  
  
Poniżej przedstawiono przykład zawierający `LinkAttribution` i `TextAttribution` reguły.  
  
![Autorstwa tekst łącza](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Nośnik autorstwa  

Jeśli obiekt zawiera obraz i wyświetl ją, musisz podać kliknięć łącze do witryny sieci Web dostawcy. Jeśli obiekt zawiera [MediaAttribution](reference.md#mediaattribution) reguły, użyj jej adres URL, aby utworzyć łącze kliknięć. W przeciwnym razie użyj adresu URL uwzględniony w obrazie `provider` pola, aby utworzyć łącze kliknięć.  
  
Poniżej przedstawiono przykład zawierający obraz `provider` pola i umownymi reguły. Przykład zawiera reguły umownymi, zignoruje obrazu `provider` pola i Zastosuj `MediaAttribution` reguły.  
  
![Nośnik autorstwa](./media/mediaattribution.png)  

## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](c-sharp-quickstart.md)
- [Szybki Start Java](java-quickstart.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)
