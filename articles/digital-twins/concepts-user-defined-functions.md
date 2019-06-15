---
title: Przetwarzanie danych i funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie przetwarzania danych, dopasowujące jednostki i funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.openlocfilehash: 4db515a931bc7f423eb11ae31b7304a602f0da46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60925924"
---
# <a name="data-processing-and-user-defined-functions"></a>Przetwarzanie danych i funkcje zdefiniowane przez użytkownika

Twins cyfrowych platformy Azure oferuje możliwości zaawansowanych obliczeń. Deweloperzy mogą zdefiniować i uruchamiać funkcje niestandardowe wiadomości przychodzących danych telemetrycznych do wysyłania zdarzeń do wstępnie zdefiniowanych punktów końcowych.

## <a name="data-processing-flow"></a>Przepływ przetwarzania danych

Po urządzenia wysyłają dane telemetryczne do reprezentacji urządzeń cyfrowych platformy Azure, deweloperzy może przetwarzać dane w czterech fazach: *zweryfikować*, *dopasowania*, *obliczenia*, i *wysyłania* .

![Usługa Azure przepływ przetwarzania danych cyfrowych bliźniaczych reprezentacji][1]

1. Faza sprawdzania poprawności przekształca przychodzące komunikaty telemetryczne w celu prezentacji typowe [obiekt transferu danych](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) formatu. Ta faza wykonuje również weryfikacji urządzeń i czujników.
1. Faza dopasowanie wyszukuje odpowiednie funkcje zdefiniowane przez użytkownika do uruchomienia. Wstępnie zdefiniowane dopasowujące jednostki Znajdź funkcje zdefiniowane przez użytkownika na podstawie urządzeń, czujników i informacji o miejscu na przychodzące komunikaty telemetryczne.
1. Faza obliczeń działa funkcje zdefiniowane przez użytkownika dopasowane w poprzednim kroku. Te funkcje mogą odczytywać i aktualizować obliczonych wartości na przestrzenne programu graph węzły i może emitować niestandardowe powiadomienia.
1. Faza wysyłania trasy niestandardowe powiadomienia z fazy obliczeń do punktów końcowych zdefiniowanych na wykresie.

## <a name="data-processing-objects"></a>Przetwarzanie danych obiektów

Przetwarzanie danych w reprezentacji urządzeń cyfrowych platformy Azure składa się z Definiowanie trzy obiekty: *dopasowujące jednostki*, *funkcje zdefiniowane przez użytkownika*, i *przypisań ról*.

![Azure cyfrowego bliźniaczych reprezentacji przetwarzanie danych obiektów][2]

<div id="matcher"></div>

### <a name="matchers"></a>Dopasowujące jednostki

