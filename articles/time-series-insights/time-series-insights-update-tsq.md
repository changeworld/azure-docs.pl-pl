---
title: Czas kwerendy serii | Dokumentacja firmy Microsoft
description: Axure Time Series Insights (wersja zapoznawcza), zapytania serii czasu
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: f5370d354833e9507d0794d7bc407a7ea2294e1a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856470"
---
# <a name="time-series-query"></a>Zapytania dla szeregów czasowych

**Czas kwerendy serii** (TSQ) ułatwia obliczeń i pobierać dane szeregów czasowych przechowywanych w Time Series Insights (TSI) na dużą skalę. TSQ korzysta z definicji obliczeniową w celu przekształcania i pobierać dane z magazynu. Odbywa się to z **modeli szeregów czasowych** (TSM) lub za pośrednictwem definicje zmiennych w tekście.

TSQ pobiera dane na dwa różne sposoby. TSQ może pobierać dane ponieważ odbywa się z dostawcą lub zmniejszyć danych lub Rekonstrukcja sygnały wykorzystaniu określonej metody, aby umożliwić klientom wykonywanie operacji przekształcania, łączyć i wykonywania obliczeń na danych szeregów czasowych.

Szczegółowe informacje szeregami czasowymi są dostępne za pośrednictwem natywnych aktualizacji TSI Explorer lub publiczny interfejs API powierzchni. TSQ oferuje również język wyrażeń, dzięki czemu możesz można tworzyć własne bardziej zaawansowanych usługa TSI Przesyła zapytania. Poniżej przedstawiono cele firmy Microsoft przy użyciu TSQ:

![Cel][1]

## <a name="core-apis"></a>Podstawowe interfejsy API

Poniżej przedstawiono podstawowe interfejsy API, firma Microsoft obsługuje.

![tsq][2]

### <a name="getmodelsettings-api"></a>getModelSettings interfejsu API

Interfejs API getModelSettings jest używane do zwracania automatycznie utworzony model dla środowisk klucza partycji.

GetModelSettings API przyjmuje następujące parametry:

* *Nazwa*: Nazwa modelu, o których chcesz pobrać.
* *timeSeriesIdProperties*

  * *Nazwa*
  * *type*

* *defaultTypeID*

#### <a name="getmodelsettings-json-request-and-response-example"></a>przykład kodu JSON getModelSettings żądań i odpowiedzi

Podane żądanie GET HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/modelSettings?api-version=API_VERSION
```

| Name (Nazwa) | Opis | Przykład |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Nazwa środowiska  | `environment123` |
| API_VERSION  |  Specyfikacja interfejsu API | `2018-11-01-preview` |

Odpowiedź:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

### <a name="gettypes-api"></a>getTypes interfejsu API

GetTypes interfejsu API zwraca wszystkie typy szeregów czasowych i ich skojarzone zmienne dla modelu.

GetTypes API przyjmuje następujące parametry:

* *typeId*: niezmienne Unikatowy identyfikator dla tego typu.
* *timeSeriesId*: **identyfikator serii czasu** jest unikatowym kluczem dla danych w strumieniu zdarzeń i modelu. Ten klucz jest TSI używa do podzielenia danych.
* *Opis*: opis typu.
* *hierarchyIds*: tablica skojarzone hierarchyIds dla typu.
* *instanceFields*:
  * *state*
  * *Miasto*

#### <a name="gettypes-json-request-and-response-example"></a>przykład kodu JSON getTypes żądań i odpowiedzi

Podane żądanie POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/types/$batch?api-version=API_VERSION
```

| Name (Nazwa) | Opis | Przykład |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Nazwa środowiska  | `environment123` |
| API_VERSION  |  Specyfikacja interfejsu API | `2018-11-01-preview` |

Z następującymi JSON treści i zmienna atrybutami:

