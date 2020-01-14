---
title: Wstępnie skompilowane jednostki DatetimeV2 — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera datetimeV2 informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 8c29ebd675bb6af66203c13824dacbe9ea2421a2
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732799"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 prekompilowaną jednostkę dla aplikacji LUIS

**DatetimeV2** wstępnie utworzone jednostki wyodrębnia wartości daty i godziny. Rozwiąż te wartości w standardowym formacie dotyczących programów klienckich do korzystania. Gdy wypowiedź znajdują się daty lub godziny, która nie została ukończona, LUIS obejmuje _zarówno w przeszłości, jak i w przyszłości wartości_ w odpowiedzi punktu końcowego. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający datetimeV2 do intencji aplikacji.

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) repozytorium GitHub.

## <a name="example-json"></a>Przykładowy plik JSON

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`8am on may 2nd 2019`

#### <a name="v3-responsetab1-1"></a>[Odpowiedź v3](#tab/1-1)

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

#### <a name="v3-verbose-responsetab1-2"></a>[Pełna odpowiedź w wersji 3](#tab/1-2)

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

#### <a name="v2-responsetab1-3"></a>[Odpowiedź w wersji 2](#tab/1-3)

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

|Nazwa właściwości |Typ właściwości i opis|
|---|---|
|Jednostka|**ciąg** — tekst został wyodrębniony z wypowiedź z typem daty, godziny, zakres dat lub zakresu czasu.|
|type|**ciąg** — jest to jeden z [podtypy datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** — indeks w wypowiedź, od której rozpoczyna się jednostki.|
|endIndex|**int** — indeks w wypowiedź, w którym kończy się jednostki.|
|rozwiązanie|Ma `values` tablica, która ma jeden, dwa lub cztery [wartości rozdzielczości](#values-of-resolution).|
|end|Wartość końcowa czasu lub zakres dat, w tym samym formacie co `value`. Używany tylko, jeśli `type` jest `daterange`, `timerange`, lub `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

**DatetimeV2** wstępnie utworzone jednostki ma następujące podtypy oraz przykłady każdego z nich znajdują się w tabeli znajdującej się poniżej:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Wartości rozdzielczości
* Tablica ma jeden element, jeśli datę lub godzinę w wypowiedź jest w pełni określony i jednoznaczna.
* Tablica ma dwa elementy, jeśli wartość datetimeV2 jest niejednoznaczna. Niejednoznaczności obejmuje braku konkretnego roku, czasu lub zakresu czasu. Zobacz [niejednoznaczne daty](#ambiguous-dates) przykłady. Kiedy czas jest niejednoznaczny na godzinę lub po południu, obie wartości są uwzględniane.
* Tablica ma cztery elementy, jeśli wypowiedź ma dwa elementy o niejednoznaczności. Tę niejednoznaczność zawiera elementy, które mają:
  * Datę lub zakres dat, który jest niejednoznaczny, co rok
  * Zakres czasu, który jest niejednoznaczny co godzinę lub czasu or P.M. Na przykład 3:00 3 kwietnia.

Każdy element obiektu `values` macierzy może zawierać następujące pola:

|Nazwa właściwości|Opis właściwości|
|--|--|
|Timex|Godzina, data lub zakresu dat wyrażonych w formacie TIMEX, który następuje po [standardem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i atrybuty TIMEX3 przy użyciu języka TimeML adnotacji. Ta adnotacja jest opisana w [wytycznych TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|Funkcja|termin używany do opisywania sposobu używania wartości, takiej jak `before`, `after`.|
|type|Podtyp, który może być jednym z następujących elementów: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|wartość|**Opcjonalnie.** Obiekt DateTime w formacie RRRR-MM-DD (Date), gg: mm: SS (Time) RRRR-MM-DD GG: mm: SS (DateTime). Jeśli `type` jest `duration`, wartość jest to liczba sekund (czas trwania) <br/> Używany tylko, jeśli `type` jest `datetime` lub `date`, `time`, lub "czas trwania.|

## <a name="valid-date-values"></a>Wartości prawidłowej daty

**DatetimeV2** obsługuje daty wypadające między następujących zakresów:

| Min. | Maks. |
|----------|-------------|
| 1 stycznia 1900   | 31 grudnia 2099 |

## <a name="ambiguous-dates"></a>Niejednoznaczne daty

Jeśli w przeszłości, lub przyszłe, może być data, LUIS zawiera obie wartości. Przykładem jest wypowiedź zawierającą miesiąc i datę bez roku.

Na przykład, uwzględniając następujące wypowiedź:

`May 2nd`

* Jeśli dzisiaj jest maj 2017 3, LUIS zapewnia "2017-05-02" i "2018-05-02" jako wartości.
* Dzisiaj po maja 2017 r. 1. Usługa LUIS zapewnia "2016-05-02" i "2017-05-02" jako wartości.

Poniższy przykład pokazuje rozwiązanie "2 maja" jednostki. Tego rozwiązania przyjęto założenie, że dziś jest data między maj 2017 2 i 1. w maju 2018 r.
Pola z `X` w `timex` pola są części daty, które nie są jawnie określone w wypowiedź.

## <a name="date-resolution-example"></a>Przykład rozpoznawania dat


Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`May 2nd`

#### <a name="v3-responsetab2-1"></a>[Odpowiedź v3](#tab/2-1)

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

#### <a name="v3-verbose-responsetab2-2"></a>[Pełna odpowiedź w wersji 3](#tab/2-2)

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

#### <a name="v2-responsetab2-3"></a>[Odpowiedź w wersji 2](#tab/2-3)

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Przykłady rozpoznawania zakresu Data liczbowych daty

`datetimeV2` Jednostki wyodrębnia zakresów daty i godziny. `start` i `end` pola określają początek i koniec zakresu. W przypadku `May 2nd to May 5th`wypowiedź LUIS zawiera wartości **DateRange** dla bieżącego roku i następnego roku. W `timex` pola `XXXX` wartości wskazują niejednoznaczności roku. `P3D` Wskazuje okres trzech dni.

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`May 2nd to May 5th`

#### <a name="v3-responsetab3-1"></a>[Odpowiedź v3](#tab/3-1)

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


#### <a name="v3-verbose-responsetab3-2"></a>[Pełna odpowiedź w wersji 3](#tab/3-2)

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

#### <a name="v2-responsetab3-3"></a>[Odpowiedź w wersji 2](#tab/3-3)

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Przykłady rozpoznawania zakresu dat dzień tygodnia

Poniższy przykład pokazuje, jak LUIS używa **datetimeV2** do rozwiązywania `Tuesday to Thursday`wypowiedź. W tym przykładzie bieżąca data jest 19 czerwca. Obejmuje usługi LUIS **daterange** wartości dla obu zakresów dat, które poprzedzają i postępuj zgodnie z bieżącą datą.

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`Tuesday to Thursday`

#### <a name="v3-responsetab4-1"></a>[Odpowiedź v3](#tab/4-1)

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

#### <a name="v3-verbose-responsetab4-2"></a>[Pełna odpowiedź w wersji 3](#tab/4-2)

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

#### <a name="v2-responsetab4-3"></a>[Odpowiedź w wersji 2](#tab/4-3)

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
Tablica wartości ma dwa elementy w czasie, jeśli czas lub zakres czasu jest niejednoznaczny. W przypadku niejednoznaczny czas wartości mają zarówno przed południem and P.M. czasy.

## <a name="time-range-resolution-example"></a>Przykład rozwiązywania zakresu czasu

Odpowiedź JSON DatetimeV2 została zmieniona w interfejsie API v3. W poniższym przykładzie pokazano, jak korzysta z usługi LUIS **datetimeV2** rozpoznać wypowiedź, która ma zakres czasu.

Zmiany z interfejsu API v2:
* Właściwość `datetimeV2.timex.type` nie jest już zwracana, ponieważ jest zwracana na poziomie nadrzędnym, `datetimev2.type`.
* Nazwa właściwości `datetimeV2.value` została zmieniona na `datetimeV2.timex`.

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`from 6pm to 7pm`

#### <a name="v3-responsetab5-1"></a>[Odpowiedź v3](#tab/5-1)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

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
#### <a name="v3-verbose-responsetab5-2"></a>[Pełna odpowiedź w wersji 3](#tab/5-2)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

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
#### <a name="v2-responsetab5-3"></a>[Odpowiedź w wersji 2](#tab/5-3)

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

## <a name="time-resolution-example"></a>Przykład czasu rozwiązania

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`8am`

#### <a name="v3-responsetab6-1"></a>[Odpowiedź v3](#tab/6-1)

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
#### <a name="v3-verbose-responsetab6-2"></a>[Pełna odpowiedź w wersji 3](#tab/6-2)

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
#### <a name="v2-responsetab6-3"></a>[Odpowiedź w wersji 2](#tab/6-3)

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

## <a name="deprecated-prebuilt-datetime"></a>Przestarzałe wbudowanych daty/godziny

`datetime` Wstępnie utworzone jednostki jest przestarzały i zastąpiony **datetimeV2**.

Aby zastąpić `datetime` z `datetimeV2` w swojej aplikacji usługi LUIS, wykonaj następujące czynności:

1. Otwórz **jednostek** okienko interfejsu sieci web usługi LUIS.
2. Usuń **daty/godziny** wstępnie utworzone jednostki.
3. Kliknij przycisk **Dodawanie wstępnie utworzone jednostki**
4. Wybierz **datetimeV2** i kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o [wymiaru](luis-reference-prebuilt-dimension.md), [e-mail](luis-reference-prebuilt-email.md) jednostek i [numer](luis-reference-prebuilt-number.md).

