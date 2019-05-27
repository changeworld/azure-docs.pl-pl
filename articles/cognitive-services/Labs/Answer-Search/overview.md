---
title: Czym jest projekt wyszukiwania odpowiedzi?
titlesuffix: Azure Cognitive Services
description: Wprowadzenie do laboratorium Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: ac1717a8e8a08fcfedc3bc21bb0f03b3e3ca2511
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "60721853"
---
# <a name="what-is-project-answer-search"></a>Czym jest projekt wyszukiwania odpowiedzi?
Interfejs API laboratorium Project Answer Search korzysta z punktu końcowego Bing w wersji 7, aby uzyskać odpowiedzi na zapytania w formie pytań. Pytanie „What is the circumference of the earth?”(Jaki jest obwód ziemi?) zwraca odpowiedź, która zawiera fakty.  Zapytanie dotyczące osoby, miejsca lub przedmiotu zwraca informacje dotyczące jednostki określonej przez zapytanie. Te scenariusze mogą być przydatne w aplikacjach, takich jak na przykład boty konwersacyjne, aplikacje do obsługi wiadomości lub czytniki.  

Zapytania zwracają odpowiedzi, które są uzależnione od scenariusza zapytania: strony internetowe są zwracane zawsze, podczas gdy [fakty](fact-queries.md) i/lub [jednostki](entity-queries.md) są zwracane wówczas, gdy są istotne.

## <a name="endpoint"></a>Endpoint
Aby uzyskać odpowiedzi na pytanie lub informacje na temat osoby, miejsca lub przedmiotu, należy wysłać żądanie do punktu końcowego interfejsu API Answer Search. Użyj odpowiednich nagłówków i parametrów adresu URL do celów różnych specyfikacji.  Dołącz nagłówek *Ocp-Apim-Subscription-Key* zawierający prawidłowy token.  Wymagane jest podanie parametru rynku. Aktualnie obsługiwany jest tylko rynek `en-us`.

Następujące zapytanie umożliwia uzyskanie odpowiedzi na pytanie: „What is the circumference of the earth?”(Jaki jest obwód ziemi?)

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

Aby określić obiekt wyszukiwania, konieczny jest parametr adresu URL `q=`.

## <a name="response-object"></a>Obiekt odpowiedzi

Odpowiedź zawiera nagłówki HTTP, strony internetowe, fakty i/lub jednostki.

```
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
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
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
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
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

```

## <a name="terms-of-use"></a>Warunki użytkowania
Laboratoria Project Answer Search i Project Video Trends podlegają [wymaganiom związanym z korzystaniem i wyświetlaniem wyszukiwania Bing](use-display-requirements.md).

Użytkownik ani żaden inny podmiot w jego imieniu nie może używać, zachowywać, przechowywać, buforować, udostępniać ani rozpowszechniać żadnych danych z interfejsu API projektu podglądu adresu URL do celów testowania, opracowywania, rozpowszechniania lub udostępniania jakiejkolwiek usługi lub funkcji firmy innej niż Microsoft. 

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Określanie autorstwa danych  

Odpowiedzi laboratorium Projekt Answer Search zawierają informacje, których właścicielem są inne podmioty. Użytkownik odpowiada za korzystanie z usługi w sposób właściwy, na przykład poprzez przestrzeganie licencji Creative Commons, z których mogą korzystać elementy środowiska użytkownika.  
  
Jeśli odpowiedź lub wynik zawiera pola `contractualRules`, `attributions` lub `provider`, należy podać autorstwo danych. Jeśli odpowiedź nie zawiera żadnego z tych pól, podanie autorstwa danych nie jest wymagane. Jeśli odpowiedź zawiera pole `contractualRules` oraz pola `attributions` i/lub `provider`, należy zastosować reguły umowne w celu określenia autorstwa danych.  
  
W poniższym przykładzie pokazano jednostkę, która zawiera regułę umowną MediaAttribution i obraz, który zawiera pole `provider`. Reguła MediaAttribution identyfikuje obraz jako obiekt docelowy reguły, a więc możesz zignorować pole `provider` obrazu, i zastosować regułę MediaAttribution w celu określenia autorstwa.  
  
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
  
Jeśli reguła umowna zawiera pole `targetPropertyName`, reguła dotyczy tylko pola docelowego. W przeciwnym razie ta reguła dotyczy obiektu nadrzędnego, który zawiera pole `contractualRules`.  
  
  
W poniższym przykładzie reguła `LinkAttribution` zawiera pole `targetPropertyName`, a więc reguła dotyczy pola `description`. W przypadku reguł, które dotyczą konkretnych pól, po danych docelowych musisz umieścić wiersz zawierający hiperlink do witryny internetowej dostawcy. Na przykład aby podać autorstwo opisu, po tekście opisu należy dołączyć wiersz zawierający hiperlink do danych w witrynie internetowej dostawcy — w tym przypadku link do witryny en.wikipedia.org.  
  
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

### <a name="license-attribution"></a>Określanie licencji  

Jeśli lista reguł umownych zawiera regułę [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), należy w wierszu następującym tuż po zawartości objętej licencją dodać odpowiednią informację. Reguła `LicenseAttribution` korzysta z pola `targetPropertyName`, aby określić właściwość, której dotyczy licencja.  
  
Poniżej pokazano przykład, który zawiera regułę `LicenseAttribution`.  
  
![Określanie licencji](./media/licenseattribution.png)  
  
Wyświetlana informacja dotycząca licencji musi zawierać hiperlink do witryny internetowej, która zawiera informacje dotyczące licencji. Najczęściej hiperlinkiem jest nazwa licencji. Na przykład jeśli komunikat brzmi **Text under CC-BY-SA license** (Tekst w ramach licencji CC-BY-SA), a CC-BY-SA to nazwa licencji, hiperlinkiem będzie ciąg „CC-BY-SA”.  
  
### <a name="link-and-text-attribution"></a>Określanie autorstwa linków i tekstu  

Reguły [LinkAttribution](reference.md#linkattribution) i [TextAttribution](reference.md#textattribution) są zwykle używane do identyfikowania dostawcy danych. Pole `targetPropertyName` określa pole, którego dotyczy reguła.  
  
Aby określić dostawców, należy dodać odpowiedni wiersz tuż po zawartości, której dotyczy (na przykład po polu docelowym). Ten wiersz powinien być czytelnie oznaczony, aby wskazać, że określeni dostawcy są źródłem danych. Na przykład: „Data from: en.wikipedia.org” (Dane z witryny en.wikipedia.org). W przypadku reguł `LinkAttribution`, należy utworzyć hiperlink do witryny internetowej dostawcy.  
  
Poniżej pokazano przykład, który zawiera reguły `LinkAttribution` i `TextAttribution`.  
  
![Określanie autorstwa tekstu linku](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Określanie autorstwa multimediów  

Jeśli jednostka zawiera obraz, który wyświetlasz, musisz dołączyć link do witryny dostawcy, który można kliknąć. Jeśli jednostka zawiera regułę [MediaAttribution](reference.md#mediaattribution), należy do utworzenia tego linku użyć adresu URL określonego przez regułę. W przeciwnym razie do utworzenia tego linku użyj adresu URL zawartego w polu `provider` obrazu.  
  
Poniżej przedstawiono przykład, który zawiera pole `provider` obrazu i reguły umowne. Ponieważ przykład zawiera reguły umowne, należy zignorować pole `provider` obrazu i zastosować regułę `MediaAttribution`.  
  
![Określanie autorstwa multimediów](./media/mediaattribution.png)  

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki start dla języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
