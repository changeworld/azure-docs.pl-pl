---
title: Wyodrębnianie danych — usługa LUIS
description: Wyodrębnij dane z tekstu wypowiedź z intencji i jednostek. Dowiedz się, jakiego rodzaju dane można wyodrębnić ze zrozumienia języka (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221085"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Wyodrębnianie danych z tekstu wypowiedź z intencjami i encjami
Usługa LUIS umożliwia uzyskanie informacji z wypowiedzi użytkownika w języku naturalnym. Informacje są wyodrębniane w taki sposób, że mogą być używane przez program, aplikację lub czat bot do podjęcia działań. W poniższych sekcjach dowiedzieć się, jakie dane są zwracane z intencji i jednostek z przykładami JSON.

Najtrudniejsze dane do wyodrębnienia to dane nauczone maszynowo, ponieważ nie są zgodne z dokładnym tekstem. Wyodrębnianie danych [jednostek](luis-concept-entity-types.md) nauczanych maszyn musi być częścią [cyklu tworzenia,](luis-concept-app-iteration.md) dopóki nie masz pewności, że otrzymasz oczekiwane dane.

## <a name="data-location-and-key-usage"></a>Lokalizacja danych i użycie klucza
Usługa LUIS udostępnia dane z opublikowanego [punktu końcowego](luis-glossary.md#endpoint). **Żądanie HTTPS** (POST lub GET) zawiera wypowiedź, a także niektóre opcjonalne konfiguracje, takie jak środowiska przejściowe lub produkcyjne.

#### <a name="v2-prediction-endpoint-request"></a>[Żądanie punktu końcowego przewidywania wersji 2](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[Żądanie punktu końcowego przewidywania wersji 3](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

Jest `appID` on dostępny na stronie **Ustawienia** aplikacji usługi LUIS, a `/apps/`także część adresu URL (po) podczas edytowania tej aplikacji usługi LUIS. Jest `subscription-key` kluczem punktu końcowego używanym do wykonywania zapytań o aplikację. Podczas nauki usługi LUIS można używać bezpłatnego klucza tworzenia/rozruchu, ale ważne jest, aby zmienić klucz punktu końcowego na klucz [obsługujący oczekiwane użycie usługi LUIS.](luis-boundaries.md#key-limits) Urządzenie `timezoneOffset` jest kilka minut.

**Odpowiedź HTTPS** zawiera wszystkie intencji i informacji o jednostce usługi LUIS można określić na podstawie bieżącego opublikowanego modelu punktu końcowego przemieszczania lub produkcji. Adres URL punktu końcowego znajduje się w witrynie sieci Web [usługi LUIS,](luis-reference-regions.md) w sekcji **Zarządzanie,** na **klucze i punkty końcowe** strony.

## <a name="data-from-intents"></a>Dane z intencji
Dane podstawowe to **najwyższa nazwa intencji**oceniania . Odpowiedź punktu końcowego jest:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|
|Intencja|Ciąg|topScoringIntent.intent|"GetStoreInfo"|

Jeśli twój chatbot lub aplikacja do wywoływania usługi LUIS podejmuje decyzję na podstawie więcej niż jednego wyniku intencji, zwróć wyniki wszystkich intencji.


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

Ustaw parametr querystring, `verbose=true`. Odpowiedź punktu końcowego jest:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Ustaw parametr querystring, `show-all-intents=true`. Odpowiedź punktu końcowego jest:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

Intencje są uporządkowane od najwyższego do najniższego wyniku.

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|Wynik|
|--|--|--|--|:--|
|Intencja|Ciąg|intencji[0].intent|"GetStoreInfo"|0.984749258|
|Intencja|Ciąg|intencje[1].intent|"Brak"|0.0168218873|

Jeśli dodasz wstępnie utworzone domeny, nazwa intencji wskazuje `Utilties` `Communication` domenę, taką jak lub, a także intencję:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

|Domain|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|--|
|Narzędzia|Intencja|Ciąg|intencji[0].intent|"<b>Narzędzia</b>. ShowNext"|
|Komunikacja|Intencja|Ciąg|intencje[1].intent|<b>Komunikacji</b>. StartOver"|
||Intencja|Ciąg|intencji[2].intent|"Brak"|


## <a name="data-from-entities"></a>Dane z jednostek
Większość chatbotów i aplikacji potrzebuje więcej niż nazwa intencji. Te dodatkowe, opcjonalne dane pochodzą z jednostek wykrytych w wypowiedź. Każdy typ jednostki zwraca różne informacje o dopasowaniu.

Pojedynczy wyraz lub fraza w wypowiedź może odpowiadać więcej niż jednej jednostce. W takim przypadku każda pasująca jednostka jest zwracana z jego wynikiem.

Wszystkie jednostki są zwracane w **tablicy jednostek** odpowiedzi z punktu końcowego:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>Tokenizowana jednostka zwrócona

Przejrzyj [obsługę tokenów](luis-language-support.md#tokenization) w usłudze LUIS.

## <a name="simple-entity-data"></a>Proste dane encji

[Prosta jednostka](reference-entity-simple.md) jest wartością nauczaną maszynowo. Może to być słowo lub fraza.

## <a name="composite-entity-data"></a>Złożone dane encji

Jednostka [złożona](reference-entity-composite.md) składa się z innych jednostek, takich jak wstępnie utworzone jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całą encję.

## <a name="list-entity-data"></a>Lista danych encji

[Encje listy](reference-entity-list.md) reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj funkcji **Poleć,** aby wyświetlić sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jedna encja listy o tej samej wartości, każda jednostka jest zwracana w kwerendzie punktu końcowego.

## <a name="prebuilt-entity-data"></a>Wstępnie utworzone dane encji
[Wstępnie utworzone](luis-concept-entity-types.md) jednostki są odnajdywane na podstawie dopasowania wyrażenia regularnego przy użyciu projektu aparaty rozpoznawania typu open [source-Text.](https://github.com/Microsoft/Recognizers-Text) Wstępnie utworzone jednostki są zwracane w tablicy jednostek `builtin::`i używają nazwy typu poprzedzone . Poniższy tekst jest wypowiedź przykład z zwróconych wstępnie utworzonych jednostek:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Bez parametru querystring: `verbose=true`

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

Z parametrem querystring: `verbose=true`

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *
## <a name="regular-expression-entity-data"></a>Dane encji wyrażenia regularnego

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) wyodrębnia jednostkę na podstawie wzorca wyrażenia regularnego, który podasz.

## <a name="extracting-names"></a>Wyodrębnianie nazw
Uzyskiwanie nazw z wypowiedź jest trudne, ponieważ nazwa może być prawie dowolną kombinacją liter i słów. W zależności od typu wyodrębniania nazwy dostępnych jest kilka opcji. Poniższe sugestie nie są regułami, ale bardziej wytycznymi.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Dodawanie wstępnie utworzonych encji PersonName i GeographyV2

[Jednostki PersonName](luis-reference-prebuilt-person.md) i [GeographyV2](luis-reference-prebuilt-geographyV2.md) są dostępne w niektórych [kulturach językowych.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Nazwiska osób

Nazwa ludzi może mieć niewielki format w zależności od języka i kultury. Użyj wstępnie utworzonej **[jednostki personName](luis-reference-prebuilt-person.md)** lub **[prostej jednostki](luis-concept-entity-types.md#simple-entity)** z [rolami](luis-concept-roles.md) imienia i nazwiska.

Jeśli używasz proste jednostki, upewnij się, aby podać przykłady, które używają imię i nazwisko w różnych częściach wypowiedź, wypowiedzi o różnych długościach i wypowiedzi we wszystkich intencji, w tym Brak intencji. [Regularnie przeglądaj](luis-how-to-review-endoint-utt.md) wypowiedzi punktów końcowych, aby oznaczyć wszystkie nazwy, które nie zostały poprawnie przewidziane.

### <a name="names-of-places"></a>Nazwy miejsc

Nazwy lokalizacji są ustawione i znane, takie jak miasta, powiaty, stany, prowincje i kraje/regiony. Użyj wstępnie utworzonej **[jednostki geographyV2,](luis-reference-prebuilt-geographyv2.md)** aby wyodrębnić informacje o lokalizacji.

### <a name="new-and-emerging-names"></a>Nowe i pojawiające się nazwy

Niektóre aplikacje muszą być w stanie znaleźć nowe i pojawiające się nazwy, takie jak produkty lub firmy. Te typy nazw są najtrudniejszym typem wyodrębniania danych. Zacznij od **[prostej encji](luis-concept-entity-types.md#simple-entity)** i dodaj [listę fraz](luis-concept-feature.md). [Regularnie przeglądaj](luis-how-to-review-endoint-utt.md) wypowiedzi punktów końcowych, aby oznaczyć wszystkie nazwy, które nie zostały poprawnie przewidziane.

## <a name="pattern-roles-data"></a>Dane ról wzorca
Role są kontekstowe różnice jednostek.


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

Nazwa encji to `Location`, `Origin` `Destination`z dwiema rolami i .

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

W wersji 3 **nazwa roli** jest podstawową nazwą obiektu.

Nazwa encji to `Location`, `Origin` `Destination`z dwiema rolami i .

Bez parametru querystring: `verbose=true`

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

Z parametrem querystring: `verbose=true`

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Dane jednostki Pattern.any

[Pattern.any](reference-entity-pattern-any.md) jest symbolem zastępczym o zmiennej długości używanym tylko w wypowiedź szablonu wzorca, aby oznaczyć, gdzie jednostka zaczyna się i kończy.

## <a name="sentiment-analysis"></a>Analiza tonacji
Jeśli jest skonfigurowana analiza tonacji, odpowiedź json usługi LUIS zawiera analizę tonacji. Dowiedz się więcej o analizie opinii w dokumentacji [analizy tekstu.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)

### <a name="sentiment-data"></a>Dane dotyczące tonacji
Dane o nastrojach to wynik od 1 do 0 wskazujący pozytywny (bliżej 1) lub ujemny (bliżej 0) sentyment danych.

Gdy kultura `en-us`jest , odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Dla wszystkich innych kultur odpowiedzią jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Dane encji wyodrębniania fraz kluczowych
Jednostka wyodrębniania fraz kluczowych zwraca kluczowe frazy w wypowiedź, dostarczone przez [analizę tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Bez parametru querystring: `verbose=true`

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

Z parametrem querystring: `verbose=true`

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Dane pasujące do wielu encji

Usługa LUIS zwraca wszystkie jednostki wykryte w wypowiedź. W rezultacie twój chatbot może być potrzebny do podjęcia decyzji na podstawie wyników. Wypowiedź może mieć wiele jednostek w wypowiedź:

`book me 2 adult business tickets to paris tomorrow on air france`

Punkt końcowy usługi LUIS można odnajdyć te same dane w różnych jednostkach.

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Bez `verbose=true` jako querystring parametr.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

Z `verbose=true` jako parametr querystring.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Dane pasujące do wielu encji listy

Jeśli wyraz lub fraza pasuje do więcej niż jednej encji listy, kwerenda punktu końcowego zwraca każdą encję List.

W przypadku `when is the best time to go to red rock?`kwerendy , a `red` aplikacja ma słowo na więcej niż jednej liście, usługa LUIS rozpoznaje wszystkie jednostki i zwraca tablicę jednostek jako część odpowiedzi punktu końcowego JSON:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

Bez `verbose=true` ciągu zapytania:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


Z `verbose=true` w ciągu zapytania:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie jednostek,](luis-how-to-add-entities.md) aby dowiedzieć się więcej o dodaniu jednostek do aplikacji usługi LUIS.
