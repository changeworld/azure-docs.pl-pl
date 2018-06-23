---
title: Odwołanie LUIS wbudowane datetimeV2 jednostek - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera datetimeV2 informacji wbudowane jednostki opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 261f6f27c39c280efdcd070888d735374a473c85
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321890"
---
# <a name="datetimev2-entity"></a>DatetimeV2 jednostki

**DatetimeV2** wbudowane jednostki wyodrębnia wartości daty i godziny. Rozwiązania te wartości w standardowym formacie dla programów klienckich korzystać. Jeśli utterance daty lub godziny, która nie jest pełną, LUIS obejmuje _zarówno w przeszłości, jak i w przyszłości wartości_ w odpowiedzi punktu końcowego. Ponieważ była już uczona tej jednostki, jest konieczne Dodaj zniesławiających przykład zawierający datetimeV2 do lokalizacji docelowych aplikacji. 

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) repozytorium Github

## <a name="example-json"></a>Przykład JSON 
Poniższy przykład odpowiedź w formacie JSON ma `datetimeV2` jednostki o podtypem `datetime`. Przykłady innych typów jednostek datetimeV2, zobacz [podtypów datetimeV2](#subtypes-of-datetimev2)</a>.

```JSON
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Opisy właściwości JSON

|Nazwa właściwości |Typ właściwości i opis|
|---|---|
|Jednostka|**ciąg** -tekst wyodrębniony z utterance z typem daty, godziny, zakres dat lub przedział czasu.|
|type|**ciąg** — jeden z [podtypów datetimeV2](#subtypes-of-datetimev2)
|Wartość startIndex|**int** — indeks w utterance, od której rozpoczyna się jednostki.|
|Element endIndex|**int** — indeks w utterance, w którym kończy się jednostki.|
|rozwiązanie|Ma `values` tablica, która zawiera jedną, dwie lub cztery [wartości rozdzielczości](#values-of-resolution).|
|end|Wartość końcowa czasu lub zakres dat, w tym samym formacie co `value`. Używana, tylko jeśli `type` jest `daterange`, `timerange`, lub `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Podtypów datetimeV2

**DatetimeV2** wbudowane jednostka ma następujące podtypy i przykłady każdego znajdują się w tabeli poniżej:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Wartości rozdzielczości
* Tablica ma jeden element, jeśli daty lub godziny w utterance jest w pełni określonego i jednoznaczna.
* Tablica ma dwa elementy, jeśli wartość datetimeV2 jest niejednoznaczna. Niejednoznaczności obejmuje braku danego roku, czas lub przedział czasu. Zobacz [niejednoznaczne daty](#ambiguous-dates) przykłady. Gdy czas jest niejednoznaczny na godzinę lub godzinie, obie wartości są uwzględniane.
* Tablica ma cztery elementy, jeśli utterance ma dwa elementy o niejednoznaczności. Tę niejednoznaczność zawiera elementy, które mają:
  * Datę lub zakres dat, który jest niejednoznaczny co rok
  * Zakres czasu, który jest niejednoznaczny co godzinę lub czasu or P.M. Na przykład 3 kwietnia 3:00.

Każdy element `values` Tablica może zawierać następujące pola: 

|Nazwa właściwości|Opis właściwości|
|--|--|
|Timex|czas, datę lub zakres dat, wyrażone w formacie TIMEX, który następuje [standardem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i atrybuty TIMEX3 przy użyciu języka TimeML adnotacji. Ta adnotacja jest opisany w [wytyczne TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Podtyp, który może być jedną z następujących elementów: datetime, dat, czasu, daterange, timerange, datetimerange, czas trwania, zestawu.|
|wartość|**Opcjonalne.** Obiekt datetime w yyyy:MM:dd formacie (Data) (time) gg yyyy:MM:dd HH: mm: (datetime). Jeśli `type` jest `duration`, wartość jest to liczba sekund (czas trwania) <br/> Używana, tylko jeśli `type` jest `datetime` lub `date`, `time`, lub "duration.|

## <a name="valid-date-values"></a>Wartości prawidłowa data.

**DatetimeV2** obsługuje daty wypadające między następujących zakresów:

| Min. | Maks. |
|----------|-------------|
| 1 stycznia 1900 roku.   | 31 grudnia 2099 |

## <a name="ambiguous-dates"></a>Niejednoznaczne daty

Jeśli w przeszłości lub przyszłe, może być data, LUIS zapewnia obu wartości. Przykładem jest utterance, która obejmuje Data bez rok i miesiąc.  

Na przykład podane utterance "Może 2":
* Jeśli dzisiaj 2017 3 maja, LUIS zapewnia "2017-05-02" i "2018-05-02" jako wartości. 
* Dzisiaj po 2017 1 maja LUIS zapewnia "2016-05-02" i "2017-05-02" jako wartości.

W poniższym przykładzie przedstawiono rozwiązanie jednostki "może 2". To rozwiązanie zakłada, że dzisiaj Data między maja 2017 2 i 1 2018 maja.
Pola z `X` w `timex` części daty, które nie są jawnie określone w utterance są pola.

```JSON
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
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Data zakresu rozpoznawania przykłady dla daty numeryczne

`datetimeV2` Jednostki wyodrębnia zakresy daty i godziny. `start` i `end` pola Określ początek i koniec zakresu. Zapewnia LUIS "Może 2 do 5. może" utterance, **daterange** wartości dla bieżącego roku i następnym roku. W `timex` pola `XXXX` wartości wskazują niejednoznaczności roku. `P3D` Wskazuje okres czasu jest długa trzy dni.

```JSON
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
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Przykłady rozpoznawania zakresu dat dzień tygodnia

W poniższym przykładzie pokazano, jak używa LUIS **datetimeV2** rozwiązywać utterance "Wtorek na czwartek". W tym przykładzie 19 czerwca jest data bieżąca. Obejmuje LUIS **daterange** wartości dla obu zakresów daty, które należy poprzedzić i postępuj zgodnie z bieżącą datą.

```JSON
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
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Niejednoznaczny czas
Tablica wartości ma dwa elementy czasu, jeśli czas lub przedział czasu jest niejednoznaczny. W przypadku niejednoznaczny czas wartości mają zarówno od godziny and P.M. razy.

## <a name="time-range-resolution-example"></a>Przykład rozpoznawania zakresu czasu

W poniższym przykładzie pokazano, jak używa LUIS **datetimeV2** rozwiązywać utterance, która ma zakres czasu.

```
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

## <a name="deprecated-prebuilt-datetime"></a>Przestarzałe wbudowane daty/godziny

`datetime` Wbudowane jednostki jest przestarzałe i zastępowane [ `datetimeV2` ](#builtindatetimev2). 

Aby zastąpić `datetime` z `datetimeV2` w aplikacji LUIS, wykonaj następujące czynności:

1. Otwórz **jednostek** okienko LUIS interfejsu sieci web. 
2. Usuń **datetime** wbudowane jednostki.
3. Kliknij przycisk **dodania jednostki wbudowane**
4. Wybierz **datetimeV2** i kliknij przycisk **zapisać**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wymiaru](luis-reference-prebuilt-dimension.md), [e-mail](luis-reference-prebuilt-email.md) jednostek, a [numer](luis-reference-prebuilt-number.md). 

