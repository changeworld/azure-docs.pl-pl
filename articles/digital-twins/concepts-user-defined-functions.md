---
title: Przetwarzanie danych i funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie przetwarzania danych, dopasowujące jednostki i funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 2703778cd2eab582a9e7311aaf2024f100261889
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624526"
---
# <a name="data-processing-and-user-defined-functions"></a>Przetwarzanie danych i funkcje zdefiniowane przez użytkownika

Twins cyfrowych platformy Azure oferuje możliwości zaawansowanych obliczeń. Deweloperzy mogą zdefiniować i uruchamiać funkcje niestandardowe wiadomości przychodzących danych telemetrycznych do wysyłania zdarzeń do wstępnie zdefiniowanych punktów końcowych.

## <a name="data-processing-flow"></a>Przepływ przetwarzania danych

Po urządzenia wysyłają dane telemetryczne do reprezentacji urządzeń cyfrowych platformy Azure, deweloperzy może przetwarzać dane w fazach cztery: Weryfikowanie, zgodne, obliczeń i wysyłania.

![Usługa Azure przepływ przetwarzania danych cyfrowych bliźniaczych reprezentacji][1]

1. Faza sprawdzania poprawności przekształca przychodzące komunikaty telemetryczne w celu prezentacji typowe [obiekt transferu danych](https://en.wikipedia.org/wiki/Data_transfer_object) formatu. Ta faza wykonuje również weryfikacji urządzeń i czujników.
1. Faza dopasowanie wyszukuje odpowiednie funkcje zdefiniowane przez użytkownika (UDF), aby uruchomić. Wstępnie zdefiniowane dopasowujące jednostki Znajdź funkcje zdefiniowane przez użytkownika na podstawie urządzeń, czujników i informacji o miejscu na przychodzące komunikaty telemetryczne.
1. Faza obliczeń działa dopasowywany w poprzedniej fazy funkcje zdefiniowane przez użytkownika. Te funkcje mogą odczytywać i aktualizować obliczonych wartości na przestrzenne programu graph węzły i może emitować niestandardowe powiadomienia.
1. Faza wysyłania trasy niestandardowe powiadomienia z fazy obliczeń do punktów końcowych zdefiniowanych na wykresie.

## <a name="data-processing-objects"></a>Przetwarzanie danych obiektów

Przetwarzanie danych w reprezentacji urządzeń cyfrowych platformy Azure składa się z Definiowanie trzy obiekty: dopasowujące jednostki, funkcje zdefiniowane przez użytkownika i przypisań ról.

![Azure cyfrowego bliźniaczych reprezentacji przetwarzanie danych obiektów][2]

### <a name="matchers"></a>Dopasowujące jednostki

Dopasowujące jednostki definiują zestaw warunków określających, jakie akcje wykonane, na podstawie przychodzących danych telemetrycznych z czujnika. Aby określić dopasowanie może to być właściwości z czujnika, czujnik nadrzędnego urządzenia i czujnik nadrzędnego miejsca. Warunki są wyrażane jako porównania z typem [ścieżkę JSON](http://jsonpath.com/) zgodnie z opisem w tym przykładzie:

- Wszystkie czujniki datatype **temperatury**
- Posiadanie `01` ich portu
- Które należą do urządzenia przy użyciu klucza właściwości rozszerzonej **producenta** ustawiona na wartość `"GoodCorp"`
- Które należą do miejsca do magazynowania o typie `"Venue"`
- Które są elementy podrzędne elementu nadrzędnego **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Ścieżki JSON jest uwzględniana wielkość liter.
> - Ładunek JSON jest taka sama jak ładunek, który jest zwracany przez:
>   - `/sensors/{id}?includes=properties,types` dla czujnika.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` Czujnik nadrzędnego urządzenia.
>   - `/spaces/{id}?includes=properties,types,location,timezone` Czujnik nadrzędnego miejsca.
> - Porównania jest rozróżniana wielkość liter.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Funkcji zdefiniowanej przez użytkownika jest funkcją niestandardowej, która działa w izolowanym środowisku w reprezentacji urządzeń cyfrowych platformy Azure. Funkcje zdefiniowane przez użytkownika mieć dostęp do czujników nieprzetworzone komunikaty telemetryczne zostało przesłane. Funkcje zdefiniowane przez użytkownika mają również dostęp do przestrzennych usługi graph i dyspozytora. Po zarejestrowaniu funkcji zdefiniowanej przez użytkownika w obrębie grafu dopasowywania (szczegóły przedstawiono powyżej) musi zostać utworzona, aby określić, kiedy uruchamianie funkcji zdefiniowanej przez użytkownika. Gdy Twins cyfrowego Azure odbiera nowe dane telemetryczne z danym czujnika, dopasowany UDF obliczyć ruchomą średnią ostatnich kilku odczyty czujników, na przykład.

Funkcje zdefiniowane przez użytkownika można pisać w języku JavaScript. Deweloperzy mogą wykonywać niestandardowe fragmenty kodu na podstawie komunikaty telemetryczne z czujników. Metody pomocnicze wchodzić w interakcje z wykresem w środowisku wykonawczym zdefiniowanych przez użytkownika. Za pomocą funkcji zdefiniowanej przez użytkownika deweloperzy mogą:

- Ustaw czujnik odczytu bezpośrednio na obiekt czujnika w obrębie wykresu.
- Wykonaj czynności w oparciu o odczyty czujników różnych w obrębie przestrzeni na wykresie.
- Utwórz powiadomienie, gdy są spełnione określone warunki dla czujnika przychodzących do czytania.
- Dołącz grafu metadanych do czujnika czytania przed wysłaniem się powiadomienie.

Aby uzyskać więcej informacji, zobacz [jak używać funkcji zdefiniowanych przez użytkownika](how-to-user-defined-functions.md).

### <a name="role-assignment"></a>Przypisanie roli

Operacje systemu plików UDF podlegają kontroli dostępu opartej na rolach Twins cyfrowych platformy Azure do zabezpieczania danych w ramach usługi. Przypisania ról upewnij się, że danej funkcji zdefiniowanej przez użytkownika ma odpowiednie uprawnienia do interakcji z przestrzennego wykresu. Na przykład funkcji zdefiniowanej przez użytkownika może próbować tworzenia, odczytywania, aktualizowania lub usuwania danych programu graph w ramach danego miejsca. Poziom dostępu systemu plików UDF są sprawdzane, gdy UDF organ wykres danych lub podejmuje akcję. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](security-create-manage-role-assignments.md).

Istnieje możliwość dopasowania w celu wyzwolenia funkcji zdefiniowanej przez użytkownika, który nie ma roli przypisań. W tym przypadku funkcji zdefiniowanej przez użytkownika nie może odczytać danych z wykresu.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o tym, jak kierować zdarzenia i komunikaty telemetryczne z innymi usługami platformy Azure, przeczytaj [kierowanie zdarzeń i komunikatów](concepts-events-routing.md).

* Aby dowiedzieć się więcej o sposobie tworzenia dopasowujące jednostki, funkcje zdefiniowane przez użytkownika i przypisań ról, przeczytaj [przewodnika dotyczące używania funkcji zdefiniowanych przez użytkownika](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
