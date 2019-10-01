---
title: Wstępnie skompilowane jednostki DatetimeV2 — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach datetimeV2 w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4f46efaeddb0bfe789ef752abdd133c14da514da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677690"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 prekompilowaną jednostkę dla aplikacji LUIS

Prebudowana jednostka **datetimeV2** wyodrębnia wartości daty i godziny. Te wartości są rozwiązywane w standardowym formacie dla programów klienckich do użycia. Gdy wypowiedź ma datę lub godzinę, która nie została ukończona, LUIS zawiera _wartości przeszłe i przyszłe_ w odpowiedzi punktu końcowego. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej datetimeV2 do intencji aplikacji. 

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) repozytorium GitHub

## <a name="example-json"></a>Przykładowy kod JSON 
Następująca przykładowa odpowiedź JSON zawiera jednostkę `datetimeV2` z podtypem `datetime`. Przykłady innych typów jednostek datetimeV2 można znaleźć w tematach [podtypów datetimeV2](#subtypes-of-datetimev2)</a>.

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

## <a name="json-property-descriptions"></a>Opisy właściwości JSON

|Nazwa właściwości |Typ i opis właściwości|
|---|---|
|Jednostka|**ciąg** tekstowy wyodrębniony z wypowiedź z typem daty, godziny, zakresu dat lub zakresem czasu.|
|type|**ciąg** — jeden z [podtypów datetimeV2](#subtypes-of-datetimev2)
|Indeks|**int** — indeks w wypowiedź, w którym rozpoczyna się jednostka.|
|endIndex|**int** — indeks w wypowiedź, w którym następuje zakończenie obiektu.|
|Tłumaczenia|Ma tablicę `values`, która ma jedną, dwie lub cztery [wartości rozdzielczości](#values-of-resolution).|
|punktów|Wartość końcowa czasu lub zakresu dat w tym samym formacie co `value`. Używane tylko wtedy, gdy `type` jest `daterange`, `timerange` lub `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

Wstępnie utworzona jednostka **datetimeV2** ma następujące podtypy, a przykłady z nich są podane w poniższej tabeli:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Wartości rozwiązania
* Tablica ma jeden element, jeśli data lub godzina w wypowiedź jest w pełni określona i niejednoznaczna.
* Tablica ma dwa elementy, jeśli wartość datetimeV2 jest niejednoznaczna. Niejednoznaczność obejmuje brak określonego roku, godziny lub zakresu czasu. Zobacz [niejednoznaczne daty](#ambiguous-dates) dla przykładów. Kiedy czas jest niejednoznaczny dla A.M. lub P.M. są uwzględniane obie wartości.
* Tablica ma cztery elementy, jeśli wypowiedź ma dwa elementy z niejednoznaczności. Ta niejednoznaczność zawiera elementy, które mają:
  * Data lub zakres dat, który jest niejednoznaczny dla roku.
  * Czas lub zakres czasu, który jest niejednoznaczny od do rano lub P.M. Na przykład 3:00 kwietnia.

Każdy element tablicy `values` może mieć następujące pola: 

|Nazwa właściwości|Opis właściwości|
|--|--|
|Timex|godzina, Data lub zakres dat wyrażony w formacie TIMEX, który jest zgodny ze [standardem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i atrybutami TIMEX3 dla adnotacji przy użyciu języka TimeML. Ta adnotacja została opisana w [wytycznych](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf)dla firmy Timex.|
|type|Podtyp, który może być jednym z następujących elementów: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|wartość|**Obowiązkowe.** Obiekt DateTime w formacie RRRR: MM: DD (Date), gg: mm: SS (Time) rrrr: MM: DD HH: mm: SS (DateTime). Jeśli `type` jest `duration`, wartość jest liczbą sekund (czas trwania) <br/> Używane tylko wtedy, gdy `type` jest `datetime` lub `date`, `time` lub "Duration".|

## <a name="valid-date-values"></a>Prawidłowe wartości daty

**DatetimeV2** obsługuje daty między następującymi zakresami:

| Min. | Maks. |
|----------|-------------|
| 1 stycznia 1900   | 31 grudnia 2099 |

## <a name="ambiguous-dates"></a>Niejednoznaczne daty

Jeśli data może przypadać w przeszłości lub w przyszłości, LUIS zawiera obie wartości. Przykładem jest wypowiedź, który obejmuje miesiąc i datę bez roku.  

Na przykład wypowiedź "może być druga":
* Jeśli dzisiejszą datą jest trzecia 2017, LUIS ma zarówno "2017-05-02", jak i "2018-05-02" jako wartości. 
* Gdy dzisiejsza data wynosi 1 maja 2017, LUIS określa zarówno "2016-05-02", jak i "2017-05-02" jako wartości.

W poniższym przykładzie pokazano rozdzielczość jednostki "może być druga". W tym rozwiązaniu przyjęto założenie, że dzisiejsza data jest datą z przedziału od 2 maja 2017 do 1 maja 2018.
Pola z `X` w polu `timex` są częściami daty, które nie zostały jawnie określone w wypowiedź.

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Przykłady rozpoznawania zakresu dat dla daty numerycznej

Jednostka `datetimeV2` wyodrębnia zakresy dat i godzin. Pola `start` i `end` określają początek i koniec zakresu. Dla wypowiedź "maj od 1 maja do 5 maja", LUIS dostarcza wartości **DateRange** dla bieżącego roku i następnego roku. W polu `timex` wartości `XXXX` wskazują niejednoznaczność roku. `P3D` wskazuje, że okres jest dłuższy niż trzy dni.

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Przykłady rozpoznawania zakresu dat dla dnia tygodnia

W poniższym przykładzie pokazano, jak LUIS używa **datetimeV2** do rozwiązywania wypowiedź "wtorek do czwartku". W tym przykładzie bieżąca data to 19 czerwca. LUIS zawiera wartości **DateRange** dla obu zakresów dat, które poprzedzają bieżącą datę i obserwują ją.

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
## <a name="ambiguous-time"></a>Niejednoznaczny czas
Tablica wartości ma dwa elementy czasu, jeśli czas lub zakres czasu jest niejednoznaczny. Gdy istnieje niejednoznaczny czas, wartości mają zarówno wartość A.M. i P.M. trzykrotn.

## <a name="time-range-resolution-example"></a>Przykład rozdzielczości zakresu czasu

Poniższy przykład pokazuje, jak LUIS używa **datetimeV2** do rozwiązywania wypowiedź, który ma zakres czasu.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Odpowiedź JSON DatetimeV2 została zmieniona w interfejsie API v3. 

Zmiany z interfejsu API v2:
* Właściwość `datetimeV2.timex.type` nie jest już zwracana, ponieważ jest zwracana na poziomie nadrzędnym, `datetimev2.type`. 
* Nazwa właściwości `datetimeV2.timex` została zmieniona na `datetimeV2.value`.

W przypadku wypowiedź, `8am on may 2nd 2017`, wersja v3 DatetimeV2 jest:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
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
    }
}
```


* * * 

## <a name="deprecated-prebuilt-datetime"></a>Przestarzała prebudowana Data i godzina

Wstępnie utworzona jednostka `datetime` jest przestarzała i zastąpiona przez **datetimeV2**. 

Aby zamienić `datetime` na `datetimeV2` w aplikacji LUIS, wykonaj następujące czynności:

1. Otwórz okienko **jednostki** w interfejsie sieci Web Luis. 
2. Usuń wstępnie utworzoną jednostkę **DateTime** .
3. Kliknij pozycję **Dodaj prekompilowaną jednostkę**
4. Wybierz pozycję **datetimeV2** , a następnie kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej na temat [wymiaru](luis-reference-prebuilt-dimension.md), jednostek [poczty e-mail](luis-reference-prebuilt-email.md) i [liczby](luis-reference-prebuilt-number.md). 

