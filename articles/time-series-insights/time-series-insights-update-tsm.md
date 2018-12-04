---
title: Czas modelu szeregów | Dokumentacja firmy Microsoft
description: Opis czasu modelu szeregów
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 4cb83b61068922002c0c308f4d129a2e069beadd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856446"
---
# <a name="time-series-model"></a>Model szeregów czasowych

Ten dokument zawiera szczegółowe informacje **modelu szeregów czasowych** (TSM) części aktualizacji usługi Azure Time Series Insights (TSI). Opisano w nim samym modelu, jego możliwości i jak rozpocząć tworzenie i aktualizowanie własnego modelu.

Tradycyjnie danych zbieranych z urządzeń IoT brakuje informacji kontekstowych, co utrudnia do znalezienia i szybkie analizowanie czujników. Głównym celem dla TSM ma na celu uproszczenie wyszukiwanie i analizowanie danych IoT, włączając nadzorowaną, konserwacja i Wzbogacanie danych szeregów czasowych, ułatwiających przygotowanie przygotowane zestawów danych. **Czas serii modeli** odgrywają kluczową rolę w zapytaniach i nawigacji, ponieważ ich nakreślić kontekst dla jednostek urządzeń i innych urządzeń. Dane są utrwalane TSM uprawnień czas serii zapytania obliczeń w przy użyciu formuły są w nich przechowywane.

![TSM][1]

## <a name="key-capabilities"></a>Najważniejsze możliwości

W celu się to proste i łatwe do zarządzania contextualization serii czasu TSM włącza następujące możliwości w usłudze Azure Time Series Insights (wersja zapoznawcza):

* Możliwość tworzenia i zarządzania nimi, obliczenia lub formuły w celu przekształcania danych, wykorzystując funkcje skalarne operacje agregacji, itp.
* Definiowanie relacji podrzędnej nadrzędnego umożliwienie nawigacji i odwołania do zapewniania kontekstu danych telemetrycznych serii czasu.
* Zdefiniuj właściwości skojarzone z wystąpień część pól wystąpień i służą do tworzenia hierarchii.

## <a name="times-series-model-key-components"></a>Czas najważniejsze składniki modelu szeregów

Istnieją trzy główne składniki TSM:

* **Czas modelu szeregów** *typów*
* **Czas modelu szeregów** *hierarchii*
* **Czas modelu szeregów** *wystąpień*

## <a name="time-series-model-types"></a>Typy modelu serii czasu

**Czas modelu szeregów** *typy* definiowania zmiennych lub formuły do wykonywania obliczeń i są skojarzone z danego wystąpienia usługi TSI. Typ może mieć co najmniej jednej zmiennej. Na przykład wystąpienie usługi TSI może być typu **czujnik temperatury**, która składa się ze zmiennych: *średnia temperatura*, *minimalna temperatura*, i *maksymalny temperatura*. Możemy utworzyć domyślny typ uruchomienia danych otrzymywanych przez TSI. Można je pobrać i aktualizowane na podstawie ustawienia modelu. Domyślne typy mają zmienną, który zlicza liczbę zdarzeń.

## <a name="time-series-model-type-json-request-and-response-example"></a>Czas serii modelu typu przykład kodu JSON z żądania i odpowiedzi

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
``````

A **domyślne** *typu* odpowiedź w formacie JSON:

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

## <a name="variables"></a>Zmienne

Azure TSI typy mają zmiennych, są one nazwane obliczenia przez wartości z wydarzeń. Usługa TSI Przesyła definicje zmiennych zawierają formuły i obliczanie reguły. Definicje zmiennych obejmują rodzaj, wartość, filtr, redukcji i granice. Zmienne są przechowywane w definicji typu w TSM i można podać wbudowane za pośrednictwem interfejsów API zapytań, aby zastąpić przechowywaną definicję.

Tabela poniżej działa jako legendę definicje zmiennych:

![tabela][2]

### <a name="variable-kind"></a>Rodzaj zmiennej

Obsługiwane są następujące typy zmiennych:

* NUMERIC — ciągła

### <a name="variable-filter"></a>Filtr zmiennej

Filtry zmiennej Określ klauzulę opcjonalny filtr, aby ograniczyć liczbę wierszy, są traktowane jako obliczeń w oparciu o kryteria.

### <a name="variable-value"></a>Wartość zmiennej

Wartości zmiennych i powinny być używane w obliczeń. Jest to kolumna w zdarzeń, które firma Microsoft powinni zapoznać się z.

### <a name="variable-interpolation"></a>Interpolacja zmiennej

Proces konwersji zestawu wartości z wartością dla interwału nosi nazwę zmiennej redukcji. Zmienne redukcji może być zagregowane dane zarejestrowane ze źródła lub odtworzone sygnały przy użyciu interpolacji i sygnałów agregacji lub zrekonstruowaną przy użyciu interpolacji i pobierania próbek. Granice zmiennych mogą być dodawane do interpolacji, umożliwiają one obliczeń uwzględnić zdarzenia spoza zakresu wyszukiwania.

Azure TSI (wersja zapoznawcza) obsługuje następujące interpolacji zmiennej: `linear`, `stepright`, i `none`.

### <a name="variable-aggregation"></a>Agregacja zmiennej