Dopasowujące jednostki definiują zestaw warunków określających, jakie akcje wykonane, na podstawie przychodzących danych telemetrycznych z czujnika. Aby określić dopasowanie może to być właściwości z czujnika, czujnik nadrzędnego urządzenia i czujnik nadrzędnego miejsca. Warunki są wyrażane jako porównania z typem [ścieżkę JSON](https://jsonpath.com/) zgodnie z opisem w tym przykładzie:

- Wszystkie czujniki datatype **temperatury** reprezentowany przez wartość ciągu o zmienionym znaczeniu `\"Temperature\"`
- Posiadanie `01` ich portu
- Które należą do urządzenia przy użyciu klucza właściwości rozszerzonej **producenta** ustawiona na wartość ciągu o zmienionym znaczeniu `\"GoodCorp\"`
- Które należą do miejsca do magazynowania o typie określonym przez ciąg o zmienionym znaczeniu `\"Venue\"`
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

Funkcji zdefiniowanej przez użytkownika jest niestandardowa funkcja wykonana w izolowanym środowisku Twins cyfrowych platformy Azure. Funkcje zdefiniowane przez użytkownika mieć dostęp do czujników nieprzetworzone komunikaty telemetryczne pobiera otrzymał. Funkcje zdefiniowane przez użytkownika również mieć dostęp do przestrzennych usługi graph i dyspozytorem. Po zarejestrowaniu funkcji zdefiniowanej przez użytkownika w ramach wykres dopasowywania (szczegółowe [powyżej](#matcher)) musi zostać utworzona w celu określenia, kiedy funkcja zostanie uruchomiona. Na przykład gdy Twins cyfrowego Azure odbiera nowe dane telemetryczne z danym czujnika, dopasowany funkcji zdefiniowanej przez użytkownika można obliczyć ruchomą średnią ostatnich kilku odczyty czujników.

Funkcje zdefiniowane przez użytkownika można pisać w języku JavaScript. Metody pomocnicze wchodzić w interakcje z wykresem w środowisku wykonawczym zdefiniowanych przez użytkownika. Deweloperzy mogą wykonywać niestandardowe fragmenty kodu na podstawie komunikaty telemetryczne z czujników. Przykłady:

- Ustaw czujnik odczytu bezpośrednio na obiekt czujnika w obrębie wykresu.
- Wykonaj czynności w oparciu o odczyty czujników różnych w obrębie przestrzeni na wykresie.
- Utwórz powiadomienie, gdy są spełnione określone warunki dla czujnika przychodzących do czytania.
- Dołącz grafu metadanych do czujnika czytania przed wysłaniem się powiadomienie.

Aby uzyskać więcej informacji, zobacz [jak używać funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md).


#### <a name="examples"></a>Przykłady

[Repozytorium GitHub dla cyfrowego Twins C# przykładowe](https://github.com/Azure-Samples/digital-twins-samples-csharp/) zawiera kilka przykładów funkcje zdefiniowane przez użytkownika:
- [Ta funkcja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) szuka emisji dwutlenku węgla, ruchu i wartości temperatury, aby określić, czy pokoju jest dostępna z tymi wartościami w zakresie. [Samouczki bliźniaki cyfrowego](tutorial-facilities-udf.md) zapoznaj się z tej funkcji, które bardziej szczegółowo. 
- [Ta funkcja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) szuka danych z wielu czujników ruchu i ustali, że miejsce jest dostępna, jeśli żadna z nich wykryć wszelkie ruchu. Można łatwo zastąpić funkcji zdefiniowanej przez użytkownika używane w każdym [Szybki Start](quickstart-view-occupancy-dotnet.md), lub [samouczki](tutorial-facilities-setup.md), wprowadzając zmiany, o których wspomniano w sekcji komentarze w pliku. 



### <a name="role-assignment"></a>Przypisanie roli

Akcje funkcji zdefiniowanej przez użytkownika podlegają Twins cyfrowego Azure [kontroli dostępu opartej na rolach](./security-role-based-access-control.md) do zabezpieczania danych w ramach usługi. Przypisania ról definiują, jakie funkcje zdefiniowane przez użytkownika ma odpowiednie uprawnienia do interakcji z przestrzennego wykresu i jego jednostek. Na przykład funkcji zdefiniowanej przez użytkownika może mieć możliwości i uprawnienia do *Utwórz*, *odczytu*, *aktualizacji*, lub *Usuń* danych wykresu w obszarze danego miejsca. Poziom dostępu w funkcji zdefiniowanej przez użytkownika jest sprawdzany w funkcji zdefiniowanej przez użytkownika organ wykres danych lub podejmuje akcję. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](./security-create-manage-role-assignments.md).

Istnieje możliwość dopasowania w celu wyzwolenia funkcji zdefiniowanej przez użytkownika, który nie ma roli przypisań. W takim przypadku funkcja zdefiniowana przez użytkownika nie może odczytać wszystkie dane z wykresu.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o tym, jak kierować zdarzenia i komunikaty telemetryczne z innymi usługami platformy Azure, przeczytaj [kierowanie zdarzeń i komunikatów](./concepts-events-routing.md).

- Aby dowiedzieć się więcej o sposobie tworzenia dopasowujące jednostki, funkcje zdefiniowane przez użytkownika i przypisań ról, przeczytaj [przewodnika dotyczące używania funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md).

- Przegląd [funkcji zdefiniowanej przez użytkownika klienta biblioteki dokumentacji](./reference-user-defined-functions-client-library.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
