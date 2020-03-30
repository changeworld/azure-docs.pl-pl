---
title: Przetwarzanie danych i funkcje zdefiniowane przez użytkownika — Azure Digital Twins| Dokumenty firmy Microsoft
description: Omówienie przetwarzania danych, matcherów i funkcji zdefiniowanych przez użytkownika za pomocą usługi Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265183"
---
# <a name="data-processing-and-user-defined-functions"></a>Przetwarzanie danych i funkcje zdefiniowane przez użytkownika

Usługa Azure Digital Twins oferuje zaawansowane funkcje obliczeniowe. Deweloperzy mogą definiować i uruchamiać funkcje niestandardowe względem przychodzących komunikatów telemetrycznych, aby wysyłać zdarzenia do wstępnie zdefiniowanych punktów końcowych.

## <a name="data-processing-flow"></a>Przepływ przetwarzania danych

Po wysłaniu danych telemetrycznych do usługi Azure Digital Twins deweloperzy mogą przetwarzać dane w czterech fazach: *sprawdzania poprawności,* *dopasowywania,* *obliczania*i *wysyłania.*

[![Przepływ przetwarzania danych usługi Azure Digital Twins](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. Faza sprawdzania poprawności przekształca przychodzący komunikat telemetryczny do powszechnie rozumianego [formatu obiektu transferu danych.](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) Ta faza wykonuje również sprawdzanie poprawności urządzenia i czujnika.
1. Faza dopasowania znajduje odpowiednie funkcje zdefiniowane przez użytkownika do uruchomienia. Wstępnie zdefiniowane matchers znaleźć funkcje zdefiniowane przez użytkownika na podstawie urządzenia, czujnika i miejsca informacji z przychodzącego komunikatu telemetrycznego.
1. Faza obliczeniowa uruchamia funkcje zdefiniowane przez użytkownika dopasowane w poprzedniej fazie. Te funkcje mogą odczytywać i aktualizować obliczone wartości w węzłach wykresu przestrzennego i mogą emitować powiadomienia niestandardowe.
1. Faza wysyłki kieruje wszelkie niestandardowe powiadomienia z fazy obliczeniowej do punktów końcowych zdefiniowanych na wykresie.

## <a name="data-processing-objects"></a>Obiekty przetwarzania danych

Przetwarzanie danych w usłudze Azure Digital Twins polega na zdefiniowaniu trzech obiektów: *matcherów,* *funkcji zdefiniowanych przez użytkownika*i *przypisania ról.*

[![Obiekty przetwarzania danych usługi Azure Digital Twins](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Matchery

Matchers definiują zestaw warunków, które oceniają, jakie akcje odbywają się na podstawie danych telemetrycznych czujnika przychodzącego. Warunki określania dopasowania mogą obejmować właściwości z czujnika, urządzenia nadrzędnego czujnika i przestrzeni nadrzędnej czujnika. Warunki są wyrażone jako porównania ze [ścieżką JSON,](https://jsonpath.com/) jak opisano w tym przykładzie:

- Wszystkie czujniki **temperatury** typu danych reprezentowane przez wartość wysunął ciąg`\"Temperature\"`
- Mając `01` w swoim porcie
- Które należą do urządzeń z rozszerzonym kluczem właściwości **Producent** ustawiony na wartość ciągu umuknąć`\"Contoso\"`
- Które należą do spacji typu określonego przez ciąg umukniony`\"Venue\"`
- Które są elementami podrzędnymi nadrzędnego **spaceid**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - W ścieżkach JSON rozróżniana jest wielkość liter.
> - Ładowność JSON jest taka sama jak ładunek, który jest zwracany przez:
>   - `/sensors/{id}?includes=properties,types`czujnika.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`urządzenia macierzystego czujnika.
>   - `/spaces/{id}?includes=properties,types,location,timezone`przestrzeni macierzystej czujnika.
> - Porównania są niewrażliwe na wielkości liter.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Funkcja zdefiniowana przez użytkownika jest funkcją niestandardową wykonaną w odizolowanym środowisku usługi Azure Digital Twins. Funkcje zdefiniowane przez użytkownika mają dostęp do nieprzetworzonego komunikatu telemetrycznego czujnika po jego odebraniu. Funkcje zdefiniowane przez użytkownika mają również dostęp do wykresu przestrzennego i usługi dyspozytora. Po zarejestrowaniu funkcji zdefiniowanej przez użytkownika w wykresie należy utworzyć matcher (wyszczególnione [powyżej),](#matchers)aby określić, kiedy funkcja jest wykonywana. Na przykład gdy usługa Azure Digital Twins odbiera nowe dane telemetryczne z danego czujnika, dopasowana funkcja zdefiniowana przez użytkownika może obliczyć średnią ruchomą z kilku ostatnich odczytów czujników.

Funkcje zdefiniowane przez użytkownika można zapisywać w języku JavaScript. Metody pomocnika współdziałają z wykresem w środowisku wykonywania zdefiniowanym przez użytkownika. Deweloperzy mogą wykonywać niestandardowe fragmenty kodu względem komunikatów telemetrycznych czujnika. Przykłady:

- Ustaw odczyt czujnika bezpośrednio na obiekcie czujnika w obrębie wykresu.
- Wykonaj akcję na podstawie różnych odczytów czujników w obrębie przestrzeni na wykresie.
- Utwórz powiadomienie, gdy spełnione są określone warunki dla odczytu czujnika przychodzącego.
- Przed wysłaniem powiadomienia dołącz metadane wykresu do odczytu z czujnika.

Aby uzyskać więcej informacji, zobacz [Jak korzystać z funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Przykłady

[Repozytorium GitHub dla przykładu Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/) zawiera kilka przykładów funkcji zdefiniowanych przez użytkownika:
- [Ta funkcja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) wyszukuje wartości dwutlenku węgla, ruchu i temperatury, aby określić, czy pomieszczenie jest dostępne z tymi wartościami w zakresie. [Tutoriale dla cyfrowych bliźniąt](tutorial-facilities-udf.md) eksplorować tę funkcję bardziej szczegółowo. 
- [Ta funkcja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) wyszukuje dane z wielu czujników ruchu i określa, że miejsce jest dostępne, jeśli żaden z nich nie wykryje żadnego ruchu. Możesz łatwo zastąpić zdefiniowaną przez użytkownika funkcję używaną w [przewodniku Szybki start](quickstart-view-occupancy-dotnet.md)lub [samouczkach,](tutorial-facilities-setup.md)wprowadzając zmiany wymienione w sekcji komentarzy pliku. 

### <a name="role-assignment"></a>Przypisanie roli

Akcje funkcji zdefiniowane przez użytkownika podlegają [kontroli dostępu opartej na rolach](./security-role-based-access-control.md) usługi Azure Digital Twins w celu zabezpieczenia danych w ramach usługi. Przypisania ról określają, które funkcje zdefiniowane przez użytkownika mają odpowiednie uprawnienia do interakcji z wykresem przestrzennym i jego encjami. Na przykład funkcja zdefiniowana przez użytkownika może mieć możliwość i uprawnienia do *tworzenia*, *odczytu,* *aktualizacji*lub *usuwania* danych wykresu w danym miejscu. Poziom dostępu funkcji zdefiniowanej przez użytkownika jest sprawdzany, gdy funkcja zdefiniowana przez użytkownika prosi wykres o dane lub próbuje akcji. Aby uzyskać więcej informacji, przeczytaj [artykuł Kontrola dostępu oparta na rolach](./security-create-manage-role-assignments.md).

Jest możliwe dla matcher wyzwolić funkcję zdefiniowaną przez użytkownika, która nie ma przypisania ról. W takim przypadku funkcja zdefiniowana przez użytkownika nie może odczytać żadnych danych z wykresu.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o sposobie kierowania zdarzeń i wiadomości telemetrycznych do innych usług platformy Azure, przeczytaj [artykuł Rozsyłanie zdarzeń i wiadomości](./concepts-events-routing.md).

- Aby dowiedzieć się więcej o tworzeniu matcherów, funkcji zdefiniowanych przez użytkownika i przypisań ról, przeczytaj [Przewodnik dotyczący korzystania z funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md).

- Przejrzyj [dokumentację referencyjną biblioteki klienta zdefiniowanej przez użytkownika](./reference-user-defined-functions-client-library.md).