| Atrybut | Wymagane lub opcjonalne |
| --- | --- |
| **rodzaj**  |  Wymagane  |
| **Filtr**  |  Optional (Opcjonalność) |
| **value**  | Wartość null lub nie został określony  |
| **interpolacji**  |  Optional (Opcjonalność) |
| **Agregacji**  |  Wymagane |

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleType",
            "description": "This is type 2",
            "variables": {
                "Avg Temperature": {
                    "kind": "numeric",
                    "filter": null,
                    "value": { "tsx": "$event.temperature.Double" },
                    "interpolation": "None",
                    "aggregation": {"tsx": "avg($value)"}
                },
                "Count Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "count()"}
                },
                "Min Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "min($event.temperature)"}
                },
            }
        }
    ]
}
```

Odpowiedź:

```JSON
{
    "get": null,
    "put": [
        {
            "timeSeriesType": {
                "id": "fc4f526c-da6e-4b85-87f7-16f6cf9b69be",
                "name": "type2",
                "description": "This is type 2",
                "variables": {
                    "Avg Temperature": {
                        "kind": "numeric",
                        "filter": null,
                        "value": { "tsx": "$event.temperature.Double" },
                        "interpolation": "None",
                        "aggregation": {"tsx": "avg($value)"}
                    },
                    "Count Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "count()"}
                    },
                    "Min Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "min($event.temperature)"}
                    }
                }
            },
            "error": null
        }
    ]
}
```

### <a name="gethierarchies-api"></a>getHierarchies interfejsu API

GetHierarchies interfejsu API zwraca wszystkie hierarchie szeregów czasowych i wszystkie ich ścieżek skojarzonego pola.

GetHierarchies API przyjmuje następujące parametry:

* *Identyfikator*: jednoznacznie identyfikujące klucza dla hierarchii.
* *Nazwa*: Nazwa hierarchii
* *source*
* *instanceFields*
  * *Rok*
  * *Miesiąc*

#### <a name="gethierarchies-json-request-and-response-example"></a>przykład kodu JSON getHierarchies żądań i odpowiedzi

Podane żądanie POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/hierarchies/$batch?api-version=API_VERSION
```

| Name (Nazwa) | Opis | Przykład |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Nazwa środowiska  | `environment123` |
| API_VERSION  |  Specyfikacja interfejsu API | `2018-11-01-preview` |

Za pomocą treść kodu JSON:

```JSON
{
    "get": null,
    "put": [
        {
            "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
            "name": "Location",
            "source": {
                "instanceFieldNames": [
                    "state",
                    "city"
                ]
            }
        }
    ]
}
```

Odpowiedź:

```JSON
{
    "get": null,
    "put": [
        {
            "hierarchy": {
                "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
                "name": "Location",
                "source": {
                    "instanceFieldNames": [
                        "state",
                        "city"
                    ]
                }
            },
            "error": null
        }
    ]
}
```

### <a name="getinstances-api"></a>getInstances interfejsu API

GetInstances interfejsu API jest używany do zwrócenia wszystkich wystąpień serii czasu i ich pól skojarzonego wystąpienia.

GetInstances API przyjmuje następujące parametry:

* *Nazwa*: Nazwa skojarzonego z wystąpieniem, używany do wykonywania zapytań, a także zastępując pozycje najlepsze środowisko użytkownika.
* *typeId*: jednoznacznie identyfikujące klucza dla typu.
* *timeSeriesId*: **identyfikator serii czasu** jest unikatowym kluczem dla danych w strumieniu zdarzeń i modelu. Ten klucz jest TSI używa do podzielenia danych.
* *Opis*: opis wystąpienia.
* *hierarchyIds*: tablica jednego lub większej liczby hierarchyIds jednoznacznie definiujące każda hierarchia.
* *instanceFields*:
  * *state*
  * *Miasto*

#### <a name="getinstances-json-request-and-response-example"></a>przykład kodu JSON getInstances żądań i odpowiedzi

Podane żądanie POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/instances/$batch?api-version=API_VERSION
```

| Name (Nazwa) | Opis | Przykład |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Nazwa środowiska  | `environment123` |
| API_VERSION  |  Specyfikacja interfejsu API | `2018-11-01-preview` |

Za pomocą treść kodu JSON:

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleName",
            "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
            "timeSeriesId": [
                "samplePartitionKeyValueOne"
            ],
            "description": "floor 100",
            "hierarchyIds": [
                "e37a4666-9650-42e6-a6d2-788f12d11158"
            ],
            "instanceFields": {
                "state": "California",
                "city": "Los Angeles"
            }
        }
    ]
}
```

Odpowiedź:

```JSON
{
    "get": null,
    "put": [
        {
            "instance": null,
            "error": null
        },
        {
            "instance": null,
            "error": null
        }
    ]
}
```

### <a name="aggregateseries-api"></a>aggregateSeries interfejsu API

AggregateSeries interfejs API umożliwia wysyłanie kwerend i pobieranie danych usługi TSI z przechwytywanych zdarzeń przez pobieranie próbek i agregowanie zarejestrowane przy użyciu funkcji agregujących ani przykładowe dane.

AggregateSeries API przyjmuje następujące parametry:

