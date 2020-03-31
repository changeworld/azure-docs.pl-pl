---
title: Model szeregów czasowych — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o modelu szeregów czasowych w usłudze Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476658"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Model szeregów czasowych w usłudze Azure Time Series Insights Preview

W tym artykule opisano model szeregów czasowych, możliwości i jak rozpocząć tworzenie i aktualizowanie własnych modeli w środowisku usługi Azure Time Series Insights Preview.

> [!TIP]
>  * Przejdź do środowiska [demonstracyjnego farmy wiatrowej Contoso,](https://insights.timeseries.azure.com/preview/samples) aby uzyskać przykład modelu szeregów czasowych na żywo.
> * Przeczytaj o [Eksploratorze usługi Azure Time Series Insights Preview,](time-series-insights-update-explorer.md) aby dowiedzieć się, jak poruszać się po interfejsie użytkownika modelu szeregów czasowych.
> * Dowiedz [się, jak pracować z modelem szeregów czasowych](time-series-insights-update-how-to-tsm.md) przy użyciu eksploratora internetowego Time Series Insights.

## <a name="summary"></a>Podsumowanie

Tradycyjnie dane zbierane z urządzeń IoT nie są zawierane z informacjami kontekstowymi, co utrudnia szybkie znajdowanie i analizowanie czujników. Główną motywacją dla modelu szeregów czasowych jest uproszczenie znajdowania i analizowania danych IoT lub szeregów czasowych. Osiąga ten cel, umożliwiając curation, konserwacji i wzbogacania danych szeregów czasowych, aby pomóc w przygotowaniu zestawów danych gotowych dla konsumenta do analizy.

## <a name="scenario-contosos-new-smart-oven"></a>Scenariusz: Nowy inteligentny piekarnik firmy Contoso

**Rozważmy fikcyjny scenariusz inteligentnego piekarnika Contoso.** W tym scenariuszu załóżmy, że każdy inteligentny piekarnik Contoso ma pięć czujników temperatury, po jednym dla każdego z czterech palników górnych i jeden dla samego piekarnika. Do niedawna każdy czujnik temperatury contoso wysyłał, zapisywał i wizualizował swoje dane indywidualnie. Do monitorowania urządzeń kuchennych firma Contoso opierała się na podstawowych wykresach, po jednym dla każdego czujnika.

Chociaż contoso był zadowolony z początkowych danych i rozwiązania do wizualizacji, stało się oczywiste kilka ograniczeń:

* Klienci chcieli wiedzieć, jak gorąco cały piec dostanie, gdy większość najlepszych palników były na. Contoso miał większe trudności z analizą i przedstawieniem jednolitej odpowiedzi na temat warunków panujących w całym piecu.
* Inżynierowie Contoso chcieli sprawdzić, czy górne palniki są uruchamiane jednocześnie, nie spowoduje nieefektywnego pociągania mocy. Nie było trudności z odsyłaniem, które czujniki temperatury i napięcia były ze sobą powiązane i jak je zlokalizować w sklepie.
* Zespół zapewnienia jakości firmy Contoso chciał przeprowadzić audyt i porównać historię między dwiema wersjami czujników. Trudno było określić, jakie dane należały do której wersji czujnika.

Bez możliwości struktury, organizowania i definiowania nadrzędnego modelu inteligentnych szeregów czasowych pieca, każdy czujnik temperatury utrzymywał zwichnięte, odizolowane i mniej pouczające punkty danych. Przekształcenie tych punktów danych w użyteczne spostrzeżenia było trudniejsze, ponieważ każdy zestaw danych żył niezależnie od innych.

Ograniczenia te ujawniły znaczenie inteligentnych narzędzi do agregacji danych i wizualizacji, które towarzyszą nowemu piecowi firmy Contoso:

* Wizualizacja danych okazuje się przydatna, gdy możesz skojarzyć i połączyć dane w wygodny widok. Przykładem jest pokazanie czujników napięcia wraz z czujnikami temperatury.
* Zarządzanie wielowymiarowymi danymi dla kilku jednostek wraz z funkcjami porównania, powiększania i zakresu czasu może być trudne do wykonania.

**Model szeregów czasowych zapewnia wygodne rozwiązanie** dla wielu scenariuszy napotkanych w tym fikcyjnym przykładzie:

[![Model szeregów czasowych przykład inteligentnego wykresu piekarnika](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Model szeregów czasowych odgrywa istotną rolę w kwerendach i nawigacji, ponieważ contextualizes danych, umożliwiając porównania, które mają być rysowane w zakresie czasu oraz między czujnikiem i rodzaju urządzenia. (**A**) 
* Dane są dodatkowo kontekstowe, ponieważ dane utrwalone w modelu szeregów czasowych zachowuje obliczenia zapytań szeregów czasowych jako zmienne i ponownie używa ich w czasie kwerendy.
* Model szeregów czasowych organizuje i agreguje dane w celu zwiększenia możliwości wizualizacji i zarządzania. (**B)** 

### <a name="key-capabilities"></a>Najważniejsze możliwości

Aby ułatwić i bezproblemowo zarządzać kontekstyzacją szeregów czasowych, model szeregów czasowych umożliwia następujące możliwości w usłudze Time Series Insights Preview. Pomaga:

* Tworzenie obliczeń lub formuł i zarządzanie nimi z wykorzystaniem funkcji skalarnych, operacji agregujących itd.
* Definiowanie relacji nadrzędny-podrzędny w celu umożliwienia nawigacji, wyszukiwania i odwoływania się.
* Zdefiniuj właściwości skojarzone z wystąpieniami, zdefiniowane jako *pola wystąpień,* i użyj ich do tworzenia hierarchii.

### <a name="components"></a>Składniki

Model szeregów czasowych ma trzy podstawowe składniki:

* [Wystąpienia modelu szeregów czasowych](#time-series-model-instances)
* [Hierarchie modeli szeregów czasowych](#time-series-model-hierarchies)
* [Typy modeli szeregów czasowych](#time-series-model-types)

Te składniki są łączone w celu określenia modelu szeregów czasowych i zorganizowania danych usługi Azure Time Series Insights.

[![Wykres przeglądu modelu szeregów czasowych](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Model szeregów czasowych można tworzyć i zarządzać za pośrednictwem interfejsu [usługi Time Series Insights Preview.](time-series-insights-update-how-to-tsm.md) Ustawieniami modelu szeregów czasowych można zarządzać za pomocą [interfejsu API ustawień modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Wystąpienia modelu szeregów czasowych

Wystąpienia modelu *szeregów czasowych* są wirtualnymi reprezentacjami samych szeregów czasowych.

W większości przypadków wystąpienia są jednoznacznie identyfikowane przez **deviceId** lub **assetId**, które są zapisywane jako identyfikatory szeregów czasowych.

Wystąpienia mają skojarzone z nimi informacje opisowe zwane właściwościami wystąpienia, takie jak identyfikator *szeregów czasowych,* typ, nazwa, opis, hierarchie i pola instancji. Co najmniej właściwości wystąpienia obejmują informacje o hierarchii.

*Pola wystąpienia* to zbiór informacji opisowych, które mogą zawierać wartości poziomów hierarchii, a także producenta, operatora itd.

Po skonfigurowaniu źródła zdarzeń dla środowiska usługi Time Series Insights wystąpienia są automatycznie odnajdywane i tworzone w modelu szeregów czasowych. Wystąpienia można tworzyć lub aktualizować za pośrednictwem Eksploratora usługi Time Series Insights przy użyciu zapytań modelu szeregów czasowych.

[Prezentacja farmy wiatrowej Contoso](https://insights.timeseries.azure.com/preview/samples) zawiera kilka przykładów wystąpienia na żywo.

[![Przykład wystąpienia modelu szeregów czasowych](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Właściwości wystąpienia

Wystąpienia są definiowane przez **timeSeriesId**, **typeId**, **name**, **description**, **hierarchyIds**i **instanceFields**. Każde wystąpienie jest mapowe tylko na jeden *typ*i jedną lub więcej *hierarchii.*

| Właściwość | Opis |
| --- | ---|
| timeSeriesId (100) | UUID szeregów czasowych, z które jest skojarzone wystąpienie. |
| Typeid | UUID typu modelu szeregów czasowych, z jakim jest skojarzone wystąpienie. Domyślnie wszystkie wykryte nowe wystąpienia są skojarzone z typem domyślnym.
| name | Właściwość **name** jest opcjonalna i rozróżniana wielkość liter. Jeśli **nazwa** nie jest dostępna, domyślnie jest to **timeSeriesId**. Jeśli podana zostanie nazwa, **timeSeriesId** jest nadal dostępny w [studni](time-series-insights-update-explorer.md#4-time-series-well). |
| description | Opis tekstowy wystąpienia. |
| hierarchyIds | Określa hierarchie, do których należy wystąpienie. |
| pola instancji | Właściwości wystąpienia i wszelkie dane statyczne, które definiuje wystąpienie. Definiują wartości właściwości hierarchii lub niearchistyki, a jednocześnie obsługują indeksowanie w celu wykonywania operacji wyszukiwania. |

> [!NOTE]
> Hierarchie są tworzone przy użyciu pól wystąpień. Dodatkowe **instanceFields** można dodać dla dalszych definicji właściwości wystąpienia.

Wystąpienia mają następującą reprezentację JSON:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Aby uzyskać interfejs API wystąpienia usługi Time Series Insights i utworzyć, przeczytać, zaktualizować i usunąć (CRUD) — przeczytaj artykuł [o kwerendzie danych](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) i [dokumentację REST interfejsu API wystąpienia.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)

## <a name="time-series-model-hierarchies"></a>Hierarchie modeli szeregów czasowych

Hierarchie modeli szeregów *czasowych* organizują wystąpienia, określając nazwy właściwości i ich relacje.

Można skonfigurować wiele hierarchii w danym środowisku usługi Time Series Insights. Wystąpienie modelu szeregów czasowych może mapować do jednej hierarchii lub wielu hierarchii (relacja wiele do wielu).

Interfejs klienta [demonstracyjny farmy wiatrowej Contoso](https://insights.timeseries.azure.com/preview/samples) wyświetla standardową hierarchię wystąpień i typów.

[![Przykład hierarchii modelu szeregów czasowych](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definicja hierarchii

Hierarchie są definiowane **id**przez identyfikator hierarchii, **nazwę**i **źródło**.

| Właściwość | Opis |
| ---| ---|
| id | Unikatowy identyfikator hierarchii, który jest używany, na przykład podczas definiowania wystąpienia. |
| name | Ciąg używany do dostarczania nazwy hierarchii. |
| source | Określa hierarchię lub ścieżkę organizacyjną, która jest odgórną kolejnością nadrzędna-podrzędna hierarchii, którą użytkownicy chcą utworzyć. Właściwości nadrzędny podrzędny mapują pola wystąpienia. |

Hierarchie są reprezentowane w JSON jako:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

W poprzednim przykładzie JSON:

* `Location`definiuje hierarchię z `states` elementem `cities`nadrzędnym i podrzędnym . Każdy `location` może `states`mieć wiele , które `cities`z kolei mogą mieć wiele .
* `ManufactureDate`definiuje hierarchię z `year` elementem `month`nadrzędnym i podrzędnym . Każdy `ManufactureDate` może `years`mieć wiele , które `months`z kolei mogą mieć wiele .

> [!TIP]
> Aby uzyskać interfejs API wystąpienia usługi Time Series Insights i obsługę wystąpienia CRUD, przeczytaj artykuł [o kwerendzie danych](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) i [dokumentację REST interfejsu API hierarchii](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Przykład hierarchii

Rozważmy przykład, gdzie hierarchia `floor` **H1** ma `building`, i `room` jako część jego **instanceFieldNames** definicji:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Biorąc pod uwagę pola wystąpienia używane w poprzedniej definicji i kilka szeregów czasowych, atrybuty i wartości hierarchii są wyświetlane w poniższej tabeli:

| Identyfikator szeregów czasowych | Pola wystąpienia |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| Id2 | "building" = "1000", "pokój" = "55" |
| ID3 | "podłoga" = "10" |
| Id4 | "building" = "1000", "podłoga" = "10"  |
| ID5 | Nie ustawiono "budynku", "podłogi" ani "pokoju". |

Szeregi czasowe **ID1** i **ID4** są wyświetlane jako część hierarchii **H1** w [Eksploratorze usługi Azure Time Series Insights,](time-series-insights-update-explorer.md) ponieważ mają w pełni zdefiniowane i poprawnie uporządkowane parametry *budynku,* *podłogi*i *pomieszczenia.*

Pozostałe są klasyfikowane w obszarze *Wystąpienia nieparentowane,* ponieważ nie są one zgodne z określoną hierarchią danych.

## <a name="time-series-model-types"></a>Typy modeli szeregów czasowych

Typy modeli *szeregów* czasowych ułatwią definiowanie zmiennych lub formuł do wykonywania obliczeń. Typy są skojarzone z określonym wystąpieniem usługi Time Series Insights.

Typ może mieć jedną lub więcej zmiennych. Na przykład wystąpienie modelu szeregów czasowych może być typu *Czujnik temperatury,* który składa się ze zmiennych *średnia temperatura,* *temperatura min*i *maksymalna temperatura*.

[Wind Farma contoso demo](https://insights.timeseries.azure.com/preview/samples) wizualizuje kilka typów modeli szeregów czasowych skojarzonych z ich odpowiednich wystąpień.

[![Przykład typu modelu szeregów czasowych](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Aby uzyskać interfejs API wystąpienia usługi Time Series Insights i obsługę wystąpienia CRUD, przeczytaj artykuł [o kwerendzie danych](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) i [dokumentację Typ interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Właściwości typu

Typy modeli szeregów czasowych są definiowane przez **identyfikator,** **nazwę,** **opis**i **zmienne**.

| Właściwość | Opis |
| ---| ---|
| id | UUID dla typu. |
| name | Ciąg używany do podania nazwy dla typu. |
| description | Opis ciągu dla typu. |
| Zmiennych | Określ zmienne skojarzone z typem. |

Typy są zgodne z następującym przykładem JSON:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Zmienne

Typy usługi Time Series Insights mogą mieć wiele zmiennych określających reguły formuły i obliczeń dotyczące zdarzeń.

Każda zmienna może być jednym z trzech *rodzajów:* *numeryczny,* *kategoryczny*i *zagregowany.*

* **Rodzaje liczbowe** działają z wartościami ciągłymi. 
* **Rodzaje kategoryczne** działają ze zdefiniowanym zestawem wartości dyskretnych.
* **Wartości zagregowane** łączą wiele zmiennych jednego rodzaju (wszystkie numeryczne lub wszystkie kategoryczne).

W poniższej tabeli przedstawiono właściwości, które są istotne dla każdego rodzaju zmiennej.

[![Tabela zmiennych modelu szeregów czasowych](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Zmienne numeryczne

| Właściwość zmienna | Opis |
| --- | ---|
| Filtr zmiennej | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy rozważanych do obliczeń. |
| Wartość zmienna | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników lub przekształcone przy użyciu wyrażeń szeregów czasowych. Numeryczne zmienne rodzaju muszą być typu *Double*.|
| Interpolacja zmiennych | Interpolacja określa sposób rekonstrukcji sygnału przy użyciu istniejących danych. *Opcje* interpolacji krokowej i *liniowej* są dostępne dla zmiennych liczbowych. |
| Agregacja zmiennych | Obsługa obliczeń za pomocą operatorów *Średnia*, *Min*, *Max*, *Suma*, *Count*, *Pierwszy*, *Ostatni* i ważona czasowo *(Średnia*, *Min*, *Max*, *Suma*, *Lewa*). |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Zmienne kategoryczne

| Właściwość zmienna | Opis |
| --- | ---|
| Filtr zmiennej | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy rozważanych do obliczeń. |
| Wartość zmienna | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników. Zmienne rodzaju kategorii muszą być *długie* lub *ciągi.* |
| Interpolacja zmiennych | Interpolacja określa sposób rekonstrukcji sygnału przy użyciu istniejących danych. Opcja *Interpolacja kroków* jest dostępna dla zmiennych kategorycznych. |
| Kategorie zmiennych | Kategorie tworzą mapowanie między wartościami pochodzącymi z urządzenia lub czujników do etykiety. |
| Kategoria domyślna zmienna | Domyślna kategoria jest dla wszystkich wartości, które nie są mapowane we właściwości "kategorie". |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Zmienne agregowane

| Właściwość zmienna | Opis |
| --- | ---|
| Filtr zmiennej | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy rozważanych do obliczeń. |
| Agregacja zmiennych | Obsługa obliczeń za pomocą *średnia*, *Min*, *Max*, *Suma*, *Count*, *Pierwszy*, *Ostatni*. |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Zmienne są przechowywane w definicji typu modelu szeregów czasowych i mogą być dostarczane w linii za pośrednictwem [interfejsów API kwerendy,](time-series-insights-update-tsq.md) aby zastąpić przechowywaną definicję.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [magazyn i ruch przychodzący usługi Azure Time Series Insights Preview](./time-series-insights-update-storage-ingress.md).

- Dowiedz się więcej o typowych operacjach modelu szeregów czasowych w [modelowaniu danych w usłudze Azure Time Series Insights Preview](./time-series-insights-update-how-to-tsm.md)

- Przeczytaj nową dokumentację referencyjną [modelu szeregów czasowych.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)
