---
title: Model szeregów czasowych w usłudze Azure Time Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Time Series Insights czasu modelu szeregów.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 3e6e8ae76c0ae6f688dd4a039b34c52af16b6e0f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244025"
---
# <a name="time-series-model"></a>Model szeregów czasowych

W tym artykule opisano Model szeregów czasowych część Azure czas Series Insights w wersji zapoznawczej. Omówiono w nim samym modelu, jego możliwości i jak rozpocząć tworzenie i aktualizowanie własnego modelu.

Tradycyjnie dane, które są zbierane z urządzeń IoT brakuje informacji kontekstowych, który sprawia, że trudno znaleźć i szybkie analizowanie czujników. Głównym celem dla modelu szeregów czasowych to uprościć Znajdowanie i analizowanie danych IoT. Powoduje to osiągnięcie tego celu, włączając nadzorowaną, konserwacja i Wzbogacanie danych szeregów czasowych, ułatwiających przygotowanie przygotowane zestawów danych.

Modeli szeregów czasowych odgrywają kluczową rolę w zapytaniach i nawigacji, ponieważ ich nakreślić kontekst dla urządzeń i innych urządzeń jednostek. Dane, które zawiera utrwalone w modelu szeregów czasowych obsługuje zapytania dla szeregów czasowych obliczeń, wykorzystując formuły są w nich przechowywane.