* *timeSeriesId*: **identyfikator serii czasu** jest unikatowym kluczem dla danych w strumieniu zdarzeń i modelu. Ten klucz jest TSI używa do podzielenia danych.
* *searchSpan*: rozmiar przedziału czasu i przedziału dla tego wyrażenia agregującego.
* *Filtr*: opcjonalne klauzulą predykatu.
* *Interwał*: interwał dane powinny zostać obliczony.
* *ProjectedVariables*: zmienne, które znajdują się w zakresie, które ma zostać obliczony.
* *InlineVariables(optional)*: definicje zmiennych, które chcemy na zastąpienie z tym przychodzących za pośrednictwem TSM lub podany wewnętrznej do obliczeń.

GetSeries interfejsu API zwraca TSV dla każdej zmiennej dla każdego przedziału na podstawie udostępnionych **identyfikator serii czasu** i zestawem używanych zmiennych podana. GetSeries API osiąga redukcji dzięki wykorzystaniu zmiennych przechowywanych w TSM lub podano wbudowaną, aby agregacji lub przykładowe dane.

> [!NOTE]
> Interpolacja zmiennej nie jest obecnie obsługiwane.

Obsługiwane typy agregacji:

* `Min`
* `Max`
* `Sum`
* `Count`
* `Average`

#### <a name="aggregateseries-json-request-and-response-example"></a>przykład kodu JSON aggregateSeries żądań i odpowiedzi

Podane żądanie POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Name (Nazwa) | Opis | Przykład |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Nazwa środowiska  | `environment123` |
| API_VERSION  |  Specyfikacja interfejsu API | `2018-11-01-preview` |

Za pomocą treść kodu JSON:

```JSON
{
    "aggregateSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        //Optional
        //If provided, Reduction should be specified in Variable Definition
        "interval": "PT1M",
        "filter": null, //Optional
        "projectedVariables": [
            "Count",
            "Average Temperature",
            "Min Temperature"
        ],
        "inlineVariables": {
            "Average Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None",
                "aggregation": {"tsx": "avg($value)"}
            },
            //Default Type Count Experience
            "Count": {
                "kind": "aggregate",
                "filter": null,
                "value": null,
                "interpolation": "None",
                "aggregation": {"tsx": "count()"}
            },
            "Min Temperature": {
                "kind": "aggregate",
                "filter": null,
                "value": null,
                "interpolation": "None",
                "aggregation": {"tsx": "min($event.temperature)"}
            },
        }
    }
}
```

