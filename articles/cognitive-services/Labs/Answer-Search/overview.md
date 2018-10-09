---
title: Czym jest projekt wyszukiwania odpowiedzi?
titlesuffix: Azure Cognitive Services
description: Wprowadzenie do wyszukiwania odpowiedzi projektu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 87fe7b008e3e7c6cd8d1a9a870c0fb8ce2f6a7cd
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868259"
---
# <a name="what-is-project-answer-search"></a>Czym jest projekt wyszukiwania odpowiedzi?
Projekt interfejsu API wyszukiwania w odpowiedzi używa punktu końcowego usługi Bing w wersji 7, aby uzyskać odpowiedzi na zapytania interrogative. A przykład pytanie "Co to jest obwód ziemi?" zwraca odpowiedź z faktach informacji.  Zapytanie dotyczące osoby, miejsca lub rzeczy zwraca informacje o jednostka identyfikowane przez zapytanie. Te scenariusze może być przydatne w aplikacjach, takich jak Boty konwersacji, obsługi komunikatów w aplikacji, czytelnicy, itp.  

Zapytania zwracają odpowiedzi, które są zależne od scenariusza zapytania: stron sieci Web są zawsze zwracany, podczas gdy [fakty](fact-queries.md) i/lub [jednostek](entity-queries.md) są zwracane, jeśli jest to odpowiednie.

## <a name="endpoint"></a>Endpoint
Aby uzyskać odpowiedzi na pytanie lub informacji na temat osoby, miejsca lub rzeczy, Wyślij żądanie do punktu końcowego interfejsu API wyszukiwania w odpowiedzi. Na użytek nagłówki i parametry adresu URL różne specyfikacje.  Obejmują *Ocp-Apim-Subscription-Key* nagłówka z prawidłowym tokenem.  Parametr rynku jest wymagany. Tylko `en-us` rynku jest obecnie obsługiwane.

Następujące zapytanie pobiera odpowiedzi na pytanie: "Co to jest obwód ziemi?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Parametr adresu URL `q=` jest wymagany do określenia obiektu wyszukiwania.

## <a name="response-object"></a>Obiekt odpowiedzi

Odpowiedź zawiera nagłówki HTTP, stron sieci Web, faktów lub jednostki.

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
Wyszukiwanie odpowiedzi projektu i trendy wideo projektu są podlegają [Użyj wyszukiwania Bing i wymagania dotyczące wyświetlania](use-display-requirements.md).

Użytkownik lub innych firm w Twoim imieniu może nie używać, zachować, przechowywania, pamięci podręcznej, udostępnić, dystrybucji żadnych danych z interfejsu API (wersja zapoznawcza) adres URL na potrzeby testowania, tworzenia, szkolenia, rozpowszechniania lub udostępniania dowolnej usługi firmy Microsoft lub funkcji. 

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Autorstwa danych  

Odpowiedzi wyszukiwania odpowiedzi projektu zawierają informacji należących do innych firm. Ponosisz odpowiedzialność, aby upewnić się, że użytkowanie jest odpowiednie, na przykład zgodnych z licencji creative commons środowiska użytkownika może polegać na.  
  
Jeśli wynik lub odpowiedzi zawiera `contractualRules`, `attributions`, lub `provider` pól, należy atrybutów danych. Jeśli odpowiedź nie zawiera żadnego z tych pól, autorstwa nie jest wymagana. Jeśli odpowiedź obejmuje `contractualRules` pola i `attributions` i/lub `provider` pola, musisz podać umowne stosowane w UE zasady do atrybutu danych.  
  
W poniższym przykładzie przedstawiono jednostki, która zawiera regułę umownych MediaAttribution i obraz, który zawiera `provider` pola. Reguła MediaAttribution identyfikuje obraz jako obiekt docelowy reguły, dzięki czemu będzie ignorować obrazu `provider` pola, a następnie używają reguł MediaAttribution zapewnienie autorstwa.  
  
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
  
Jeśli zawiera regułę umownych `targetPropertyName` pole, reguła dotyczy tylko pola docelowego. W przeciwnym razie ta reguła ma zastosowanie do obiektu nadrzędnego, który zawiera `contractualRules` pola.  
  
  
W poniższym przykładzie `LinkAttribution` reguła zawiera `targetPropertyName` pola, więc ta reguła ma zastosowanie do `description` pola. Dla reguł, które dotyczą określonych pól musi zawierać wiersz bezpośrednio po docelowych dane, które zawierają hiperłącza do witryny sieci Web dostawcy. Na przykład aby atrybut Opis, dołączyć linię natychmiast po tekst opisu zawierający hiperłącze do danych w witrynie sieci Web dostawcy, w tym przypadku utworzenie łącza do en.wikipedia.org.  
  
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

### <a name="license-attribution"></a>Autorstwa licencji  

Jeśli zawiera listę reguł umownych [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regułę, należy wyświetlić powiadomienia w wierszu bezpośrednio po zawartości, która dotyczy licencji. `LicenseAttribution` Reguła używa `targetPropertyName` pola, aby zidentyfikować właściwość, która dotyczy licencji.  
  
Poniżej pokazano przykład, który zawiera `LicenseAttribution` reguły.  
  
![Autorstwa licencji](./media/licenseattribution.png)  
  
Ogłoszenie licencji, którą można wyświetlać musi zawierać hiperlink do witryny sieci Web, który zawiera informacje o licencji. Zazwyczaj należy nazwa licencji hiperłącze. Na przykład, jeśli jest Akceptuję **tekst w polu DW przez SA licencji** i DW przez SA nazwa licencji, czyniłyby DW przez SA hiperłącze.  
  
### <a name="link-and-text-attribution"></a>Łącze i autorstwa tekstu  

[LinkAttribution](reference.md#linkattribution) i [TextAttribution](reference.md#textattribution) zasady zwykle są używane do identyfikowania dostawcy danych. `targetPropertyName` Pole określa pola, które dotyczy reguła.  
  
Aby atrybutu dostawców, Dołącz wiersz bezpośrednio po zawartości, pierwsza stosowane do (na przykład pola docelowego). Wiersz należy wyraźnie oznaczone do wskazania, że dostawcy są źródła danych. Na przykład "dane z: en.wikipedia.org". Aby uzyskać `LinkAttribution` zasady, należy utworzyć hiperłącze do witryny sieci Web dostawcy.  
  
Poniżej pokazano przykład, który zawiera `LinkAttribution` i `TextAttribution` reguły.  
  
![Tekst łącza: uznanie autorstwa](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Nośnik: uznanie autorstwa  

Jeśli jednostka zawiera obraz, możesz wyświetlić, musisz podać klikalny link do witryny sieci Web dostawcy. Jeśli jednostka zawiera [MediaAttribution](reference.md#mediaattribution) regułę, należy użyć adresu URL reguły do utworzenia łącza za pomocą kliknięć. W przeciwnym razie użyj adresu URL uwzględnione w obrazie `provider` pola, aby utworzyć link za pomocą kliknięć.  
  
Poniżej pokazano przykład, który zawiera obraz `provider` pola i reguły umownych. Przykład zawiera reguły umowne stosowane w UE, będzie ignorować obrazu `provider` pola, a następnie zastosować `MediaAttribution` reguły.  
  
![Nośnik: uznanie autorstwa](./media/mediaattribution.png)  

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki Start języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki Start języka Java](java-quickstart.md)
- [Przewodnik Szybki Start węzła](node-quickstart.md)
- [Przewodnik Szybki Start języka Python](python-quickstart.md)
