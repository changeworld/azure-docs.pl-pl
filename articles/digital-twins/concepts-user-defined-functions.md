---
title: Przetwarzanie danych i funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie dopasowujące jednostki, funkcje zdefiniowane przez użytkownika za pomocą Twins cyfrowych platformy Azure i przetwarzania danych
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: a45f82b142ee4f4c9c88ea755607b88323feaae5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210129"
---
# <a name="data-processing-and-user-defined-functions"></a>Przetwarzanie danych i funkcje zdefiniowane przez użytkownika

Twins cyfrowych platformy Azure oferuje możliwości zaawansowanych obliczeń. Deweloperzy mogą zdefiniować i uruchamiać funkcje niestandardowe wiadomości przychodzących danych telemetrycznych do wysyłania zdarzeń do wstępnie zdefiniowanych punktów końcowych.

## <a name="data-processing-flow"></a>Przepływ przetwarzania danych

Gdy urządzenia wysyłają dane telemetryczne do reprezentacji urządzeń cyfrowych, deweloperzy może przetwarzać dane w czterech fazach: _zweryfikować_, _dopasowania_, _obliczenia_, i _wysyłania_:

![Przepływ przetwarzania danych cyfrowych bliźniaczych reprezentacji][1]

1. _Zweryfikować_ fazy przekształca przychodzące komunikaty telemetryczne w celu prezentacji typowe [ **obiekt transferu danych** ](https://en.wikipedia.org/wiki/Data_transfer_object) formatu. Ta faza wykonuje również weryfikacji urządzeń i czujników.
1. _Dopasowania_ fazy umożliwia znalezienie odpowiednich funkcji zdefiniowane przez użytkownika do uruchomienia. Wstępnie zdefiniowane dopasowujące jednostki znajdzie funkcji zdefiniowane przez użytkownika zależy od urządzeń, czujników i informacji o miejscu na przychodzące komunikaty telemetryczne.
1. _Obliczenia_ fazy uruchamia funkcji User-defined dopasowywany w poprzedniej fazy. Tych funkcji mogą odczytu i aktualizacji obliczone wartości w węzłach przestrzenne programu graph i może emitować niestandardowe powiadomienia.
1. _Wysyłania_ fazy trasy niestandardowe powiadomienia z fazy obliczeń do punktów końcowych zdefiniowanych na wykresie.

## <a name="data-processing-objects"></a>Przetwarzanie danych obiektów

Przetwarzanie danych w reprezentacji urządzeń cyfrowych platformy Azure składa się z Definiowanie trzy obiekty: _dopasowujące jednostki_, _funkcje zdefiniowane przez użytkownika_, i _przypisań ról_:

![Przepływ przetwarzania danych cyfrowych bliźniaczych reprezentacji][2]

### <a name="matchers"></a>Dopasowujące jednostki

_Dopasowujące jednostki_ zdefiniować zestaw warunków określających, jakie akcje zostaną zrealizowane na podstawie przychodzących danych telemetrycznych z czujnika. Te warunki, aby określić dopasowania mogą obejmować właściwości z czujnika, czujnik nadrzędnego urządzenia i czujnik nadrzędnego miejsca. Warunki są wyrażane jako porównania z typem [ścieżkę JSON](http://jsonpath.com/) tak jak pokazano w poniższym przykładzie:

- Wszystkie czujniki datatype **temperatury**.
- Posiadanie `01` ich portu.
- Które należą do urządzenia przy użyciu klucza właściwości rozszerzonej **producenta** ustawiona na wartość `"GoodCorp"`.
- Które należą do miejsca do magazynowania o typie `"Venue"`.
- Które są elementy podrzędne elementu nadrzędnego **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`.

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
> - Ścieżki JSON jest rozróżniana wielkość liter.
> - Ładunek JSON jest taka sama jak ładunek, który będzie zwracany przez:
>   - `/sensors/{id}?includes=properties,types` dla czujnika.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` Czujnik nadrzędnego urządzenia.
>   - `/spaces/{id}?includes=properties,types,location,timezone` Czujnik nadrzędnego miejsca.
> - Porównania uwzględniają wielkość liter.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

A _funkcji zdefiniowanej przez użytkownika_, lub _UDF_, jest funkcją niestandardowej, która działa w izolowanym środowisku w reprezentacji urządzeń cyfrowych platformy Azure. Funkcje zdefiniowane przez użytkownika mieć dostęp do komunikat czujnik nieprzetworzone dane telemetryczne o została odebrana, jak również co przestrzennych usługi graph i dyspozytora. Po zarejestrowaniu funkcji zdefiniowanej przez użytkownika w obrębie grafu dopasowywania (szczegóły przedstawiono powyżej) musi zostać utworzona, aby określić, kiedy uruchamianie funkcji zdefiniowanej przez użytkownika. Gdy cyfrowego Twins otrzymuje nowe dane telemetryczne z danym czujnika, dopasowany UDF obliczyć ruchomą średnią ostatnich kilku odczyty czujników, na przykład.

Funkcje zdefiniowane przez użytkownika mogą być zapisywane w języku JavaScript i umożliwia deweloperom wykonywanie niestandardowe fragmenty kodu na podstawie komunikaty telemetryczne z czujników. Dostępne są także metody pomocnika do interakcji z wykresu w środowisku wykonawczym zdefiniowanych przez użytkownika. Za pomocą funkcji zdefiniowanej przez użytkownika deweloperzy mogą:

- Ustaw czujnik odczytu bezpośrednio na obiekt czujnika w obrębie wykresu.
- Wykonaj czynności w oparciu o odczyty czujników różnych w obrębie przestrzeni na wykresie.
- Utwórz powiadomienie, gdy są spełnione określone warunki dla czujnika przychodzących do czytania.
- Dołącz grafu metadanych do czujnika czytania przed wysłaniem się powiadomienie.

Zapoznaj się [jak funkcje User User-Defined](how-to-user-defined-functions.md) Aby uzyskać więcej informacji.

### <a name="role-assignment"></a>Przypisanie roli

Operacje systemu plików UDF podlegają kontroli dostępu opartej na rolach Twins cyfrowych do zabezpieczania danych w ramach usługi. Przypisania ról upewnij się, że danej funkcji zdefiniowanej przez użytkownika ma odpowiednie uprawnienia do interakcji z przestrzennego wykresu. Na przykład funkcji zdefiniowanej przez użytkownika może próbować tworzenia, odczytywania, aktualizowania lub usuwania danych programu graph w ramach danego miejsca. Poziom dostępu systemu plików UDF są sprawdzane, gdy UDF organ wykres danych lub podejmuje akcję. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](security-create-manage-role-assignments.md).

Istnieje możliwość dopasowania w celu wyzwolenia funkcji zdefiniowanej przez użytkownika, który nie ma roli przypisań. W tym przypadku funkcji zdefiniowanej przez użytkownika może zakończyć się niepowodzeniem na odczyt danych z wykresu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o sposobie routingu zdarzenia i komunikaty telemetryczne z innymi usługami platformy Azure, przeczytaj [Routing zdarzeń i komunikatów](concepts-events-routing.md).

Aby dowiedzieć się więcej o sposobie tworzenia dopasowujące jednostki, funkcje zdefiniowane przez użytkownika i przypisań ról, przeczytaj [przewodnika dotyczące używania funkcji zdefiniowanych przez użytkownika](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