Odpowiedź:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Average Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316]
        },
        {
            "name": "Count Temperature",
            "type": "Double",
            "values": [ 60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0 ]
        },
        {
            "name": "Min Temperature",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getseries-api"></a>getSeries interfejsu API

GetSeries interfejs API umożliwia wysyłanie kwerend i pobieranie danych usługi TSI z przechwytywanych zdarzeń przy użyciu danych zarejestrowanych na potrzeby przesyłu przy użyciu zmiennych zdefiniować w modelu lub podać wbudowane.

> [!Note]
> Klauzula interpolacji i agregację znajduje się w zmiennej lub interwału, zostaną zignorowane.

GetSeries API przyjmuje następujące parametry:

* *timeSeriesId*: **identyfikator serii czasu** jest unikatowym kluczem dla danych w strumieniu zdarzeń i modelu. Ten klucz jest TSI używa do podzielenia danych.
* *searchSpan*: rozmiar przedziału czasu i przedziału dla tego wyrażenia agregującego.
* *Filtr*: opcjonalne klauzulą predykatu.
* *ProjectedVariables*: zmienne, które znajdują się w zakresie, które ma zostać obliczony.
* *InlineVariables(optional)*: definicje zmiennych, które chcemy na zastąpienie z tym przychodzących za pośrednictwem TSM lub podany wewnętrznej do obliczeń.

GetSeries interfejsu API zwraca TSV dla każdej zmiennej, na podstawie udostępnionych **identyfikator serii czasu** i zestawem używanych zmiennych podana. GetSeries, który zapewnia interfejs API nie obsługuje odstępach czasu lub zmiennej redukcji/interpolacji.  

#### <a name="getseries-json-request-and-response-example"></a>przykład kodu JSON getSeries żądań i odpowiedzi

Podane żądanie POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Name (Nazwa) | Opis | Przykład |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Nazwa środowiska  | `environment123` |
| API_VERSION  |  Specyfikacja interfejsu API | `2018-11-01-preview` |

Za pomocą treść kodu JSON:

```JSON
{
    "getSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        "interval": null, //Null or not specified
        "filter": null, //Optional
        "projectedVariables": [
            "Temperature",
            "Pressure",
        ],
        "inlineVariables": {
            "Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            },
            "Pressure": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.pressure.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            }
        }
    }
}
```

Odpowiedź:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        },
        {
            "name": "Pressure",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getevents-api"></a>getEvents interfejsu API

GetEvents interfejsu API umożliwia wysyłanie kwerend i pobieranie danych usługa TSI Przesyła zdarzeń są rejestrowane w usłudze Time Series Insights od dostawcy źródła.

GetEvents API przyjmuje następujące parametry:

* *timeSeriesId*: **identyfikator serii czasu** jest unikatowym kluczem dla danych w strumieniu zdarzeń i modelu. Ten klucz jest TSI używa do podzielenia danych.
* *searchSpan*: rozmiar przedziału czasu i przedziału dla tego wyrażenia agregującego.
* *Filtr*: umożliwia określenie wartości predykatu do filtrowania zapytań, jednak wygodną pracę.
* *(opcjonalnie) projectedProperties*: umożliwia filtrowanie kolumny/właściwości.

GetEvents interfejs API zwraca nieprzetworzone wartości z przechwytywanych zdarzeń, przechowywane w usłudze TSI dla danego **identyfikator serii czasu** i wybranego zakresu czasu. Definicje zmiennych (TSM ani definicje zmiennych nie są używane) nie jest wymagane.

### <a name="getevents-json-request-and-response-example"></a>przykład kodu JSON getEvents żądań i odpowiedzi

Podane żądanie POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Name (Nazwa) | Opis | Przykład |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Nazwa środowiska  | `environment123` |
| API_VERSION  |  Specyfikacja interfejsu API | `2018-11-01-preview` |

Za pomocą treść kodu JSON:

```JSON
{
    "getEvents": {
        // Required and Supports 3 Key as tsId
        "timeSeriesId": [
            "PU.123","W00158","ABN.9890"
        ],
        // Required.
        "searchSpan": {
            "from": "2018-08-01T17:01:00Z",
            "to": "2018-08-20T17:01:00Z",
        },
        // Optional.
        "filter": {
            "tsx": "($event.Value != null) OR ($event.Status.String == 'Good')"
        },
        // Optional – array of simple TSXs that refer exactly one property
        // If not specified, we will return all the properties.
        "projectedProperties": [ 
            {"propertyName" : "Volts", "type": "double" } 
        ]
    }
}
```

Odpowiedź:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:03Z","2016-08-01T00:02:05Z","2016-08-01T00:03:00Z",
        "2016-08-01T00:04:00Z","2016-08-01T00:05:05Z","2016-08-01T00:06:00Z","2016-08-01T00:07:08Z",
        "2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Volts",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        }
    ]
}
```

### <a name="supported-query-operators"></a>Obsługiwane zapytania, operatory

Filtrowanie:

* `HAS`
* `IN`
* `AND`
* `AND NOT`
* `OR`
* `>`
* `<`
* `<=`
* `>=`
* `!=`
* `<>`
* `/`
* `*`

Danych czasowych:

* `From`
* `To`
* `First/Last` (Tylko interfejs API)

Przekształcenie/agregacji:

* `MEASURE`
* `SUM` (miary)
* `COUNT` (zdarzenia)
* `AVG` (miary)
* `MIN` (miary)
* `MAX` (miary) "
* `GROUP BY` (kolumna kategorii)
* `ORDER BY ASC, DSC` (względem sygnatura czasowa)
* `DateHistogram` (rozmiar przedziału)

## <a name="tsq-api-limits"></a>Limity TSQ interfejsu API

> [!NOTE]
> Poniższa tabela określa limity jako **11/20/18**.

| Parametr | Limity |
| --- | --- |
| Rozmiar żądania TSQ | 32 KB. |
| Limit odpowiedzi TSQ | 16 MB |
| Limit zapytanie równoległe TSQ | 20 na środowisko |
| Pobierz zdarzenia | Rozmiar 16 MB lub 30 sekund czasu |
| Ważność tokenu | 1 godzina |
| Rozpoczynanie serii | 500 000 odstępach czasu lub sygnatur czasowych lub 16 MB rozmiar |
| Min. | Interwał limitu 1 ms |
| Maks. | 50 przewidywany zmiennych |
| Seria agregacji | 500 000 odstępach czasu lub sygnatur czasowych lub 16 MB rozmiar |
| Min. | Interwał limitu 1 ms |
| Maks. | 50 przewidywany zmiennych |

## <a name="time-series-query-tutorial"></a>Samouczek zapytania serii czasu

Pełnym samouczku dotyczącym TSQ zostaną udostępnione w przyszłości.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Kolejne kroki

Odczyt [Azure TSI (wersja zapoznawcza), magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

Przeczytaj o [najlepszych rozwiązań, wybierając Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/goal.png
[2]: media/v2-update-tsq/tsq.png