Funkcja agregująca zmiennej umożliwia część obliczeń. Przypadku interpolacji zmiennej `null` lub `none`, a następnie TSI będzie obsługiwać regularne agregacje (to znaczy, **min**, **max**, **średni**, **sum** , i **liczba**). W przypadku interpolacji zmiennej `stepright` lub `linear`, a następnie TSI będzie obsługiwać **twmin**, **twmax**, **twavg**, i **twsum**. Nie można określić liczbę w interpolacji.

## <a name="time-series-model-hierarchies"></a>Hierarchie Model serii czasu

Hierarchie organizować wystąpienia, przez określenie nazwy właściwości i ich wzajemne relacje. Konieczne może być pojedynczą hierarchię lub wielu hierarchii. Ponadto te nie muszą być bieżącym część danych, ale każde wystąpienie powinny być mapowane do hierarchii. Wystąpienie TSM mapować na pojedynczą hierarchię lub wielu hierarchii.

Hierarchie są definiowane przez **identyfikator hierarchii**, **nazwa**, i **źródła**. Hierarchie mają ścieżki, góra dół nadrzędny podrzędny kolejności hierarchii, które użytkownik chce utworzyć ścieżkę. Właściwości nadrzędne/podrzędne mapowania pól wystąpień.

### <a name="time-series-model-hierarchy-json-request-and-response-example"></a>Czas modelu szeregów hierarchii przykład kodu JSON z żądania i odpowiedzi

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

### <a name="hierarchy-definition-behavior"></a>Zachowanie definicji hierarchii

Rozważmy poniższy, gdzie H1 zawiera "Tworzenie" i "floor" oraz "pomieszczenie" jako część jego definicji:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

W zależności od pól wystąpień, hierarchii, atrybuty i wartości, pojawi się będą wyświetlane: 

| Identyfikator serii czasu | Pola wystąpienia |
| --- | --- |
| ID1 | "Tworzenie" = "1000", "floor" = "10", "pomieszczenie" = "55"  |
| IDENTYFIKATOR 2 | "Tworzenie" = "1000", "pomieszczenie" = "55" |
| ID3 |  "floor" = "10" |
| ID4 | "Tworzenie" = "1000", "floor" = "10"  |
| ID5 | |

W powyżej przykładzie ID1 jest wyświetlany jako część hierarchii H1 w interfejsie użytkownika/środowiska użytkownika, a pozostałe są klasyfikowane jako `Unparented Instances` , ponieważ nie są zgodne do hierarchii określone dane.

## <a name="time-series-model-instances"></a>Wystąpienia serii modelu godziny

Wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków będzie to *deviceId* lub *assetId* oznacza to unikatowy identyfikator elementu zawartości w środowisku. Wystąpienia zostały skojarzone z nimi informacje opisowe o nazwie właściwości wystąpienia. Co najmniej właściwości wystąpienia obejmują informacje o hierarchii. Mogą również zawierać przydatne, opisowe danych, takich jak producenta, operator lub Data ostatniego usługi.

Wystąpienia są definiowane przez *timeSeriesId*, *typeId*, *hierarchyId*, i *instanceFields*. Każde wystąpienie jest mapowany na tylko jeden *typu*oraz jednej lub wielu hierarchii. Wystąpienia dziedziczyć wszystkie właściwości hierarchii, podczas gdy jest to dodatkowe *instanceFields* można dodać więcej definicji właściwości wystąpienia.

*instanceFields* są właściwości wystąpienia i danych statycznych, który definiuje wystąpienie. Wartości właściwości hierarchia lub hierarchia nie mogą określać jednocześnie obsługując indeksowanie w celu wykonywania operacji wyszukiwania.

## <a name="time-series-model-instance-json-request-and-response-example"></a>Czas modelu szeregów wystąpienia przykład kodu JSON z żądania i odpowiedzi

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

## <a name="time-series-model-settings-example"></a>Przykład ustawień modelu serii czasu

Podane żądanie POST HTTP:

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

## <a name="time-series-model-limits"></a>Ograniczeń modelu szeregów czasowych

| Parametr |   Limity |
| --- | --- |
| Rozmiar obiektu modelu jednostki (typy, hierarchie i wystąpień)|  32 KB zawiera właściwości |
| Klucze można użyć jako identyfikatora TSID właściwości skonfigurowane za pomocą witryny Azure Portal |   Maksymalna liczba 3 |
| Maksymalna liczba typów w środowisku |    1000|
| Maksymalna liczba zmiennych w typie |    50|
| Maksymalna liczba hierarchie w środowisku|   32|
| Maksymalna głębokość hierarchii | 32|
| Maksymalna liczba hierarchii skojarzonych z 1 wystąpieniem   |21|
| Maksymalna liczba wystąpień w środowisku |    500,000|
| Maksymalna liczba pól wystąpień dla każdego wystąpienia |   50|
| Operacja upsert/aktualizowanie/usuwanie modelu obiektów na sekundę   |100 na sekundę|
| Szeregi czasowe modelu rozmiar żądania interfejsu API: usługi Batch |  8 MB lub 1000 obiekty modelu (zależnie co nastąpi wcześniej)|
| Rozmiar żądania Model serii czasu: wyszukiwania/Sugeruj   | 32 KB.|
| Szeregi czasowe modelu rozmiar żądania interfejsu API: usługi Batch    | 32 MB|
| Wyszukiwanie/sugerowanej wynosi 32 MB|  32 MB|

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Kolejne kroki

Odczyt [Azure TSI (wersja zapoznawcza), magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
