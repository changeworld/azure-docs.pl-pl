---
title: Model szeregów czasowych w wersji zapoznawczej Azure Time Series Insights | Microsoft Docs
description: Zrozumienie Azure Time Series Insights modelu szeregów czasowych.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 8f4ed6de5ff47efa441c371b80670c500f57c9bb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034063"
---
# <a name="time-series-model"></a>Model szeregów czasowych

W tym artykule opisano częścią modelu szeregów czasowych w wersji zapoznawczej Azure Time Series Insights. Omówiono w nim sam model, jego możliwości oraz sposób rozpoczynania kompilowania i aktualizowania własnych modeli.

Tradycyjnie dane zbierane z urządzeń IoT nie zawierają informacji kontekstowych, co utrudnia szybkie znajdowanie i analizowanie czujników. Główną motywacją dla modelu szeregów czasowych jest uproszczenie wyszukiwania i analizowania danych IoT. Realizuje ten cel, umożliwiając nadzorowanie, konserwację i wzbogacanie danych szeregów czasowych, aby pomóc w przygotowaniu gotowych do użycia przez klienta zestawów elementów.

Modele szeregów czasowych odgrywają istotną rolę w zapytaniach i nawigacji, ponieważ contextualizeją one urządzenia i jednostki niebędące urządzeniami. Dane, które są utrwalane w modelu szeregów czasowych, mają wpływ na obliczenia zapytań szeregów czasowych przez skorzystanie z formuł przechowywanych w nich.

