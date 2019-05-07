---
title: DatetimeV2 ze wstępnie utworzonych jednostek
titleSuffix: Azure
description: Ten artykuł zawiera datetimeV2 informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e7577dcf4859b1192121fe0406d0efb63a9f5990
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148636"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 wstępnie utworzone jednostki dla aplikacji usługi LUIS

**DatetimeV2** wstępnie utworzone jednostki wyodrębnia wartości daty i godziny. Rozwiąż te wartości w standardowym formacie dotyczących programów klienckich do korzystania. Gdy wypowiedź znajdują się daty lub godziny, która nie została ukończona, LUIS obejmuje _zarówno w przeszłości, jak i w przyszłości wartości_ w odpowiedzi punktu końcowego. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający datetimeV2 do intencji aplikacji. 

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) repozytorium GitHub

## <a name="example-json"></a>Przykładowy plik JSON 
Poniższy przykład odpowiedź w formacie JSON zawiera `datetimeV2` jednostki o podtypem `datetime`. Przykłady innych typów jednostek datetimeV2, zobacz [podtypy datetimeV2](#subtypes-of-datetimev2)</a>.

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

|Nazwa właściwości |Typ właściwości i opis|
|---|---|
|Jednostka|**ciąg** — tekst został wyodrębniony z wypowiedź z typem daty, godziny, zakres dat lub zakresu czasu.|
|type|**ciąg** — jest to jeden z [podtypy datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** — indeks w wypowiedź, od której rozpoczyna się jednostki.|
|endIndex|**int** — indeks w wypowiedź, w którym kończy się jednostki.|
|rozwiązanie|Ma `values` tablica, która ma jeden, dwa lub cztery [wartości rozdzielczości](#values-of-resolution).|
|end|Wartość końcowa czasu lub zakres dat, w tym samym formacie co `value`. Używany tylko, jeśli `type` jest `daterange`, `timerange`, lub `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

**DatetimeV2** wstępnie utworzone jednostki ma następujące podtypy oraz przykłady każdego z nich znajdują się w tabeli znajdującej się poniżej:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

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
|type|Podtyp, który może być jednym z następujących elementów: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|value|**Opcjonalnie.** Obiekt daty/godziny w formacie yyyy:MM:dd (Data): mm: ss (czas) yyyy:MM:dd: mm: ss (Data/godzina). Jeśli `type` jest `duration`, wartość jest to liczba sekund (czas trwania) <br/> Używany tylko, jeśli `type` jest `datetime` lub `date`, `time`, lub "czas trwania.|

## <a name="valid-date-values"></a>Wartości prawidłowej daty

**DatetimeV2** obsługuje daty wypadające między następujących zakresów:

| Min. | Maks. |
|----------|-------------|
| 1 stycznia 1900   | 31 grudnia 2099 |

## <a name="ambiguous-dates"></a>Niejednoznaczne daty

Jeśli w przeszłości, lub przyszłe, może być data, LUIS zawiera obie wartości. Przykładem jest wypowiedź zawierającą miesiąc i datę bez roku.  

Na przykład biorąc pod uwagę wypowiedź "2 maja":
* Jeśli dzisiaj jest maj 2017 3, LUIS zapewnia "2017-05-02" i "2018-05-02" jako wartości. 
* Dzisiaj po maja 2017 r. 1. Usługa LUIS zapewnia "2016-05-02" i "2017-05-02" jako wartości.

Poniższy przykład pokazuje rozwiązanie "2 maja" jednostki. Tego rozwiązania przyjęto założenie, że dziś jest data między maj 2017 2 i 1. w maju 2018 r.
Pola z `X` w `timex` pola są części daty, które nie są jawnie określone w wypowiedź.

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Przykłady rozpoznawania zakresu Data liczbowych daty

`datetimeV2` Jednostki wyodrębnia zakresów daty i godziny. `start` i `end` pola określają początek i koniec zakresu. Wypowiedź "Maja 2 do 5 maja.", udostępnia usługi LUIS **daterange** wartości zarówno dla bieżącego roku i następny rok. W `timex` pola `XXXX` wartości wskazują niejednoznaczności roku. `P3D` Wskazuje okres trzech dni.

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Przykłady rozpoznawania zakresu dat dzień tygodnia

W poniższym przykładzie pokazano, jak korzysta z usługi LUIS **datetimeV2** rozpoznać wypowiedź "Wtorek na czwartek". W tym przykładzie bieżąca data jest 19 czerwca. Obejmuje usługi LUIS **daterange** wartości dla obu zakresów dat, które poprzedzają i postępuj zgodnie z bieżącą datą.

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
Tablica wartości ma dwa elementy w czasie, jeśli czas lub zakres czasu jest niejednoznaczny. W przypadku niejednoznaczny czas wartości mają zarówno przed południem and P.M. czasy.

## <a name="time-range-resolution-example"></a>Przykład rozwiązywania zakresu czasu

W poniższym przykładzie pokazano, jak korzysta z usługi LUIS **datetimeV2** rozpoznać wypowiedź, która ma zakres czasu.

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

## <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API 3.x

Odpowiedź DatetimeV2 JSON została zmieniona w wersji 3 interfejsu API. 

Zmiany z interfejsu API w wersji 2:
* `datetimeV2.timex.type` Właściwość nie jest już jest zwracany, ponieważ jest on zwracany na poziomie nadrzędnym `datetimev2.type`. 
* `datetimeV2.timex` Właściwości została zmieniona na `datetimeV2.value`.

Aby uzyskać wypowiedź `8am on may 2nd 2017`, wersja V3 DatetimeV2 to:

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

Następujący kod JSON jest `verbose` parametr `false`:

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

## <a name="deprecated-prebuilt-datetime"></a>Przestarzałe wbudowanych daty/godziny

`datetime` Wstępnie utworzone jednostki jest przestarzały i zastąpiony **datetimeV2**. 

Aby zastąpić `datetime` z `datetimeV2` w swojej aplikacji usługi LUIS, wykonaj następujące czynności:

1. Otwórz **jednostek** okienko interfejsu sieci web usługi LUIS. 
2. Usuń **daty/godziny** wstępnie utworzone jednostki.
3. Kliknij przycisk **Dodawanie wstępnie utworzone jednostki**
4. Wybierz **datetimeV2** i kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wymiaru](luis-reference-prebuilt-dimension.md), [e-mail](luis-reference-prebuilt-email.md) jednostek i [numer](luis-reference-prebuilt-number.md). 