[![Omówienie modelu serii czasu](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Najważniejsze możliwości

W celu się to proste i łatwe do zarządzania contextualization serii czasu modelu szeregów czasowych włącza następujące możliwości w czasie Series Insights w wersji zapoznawczej. Ułatwia:

* Tworzenie i zarządzanie obliczeń lub formuł, przekształcać dane, wykorzystując funkcje skalarne, agregacji operacje i tak dalej.
* Definiowanie relacji nadrzędny podrzędny, Włącz nawigacji i odwołania do zapewniania kontekstu danych telemetrycznych serii czasu.
* Definiowanie właściwości, które są skojarzone z wystąpień część *wystąpienia pól* i używać ich do tworzenia hierarchii.

## <a name="entity-components"></a>Składniki jednostki

Modeli szeregów czasowych są trzy podstawowe składniki:

* <a href="#time-series-model-types">Typy modelu serii czasu</a>
* <a href="#time-series-model-hierarchies">Hierarchie Model serii czasu</a>
* <a href="#time-series-model-instances">Wystąpienia serii modelu godziny</a>

Te składniki są połączone, aby określić Model szeregów czasowych i organizowania danych usługi Azure Time Series Insights.

## <a name="time-series-model-types"></a>Typy modelu serii czasu

Czas modelu szeregów *typy* pomagającym w zdefiniowaniu zmiennych lub formuł do wykonywania obliczeń. Typy nie są skojarzone z określonym wystąpieniem usługi Time Series Insights. Typ może mieć co najmniej jednej zmiennej. Na przykład wystąpienie usługi Time Series Insights może być typu *czujnik temperatury*, która składa się ze zmiennych *średnia temperatura*, *minimalna temperatura*i *maksymalna temperatura*. Możemy utworzyć domyślny typ, gdy dane zacznie przepływać do usługi Time Series Insights. Domyślny typ, można je pobrać i aktualizowane na podstawie ustawienia modelu. Domyślne typy mają zmienną, który zlicza liczbę zdarzeń.

### <a name="time-series-model-type-json-example"></a>Przykład kodu JSON typ modelu serii czasu

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
```

Aby uzyskać więcej informacji na temat typów modelu szeregów czasowych, zobacz [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Zmienne

Czas Series Insights typy mają zmiennych, które są nazwane obliczenia przez wartości z tych zdarzeń. Definicje zmiennych w czasie Series Insights zawiera formułę i obliczanie reguły. Definicje zmiennych obejmują *rodzaj*, *wartość*, *filtru*, *redukcji*, i *granice*. Zmienne są przechowywane w definicji typu w modelu szeregów czasowych i można podać wbudowane za pośrednictwem interfejsów API zapytań, aby zastąpić przechowywaną definicję.

Następujące macierz działa jako legendę definicje zmiennych:

[![Typ definicji zmiennej tabeli](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definicja | Opis |
| --- | ---|
| Rodzaj zmiennej |  *Liczbowe* i *agregacji* rodzaju są obsługiwane. |
| Filtr zmiennej | Filtry zmiennej Określ klauzulę opcjonalny filtr, aby ograniczyć liczbę wierszy, są traktowane jako obliczeń w oparciu o kryteria. |
| Wartość zmiennej | Wartości zmiennych i powinny być używane w obliczeń. Odpowiednie pole do odwoływania się do dla danego punktu danych. |
| Agregacja zmiennej | Funkcji agregującej zmiennej umożliwia część obliczeń. Time Series Insights obsługuje agregacje regularne (to znaczy, *min*, *max*, *avg*, *suma*, i *liczba*). |

## <a name="time-series-model-hierarchies"></a>Hierarchie Model serii czasu

Hierarchie organizować wystąpienia, przez określenie nazwy właściwości i ich wzajemne relacje. Konieczne może być pojedynczą hierarchię lub wielu hierarchii. Nie muszą być częścią bieżącego danych, ale każde wystąpienie powinny być mapowane do hierarchii. Wystąpienie modelu szeregów czasowych mapować na pojedynczą hierarchię lub wielu hierarchii.

Hierarchie są definiowane przez *identyfikator hierarchii*, *nazwa*, i *źródła*. Hierarchie mają ścieżki, czyli kolejność góra dół nadrzędny podrzędny w hierarchii, które użytkownicy chcą utworzyć. Mapy właściwości nadrzędny podrzędny *wystąpienia pól*.

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

Aby uzyskać więcej informacji na temat hierarchii modelu szeregów czasowych, zobacz [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Zachowanie definicji hierarchii

Rozważmy następujący przykład, gdzie ma hierarchię H1 *tworzenia*, *floor*, i *pokoju* jako część jego definicji:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

W zależności od *wystąpienia pól*, hierarchii atrybutów i wartości są wyświetlane zgodnie z poniższą tabelą:

| Identyfikator serii czasu | Pola wystąpienia |
| --- | --- |
| ID1 | "Tworzenie" = "1000", "floor" = "10", "pomieszczenie" = "55"  |
| ID2 | "Tworzenie" = "1000", "pomieszczenie" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "Tworzenie" = "1000", "floor" = "10"  |
| ID5 | "Tworzenie", "floor" ani "pomieszczenie" jest ustawiona |

W powyższym przykładzie **ID1** i **ID4** Pokaż jako część hierarchii H1 w Eksploratorze usługi Azure Time Series Insights, a pozostałe są klasyfikowane jako *wystąpień bez elementów nadrzędnych* ponieważ nie są one zgodne z hierarchii określone dane.

## <a name="time-series-model-instances"></a>Wystąpienia serii modelu godziny

Wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków *deviceId* lub *assetId* jest unikatowym identyfikatorem zasobów w środowisku. Wystąpienia zostały skojarzone z nimi informacje opisowe o nazwie właściwości wystąpienia. Co najmniej właściwości wystąpienia obejmują informacje o hierarchii. Mogą również zawierać przydatne, opisowe danych, takich jak producenta, operator lub Data ostatniego usługi.

Wystąpienia są definiowane przez *typeId*, *timeSeriesId*, *nazwa*, *opis*, *hierarchyIds* , a *instanceFields*. Każde wystąpienie jest mapowany na tylko jeden *typu*oraz jednej lub wielu hierarchii. Wystąpienia dziedziczyć wszystkie właściwości z hierarchii wraz z dodatkowymi *instanceFields* można dodać więcej definicji właściwości wystąpienia.

*instanceFields* są właściwości wystąpienia i danych statycznych, który definiuje wystąpienie. Wartości właściwości hierarchia lub hierarchia nie mogą określać jednocześnie obsługując indeksowanie w celu wykonywania operacji wyszukiwania.

*Nazwa* właściwość jest opcjonalna i wielkość liter. Jeśli *nazwa* jest niedostępne, zostaną domyślnie nazwę serii czasu. Jeśli *nazwa* zostanie podana, identyfikator serii czasu będą nadal dostępne w źródle (siatka poniżej wykresów w Eksploratorze).

### <a name="time-series-model-instance-json-example"></a>Przykład kodu JSON wystąpienia modelu serii czasu

Przykład:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
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

Aby uzyskać więcej informacji na temat wystąpienia modelu szeregów czasowych, zobacz [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Przykład ustawień modelu serii czasu

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

Aby uzyskać więcej informacji na temat ustawień modelu szeregów czasowych, zobacz [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [magazynu Azure czas Series Insights w wersji zapoznawczej i ruch przychodzący](./time-series-insights-update-storage-ingress.md).

- Zobacz Omówienie nowej [modelu szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).