[@no__t — Omówienie modelu serii 1Time](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Najważniejsze możliwości

W celu uproszczenia i łatwego zarządzania contextualization szeregów czasowych model szeregów czasowych umożliwia korzystanie z następujących funkcji w wersji zapoznawczej programu Time Series Insights. Ułatwia:

* Twórz obliczenia lub formuły i zarządzaj nimi, Przekształcaj dane przy użyciu funkcji skalarnych, operacji agregujących i tak dalej.
* Zdefiniuj relacje nadrzędny-podrzędny, aby włączyć nawigację i odwołanie oraz zapewnić kontekst do telemetrii szeregów czasowych.
* Zdefiniuj właściwości, które są skojarzone z częścią wystąpień *pól wystąpień* i używają ich do tworzenia hierarchii.

## <a name="entity-components"></a>Składniki jednostki

Modele szeregów czasowych mają trzy podstawowe składniki:

* <a href="#time-series-model-types">Typy modeli szeregów czasowych</a>
* <a href="#time-series-model-hierarchies">Hierarchie modelu szeregów czasowych</a>
* <a href="#time-series-model-instances">Wystąpienia modelu szeregów czasowych</a>

Te składniki są łączone w celu określenia modelu szeregów czasowych i organizowania danych Azure Time Series Insights.

## <a name="time-series-model-types"></a>Typy modeli szeregów czasowych

*Typy* modeli szeregów czasowych pomagają definiować zmienne lub formuły do wykonywania obliczeń. Typy są skojarzone z określonym wystąpieniem Time Series Insights. Typ może mieć co najmniej jedną zmienną. Na przykład wystąpienie Time Series Insights może być typu *czujnik temperatury*, który składa się z *średniej temperatury*, temperatury *minimalnej*i *maksymalnej temperatury*. Tworzymy domyślny typ, gdy dane zaczynają przepływać do Time Series Insights. Typ domyślny można pobrać i zaktualizować z ustawień modelu. Typy domyślne mają zmienną, która zlicza liczbę zdarzeń.

### <a name="time-series-model-type-json-example"></a>Przykład JSON typu modelu szeregów czasowych

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

Typy Time Series Insights mają zmienne, które są nazwanymi obliczeniami względem wartości ze zdarzeń. Definicje zmiennych Time Series Insights zawierają formułę i reguły obliczeń. Definicje zmiennych obejmują *rodzaj*, *wartość*, *Filtr*, *zmniejszenie*i *granice*. Zmienne są przechowywane w definicji typu w modelu szeregów czasowych i mogą być dostarczane wewnętrznie za pośrednictwem interfejsów API zapytań w celu zastąpienia przechowywanej definicji.

Poniższa macierz działa jako legenda dla definicji zmiennych:

[@no__t — tabela definicji zmiennej 1Type](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definicja | Opis |
| --- | ---|
| Rodzaj zmiennej |  Typy *liczbowe* i *zagregowane* są obsługiwane |
| Filtr zmiennych | Filtry zmiennych określają opcjonalną klauzulę filtru, aby ograniczyć liczbę wierszy branych pod uwagę w obliczeniach na podstawie warunków. |
| Wartość zmiennej | Wartości zmiennych są i powinny być używane w obliczeniach. Odpowiednie pole, do którego odwołuje się dla danego punktu danych. |
| Agregacja zmiennych | Funkcja agregująca zmiennej umożliwia części obliczeń. Time Series Insights obsługuje regularne agregaty (mianowicie, *min*, *Max*, *AVG*, *sum*i *Count*). |

## <a name="time-series-model-hierarchies"></a>Hierarchie modelu szeregów czasowych

Hierarchie organizują wystąpienia przez określenie nazw właściwości i ich relacji. Może istnieć pojedyncza hierarchia lub wiele hierarchii. Nie muszą one być bieżącą częścią danych, ale każde wystąpienie powinno być mapowane na hierarchię. Wystąpienie modelu szeregów czasowych może być mapowane na pojedynczą hierarchię lub wiele hierarchii.

Hierarchie są definiowane przez *Identyfikator hierarchii*, *nazwę*i *Źródło*. Hierarchie mają ścieżkę, która jest kolejnością nadrzędny-podrzędny hierarchii, którą użytkownicy chcą tworzyć. Właściwości nadrzędny-podrzędny mapowania *pól wystąpień*.

### <a name="time-series-model-hierarchy-json-example"></a>Przykład JSON hierarchii modelu szeregów czasowych

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

Rozważmy poniższy przykład, w którym hierarchia H1 ma *kompilację*, *piętro*i *Pokój* w ramach swojej definicji:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

W zależności od *pól wystąpienia*atrybuty hierarchii i wartości są wyświetlane jak pokazano w poniższej tabeli:

| Identyfikator szeregów czasowych | Pola wystąpienia |
| --- | --- |
| ID1 | "Building" = "1000", "piętro" = "10", "Pokój" = "55"  |
| ID2 | "Building" = "1000", "Pokój" = "55" |
| ID3 | "piętro" = "10" |
| ID4 | "Building" = "1000", "piętro" = "10"  |
| ID5 | Nie ustawiono żadnego z "kompilowania", "piętra" ani "pokoju" |

W powyższym przykładzie **ID1** i **ID4** są wyświetlane jako część hierarchii H1 w Eksploratorze Azure Time Series Insights, a pozostałe są klasyfikowane jako *nienadrzędne wystąpienia* , ponieważ nie są zgodne z określoną hierarchią danych.

## <a name="time-series-model-instances"></a>Wystąpienia modelu szeregów czasowych

Wystąpienia są same w szeregach czasowych. W większości przypadków parametr *deviceId* lub *AssetID* jest unikatowym identyfikatorem elementu zawartości w środowisku. Wystąpienia mają opisowe informacje skojarzone z nimi nazywane właściwościami wystąpienia. Co najmniej właściwości wystąpienia zawiera informacje o hierarchii. Mogą również zawierać przydatne, opisowe dane, takie jak producent, operator lub Data ostatniej usługi.

Wystąpienia są definiowane przez *typeId*, *timeSeriesId*, *name*, *Description*, *hierarchyIds*i *instanceFields*. Każde wystąpienie jest mapowane na tylko jeden *Typ*i co najmniej jedną hierarchię. Wystąpienia dziedziczą wszystkie właściwości z hierarchii i dodatkowe *instanceFields* można dodać do dalszej definicji właściwości wystąpienia.

*instanceFields* to właściwości wystąpienia i wszystkie dane statyczne, które definiują wystąpienie. Definiują wartości hierarchii lub właściwości nie będących hierarchią, a także obsługują indeksowanie w celu wykonywania operacji wyszukiwania.

Właściwość *name* jest opcjonalna i uwzględnia wielkość liter. Jeśli *Nazwa* jest niedostępna, domyślnie będzie to identyfikator szeregów czasowych. W przypadku podanej *nazwy* identyfikator szeregów czasowych nadal będzie dostępny w dobrze (siatka poniżej wykresów w Eksploratorze).

### <a name="time-series-model-instance-json-example"></a>Przykład JSON wystąpienia modelu szeregów czasowych

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

Aby uzyskać więcej informacji na temat wystąpień modelu szeregów czasowych, zobacz [dokumentację referencyjną](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Przykład ustawień modelu szeregów czasowych

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

## <a name="next-steps"></a>Następne kroki

- Zobacz [Azure Time Series Insights w wersji zapoznawczej magazynu i transferu danych](./time-series-insights-update-storage-ingress.md)przychodzących.

- Zobacz nowy [model szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).