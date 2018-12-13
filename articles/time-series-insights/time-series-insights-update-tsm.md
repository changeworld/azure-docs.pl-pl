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
ms.date: 12/04/2018
ms.openlocfilehash: 5d5f94aebcd55474385e903246ce7945586456dd
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890419"
---
# <a name="time-series-model"></a>Model szeregów czasowych

Ten dokument zawiera szczegółowe informacje **modelu szeregów czasowych** (TSM) części aktualizacji usługi Azure Time Series Insights (TSI). Opisano w nim samym modelu, jego możliwości i jak rozpocząć tworzenie i aktualizowanie własnego modelu.

Tradycyjnie danych zbieranych z urządzeń IoT brakuje informacji kontekstowych, co utrudnia do znalezienia i szybkie analizowanie czujników. Głównym celem dla TSM ma na celu uproszczenie wyszukiwanie i analizowanie danych IoT, włączając nadzorowaną, konserwacja i Wzbogacanie danych szeregów czasowych, ułatwiających przygotowanie przygotowane zestawów danych. TSMs odgrywają kluczową rolę w zapytaniach i nawigacji, ponieważ ich nakreślić kontekst dla urządzeń i innych urządzeń jednostek. Dane są utrwalane TSM uprawnień czas serii zapytania obliczeń w przy użyciu formuły są w nich przechowywane.

![TSM][1]

## <a name="key-capabilities"></a>Najważniejsze możliwości

W celu się to proste i łatwe do zarządzania contextualization serii czasu TSM włącza następujące możliwości w usłudze Azure TSI (wersja zapoznawcza):

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

## <a name="time-series-model-type-json-example"></a>Przykład kodu JSON typ modelu serii czasu

Przykład:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
``````

Przeczytaj więcej na temat typów modelu szeregów czasowych z [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>Zmienne

Azure TSI typy mają zmiennych, które są nazwane obliczenia przez wartości z tych zdarzeń. Usługa TSI Przesyła definicje zmiennych zawierają formuły i obliczanie reguły. Definicje zmiennych obejmują rodzaj, wartość, filtr, redukcji i granice. Zmienne są przechowywane w definicji typu w TSM i można podać wbudowane za pośrednictwem interfejsów API zapytań, aby zastąpić przechowywaną definicję.

Tabela poniżej działa jako legendę definicje zmiennych:

![tabela][2]

### <a name="variable-kind"></a>Rodzaj zmiennej

Obsługiwane są następujące typy zmiennych:

* Liczbowy
* Agregacja

### <a name="variable-filter"></a>Filtr zmiennej

Filtry zmiennej Określ klauzulę opcjonalny filtr, aby ograniczyć liczbę wierszy, są traktowane jako obliczeń w oparciu o kryteria.

### <a name="variable-value"></a>Wartość zmiennej

Wartości zmiennych i powinny być używane w obliczeń. Jest to kolumna w zdarzeń, które firma Microsoft powinni zapoznać się z.

### <a name="variable-aggregation"></a>Agregacja zmiennej

Funkcja agregująca zmiennej umożliwia część obliczeń. TSI będzie obsługiwać regularne agregacje (to znaczy, **min**, **max**, **avg**, **suma**, i **liczba**).

## <a name="time-series-model-hierarchies"></a>Hierarchie Model serii czasu

Hierarchie organizować wystąpienia, przez określenie nazwy właściwości i ich wzajemne relacje. Konieczne może być pojedynczą hierarchię lub wielu hierarchii. Ponadto te nie muszą być bieżącym część danych, ale każde wystąpienie powinny być mapowane do hierarchii. Wystąpienie TSM mapować na pojedynczą hierarchię lub wielu hierarchii.

Hierarchie są definiowane przez **identyfikator hierarchii**, **nazwa**, i **źródła**. Hierarchie mają ścieżki, góra dół nadrzędny podrzędny kolejności hierarchii, które użytkownik chce utworzyć ścieżkę. Właściwości nadrzędne/podrzędne mapowania pól wystąpień.

### <a name="time-series-model-hierarchy-json-example"></a>Przykład kodu JSON hierarchii modelu serii czasu

Przykład:

```JSON
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
```

Dowiedz się więcej o modelu szeregów czasowych hierarchie z [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

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

Wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków *deviceId* lub *assetId* będzie unikatowy identyfikator elementu zawartości w środowisku. Wystąpienia zostały skojarzone z nimi informacje opisowe o nazwie właściwości wystąpienia. Co najmniej właściwości wystąpienia obejmują informacje o hierarchii. Mogą również zawierać przydatne, opisowe danych, takich jak producenta, operator lub Data ostatniego usługi.

Wystąpienia są definiowane przez *timeSeriesId*, *typeId*, *hierarchyId*, i *instanceFields*. Każde wystąpienie jest mapowany na tylko jeden *typu*oraz jednej lub wielu hierarchii. Wystąpienia dziedziczyć wszystkie właściwości hierarchii, podczas gdy jest to dodatkowe *instanceFields* można dodać więcej definicji właściwości wystąpienia.

*instanceFields* są właściwości wystąpienia i danych statycznych, który definiuje wystąpienie. Wartości właściwości hierarchia lub hierarchia nie mogą określać jednocześnie obsługując indeksowanie w celu wykonywania operacji wyszukiwania.

## <a name="time-series-model-instance-json-example"></a>Przykład kodu JSON wystąpienia modelu serii czasu

Przykład:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Dowiedz się więcej o modelu szeregów czasowych wystąpień z [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-settings-example"></a>Przykład ustawień modelu serii czasu

Przykład:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Przeczytaj więcej na temat ustawień modelu szeregów czasowych z [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Kolejne kroki

Odczyt [magazynu Azure TSI (wersja zapoznawcza) i ruch przychodzący](./time-series-insights-update-storage-ingress.md).

Przeczytaj o nowym [modelu szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
