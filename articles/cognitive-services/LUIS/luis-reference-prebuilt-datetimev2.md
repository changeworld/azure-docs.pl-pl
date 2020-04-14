---
title: Wstępnie utworzone jednostki DatetimeV2 — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie utworzone informacje o jednostce datetimeV2 w zrozumieni język (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.author: diberry
ms.openlocfilehash: 33f8b787119e1c5d6d1a1bb28c94d9791a1c048e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272614"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Wstępnie skompilowana jednostka DatetimeV2 dla aplikacji usługi LUIS

Wstępnie skompilowana encja **datetimeV2** wyodrębnia wartości daty i godziny. Te wartości są rozpoznawane w znormalizowanym formacie dla programów klienckich do wykorzystania. Gdy wypowiedź ma datę lub godzinę, która nie jest ukończona, usługa LUIS zawiera _zarówno przeszłe, jak i przyszłe wartości_ w odpowiedzi punktu końcowego. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać wypowiedzi przykład zawierający datetimeV2 do intencji aplikacji.

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 jest zarządzany z repozytorium GitHub [w tekście aparatów rozpoznawania.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>Przykład JSON

Poniżej przedstawiono następującą wypowiedź i jego częściową odpowiedź JSON.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Odpowiedź V2](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Nazwa właściwości |Typ i opis właściwości|
|---|---|
|Jednostka|**string** - Tekst wyodrębniony z wypowiedź z typem daty, godziny, zakresu dat lub zakresu czasu.|
|type|**string** - Jeden z [podtypów datetimeV2](#subtypes-of-datetimev2)
|Startindex|**int** — indeks w wypowiedź, w którym rozpoczyna się jednostka.|
|Endindex|**int** — indeks w wypowiedź, w którym kończy się jednostka.|
|rozwiązanie|Ma `values` tablicę, która ma jedną, dwie lub cztery [wartości rozdzielczości](#values-of-resolution).|
|end|Wartość końcowa godziny lub zakresu dat w tym `value`samym formacie co . Używane tylko `type` `daterange`wtedy, gdy jest , `timerange`lub`datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

Prekompilowana jednostka **datetimeV2** ma następujące podtypy, a przykłady każdego z nich znajdują się w poniższej tabeli:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Wartości rozdzielczości
* Tablica ma jeden element, jeśli data lub godzina w wypowiedź jest w pełni określony i jednoznaczne.
* Tablica ma dwa elementy, jeśli datetimeV2 wartość jest niejednoznaczna. Niejednoznaczność obejmuje brak określonego roku, czasu lub zakresu czasu. Zobacz [niejednoznaczne daty](#ambiguous-dates) dla przykładów. Gdy czas jest niejednoznaczny dla A.M. lub P.M., obie wartości są uwzględniane.
* Tablica ma cztery elementy, jeśli wypowiedź ma dwa elementy z niejednoznaczności. Ta niejednoznaczność obejmuje elementy, które mają:
  * Zakres dat lub dat niejednoznaczny w odniesieniu do roku
  * Przedział czasu lub czasu, który jest niejednoznaczny w odniesieniu do a.m. lub po południu. Na przykład 3:00 3 kwietnia.

Każdy element `values` tablicy może mieć następujące pola:

|Nazwa właściwości|Opis właściwości|
|--|--|
|Timex|zakres czasu, daty lub daty wyrażony w formacie TIMEX zgodnym ze [standardem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i atrybutami TIMEX3 dla adnotacji przy użyciu języka TimeML.|
|Mod|termin używany do opisywania sposobu `before`używania wartości, takich jak , `after`.|
|type|Podtyp, który może być jednym `datetime`z `date` `time`następujących `daterange` `timerange`elementów: , , , , , `datetimerange`, , `duration`. `set`|
|value|**Opcjonalne.** Obiekt datetime w formacie yyyy-MM-dd (data), HH:mm:ss (time) yyyy-MM-dd HH:mm:ss (datetime). Jeśli `type` `duration`tak, wartość jest liczbą sekund (czas trwania) <br/> Używane tylko `type` `datetime` wtedy, gdy jest lub `date`, `time`lub "czas trwania.|

## <a name="valid-date-values"></a>Prawidłowe wartości daty

**DatetimeV2** obsługuje daty między następującymi zakresami:

| Min. | Maks. |
|----------|-------------|
| 1 stycznia 1900   | 31 grudnia 2099 |

## <a name="ambiguous-dates"></a>Niejednoznaczne daty

Jeśli data może być w przeszłości lub w przyszłości, usługa LUIS zawiera obie wartości. Przykładem jest wypowiedź, która zawiera miesiąc i datę bez roku.

Na przykład biorąc pod uwagę następujące wypowiedź:

`May 2nd`

* Jeśli dzisiejsza data to 3 maja 2017 r., usługa LUIS udostępnia wartości zarówno "2017-05-02", jak i "2018-05-02" jako wartości.
* Gdy dzisiejsza data to 1 maja 2017 r., usługa LUIS udostępnia wartości zarówno "2016-05-02", jak i "2017-05-02" jako wartości.

Poniższy przykład przedstawia rozdzielczość jednostki "może 2nd". Uchwała zakłada, że dzisiejsza data jest datą między 2 maja 2017 a 1 maja 2018.
Pola `X` z `timex` w polu są części daty, które nie są jawnie określone w wypowiedź.

## <a name="date-resolution-example"></a>Przykład rozpoznawania dat


Poniżej przedstawiono następującą wypowiedź i jego częściową odpowiedź JSON.

`May 2nd`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-response"></a>[Odpowiedź V2](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Przykłady rozpoznawania zakresu dat dla daty numerycznej

Jednostka `datetimeV2` wyodrębnia zakresy dat i godzin. Pola `start` `end` i określają początek i koniec zakresu. Dla wypowiedź `May 2nd to May 5th`, usługa LUIS zawiera wartości **daterange** dla bieżącego roku i następnego roku. W `timex` tym polu `XXXX` wartości wskazują niejednoznaczność roku. `P3D`wskazuje, że okres wynosi trzy dni.

Poniżej przedstawiono następującą wypowiedź i jego częściową odpowiedź JSON.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-response"></a>[Odpowiedź V2](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Przykłady rozpoznawania zakresu dat dla dnia tygodnia

W poniższym przykładzie pokazano, jak usługa LUIS `Tuesday to Thursday`używa **datetimeV2** do rozwiązania wypowiedź . W tym przykładzie bieżąca data to 19 czerwca. Usługa LUIS zawiera wartości **datarange** dla obu zakresów dat, które poprzedzają i są zgodne z bieżącą datą.

Poniżej przedstawiono następującą wypowiedź i jego częściową odpowiedź JSON.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Odpowiedź V2](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Niejednoznaczny czas
Tablica wartości ma dwa elementy czasu, jeśli czas lub zakres czasu jest niejednoznaczny. Gdy jest niejednoznaczny czas, wartości mają zarówno A.M. i p.m. Razy.

## <a name="time-range-resolution-example"></a>Przykład rozdzielczości zakresu czasu

DatetimeV2 Odpowiedź JSON została zmieniona w interfejsie API V3. Poniższy przykład pokazuje, jak usługa LUIS używa **datetimeV2** do rozwiązania wypowiedź, która ma zakres czasu.

Zmiany z interfejsu API V2:
* `datetimeV2.timex.type`właściwość nie jest już zwracana, ponieważ `datetimev2.type`jest zwracana na poziomie nadrzędnym.
* Nazwa `datetimeV2.value` obiektu została zmieniona na `datetimeV2.timex`.

Poniżej przedstawiono następującą wypowiedź i jego częściową odpowiedź JSON.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/5-1)

Następujący JSON jest `verbose` z parametrem ustawionym na: `false`

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/5-2)

Następujący JSON jest `verbose` z parametrem ustawionym na: `true`

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-response"></a>[Odpowiedź V2](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Przykład rozdzielczości czasu

Poniżej przedstawiono następującą wypowiedź i jego częściową odpowiedź JSON.

`8am`

#### <a name="v3-response"></a>[Odpowiedź V3](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Odpowiedź pełne V3](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-response"></a>[Odpowiedź V2](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Przestarzały wstępnie utworzony datetime

`datetime` Wstępnie utworzony element jest przestarzały i zastąpiony przez **datetimeV2**.

Aby `datetime` zastąpić `datetimeV2` ją w aplikacji usługi LUIS, wykonaj następujące czynności:

1. Otwórz okienko **Jednostki** interfejsu internetowego usługi LUIS.
2. Usuń wstępnie skompilowany encję **datetime.**
3. Kliknij **pozycję Dodaj wstępnie skompilowany element**
4. Wybierz **datetimeV2** i kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Dowiedz się więcej o [wymiarze,](luis-reference-prebuilt-dimension.md)encjach [poczty e-mail](luis-reference-prebuilt-email.md) i [numerze](luis-reference-prebuilt-number.md).

