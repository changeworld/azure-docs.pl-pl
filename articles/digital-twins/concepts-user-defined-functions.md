---
title: Przetwarzanie danych i funkcje zdefiniowane przez użytkownika przy użyciu usługi Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Omówienie przetwarzania i dopasowywania danych oraz funkcji zdefiniowanych przez użytkownika przy użyciu usługi Azure Digital bliźniaczych reprezentacji.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: alinast
ms.openlocfilehash: 07facf06702a63df8ea93d43b9896b72322b209f
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178251"
---
# <a name="data-processing-and-user-defined-functions"></a>Przetwarzanie danych i funkcje zdefiniowane przez użytkownika

Usługa Azure Digital bliźniaczych reprezentacji oferuje zaawansowane możliwości obliczeniowe. Deweloperzy mogą definiować i uruchamiać funkcje niestandardowe w odniesieniu do przychodzących komunikatów telemetrycznych w celu wysyłania zdarzeń do wstępnie zdefiniowanych punktów końcowych.

## <a name="data-processing-flow"></a>Przepływ przetwarzania danych

Gdy urządzenia wysyłają dane telemetryczne do usługi Azure Digital bliźniaczych reprezentacji, deweloperzy mogą przetwarzać dane w czterech fazach: *Walidacja*, *dopasowanie*, *Obliczanie*i *wysyłanie*.

[![Przepływ przetwarzania danych Digital bliźniaczych reprezentacji systemu Azure](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. Faza walidacji przekształca przychodzące komunikaty telemetryczne do powszechnie rozumiany format [obiektu transferu danych](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) . W tej fazie wykonywane jest również Walidacja urządzenia i czujnika.
1. Etap dopasowywania umożliwia znalezienie odpowiednich funkcji zdefiniowanych przez użytkownika do uruchomienia. Wstępnie zdefiniowane dopasowania znajdują funkcje zdefiniowane przez użytkownika na podstawie informacji o urządzeniu, czujniku i miejscu z przychodzącego komunikatu telemetrii.
1. Faza obliczeń uruchamia zdefiniowane przez użytkownika funkcje dopasowane w poprzedniej fazie. Te funkcje mogą odczytywać i aktualizować obliczone wartości w węzłach wykresu przestrzennego i mogą emitować niestandardowe powiadomienia.
1. Faza wysyłania kieruje wszystkie niestandardowe powiadomienia z fazy obliczeń do punktów końcowych zdefiniowanych w grafie.

## <a name="data-processing-objects"></a>Obiekty przetwarzania danych

Przetwarzanie danych w usłudze Azure Digital bliźniaczych reprezentacji składa się z definiowania trzech obiektów: *dopasowań*, *funkcji zdefiniowanych przez użytkownika*i *przypisań ról*.

[![Obiekty przetwarzania danych Digital bliźniaczych reprezentacji na platformie Azure](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Dopasowanie

Dopasowania definiują zestaw warunków, które sprawdzają, jakie akcje odbywają się w oparciu o przychodzące dane telemetryczne czujnika. Warunki do określenia dopasowania mogą obejmować właściwości z czujnika, urządzenie nadrzędne czujnika i przestrzeń nadrzędną czujnika. Warunki są wyrażane jako porównania ze [ścieżką JSON](https://jsonpath.com/) , jak pokazano w tym przykładzie:

- Wszystkie czujniki **temperatury** elementu DataType reprezentowane przez wartość ciągu ucieczki`\"Temperature\"`
- Posiadanie `01` w porcie
- Który należy do urządzeń z kluczem właściwości rozszerzonej **producent** ustawiony na wartość ciągu o zmienionym znaczeniu`\"GoodCorp\"`
- Które należy do spacji typu określonego przez ciąg ucieczki`\"Venue\"`
- Które są elementami podrzędnymi elementu nadrzędnego **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

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
> - W ścieżkach JSON jest rozróżniana wielkość liter.
> - Ładunek JSON jest taki sam jak ładunek zwracany przez:
>   - `/sensors/{id}?includes=properties,types`dla czujnika.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`dla urządzenia nadrzędnego czujnika.
>   - `/spaces/{id}?includes=properties,types,location,timezone`dla obszaru nadrzędnego czujnika.
> - W porównaniach jest rozróżniana wielkość liter.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Funkcja zdefiniowana przez użytkownika jest funkcją niestandardową wykonywaną w izolowanym środowisku usługi Azure Digital bliźniaczych reprezentacji. Funkcje zdefiniowane przez użytkownika mają dostęp do komunikatu telemetrii nieprzetworzonej czujnika, gdy zostanie on odebrany. Funkcje zdefiniowane przez użytkownika mają również dostęp do przestrzennego wykresu i usługi dyspozytora. Po zarejestrowaniu funkcji zdefiniowanej przez użytkownika w grafie należy utworzyć odpowiednika (szczegółowo [powyżej](#matchers)), aby określić czas wykonywania funkcji. Na przykład gdy usługa Azure Digital bliźniaczych reprezentacji otrzymuje nową telemetrię z danego czujnika, dopasowana funkcja zdefiniowana przez użytkownika może obliczyć średnią przesunięcia z ostatnich kilku odczytów czujnika.

Funkcje zdefiniowane przez użytkownika mogą być zapisywane w języku JavaScript. Metody pomocnika współpracują z wykresem w środowisku wykonawczym zdefiniowanym przez użytkownika. Deweloperzy mogą wykonywać niestandardowe fragmenty kodu w odniesieniu do komunikatów telemetrycznych czujnika. Przykłady:

- Ustaw odczyt czujnika bezpośrednio do obiektu czujnika na grafie.
- Wykonaj akcję w oparciu o różne odczyty czujników w obrębie obszaru wykresu.
- Utwórz powiadomienie w przypadku spełnienia określonych warunków w przypadku odczytywania czujnika przychodzącego.
- Dołącz metadane grafu do odczytu czujnika przed wysłaniem powiadomienia.

Aby uzyskać więcej informacji, zobacz [jak korzystać z funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Przykłady

[Repozytorium GitHub dla przykładu Digital bliźniaczych reprezentacji C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp/) zawiera kilka przykładów funkcji zdefiniowanych przez użytkownika:
- [Ta funkcja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) szuka wartości ditlenku węgla, ruchu i temperatury, aby określić, czy pomieszczenie jest dostępne z tymi wartościami w zakresie. [Samouczki dotyczące usługi Digital bliźniaczych reprezentacji](tutorial-facilities-udf.md) eksplorują tę funkcję w bardziej szczegółowy sposób. 
- [Ta funkcja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) szuka danych z wielu czujników ruchu i określa, że ilość miejsca jest dostępna, jeśli żadna z nich nie wykryje żadnego ruchu. Można łatwo zastąpić funkcję zdefiniowaną przez użytkownika w przewodniku [Szybki Start](quickstart-view-occupancy-dotnet.md)lub [samouczków](tutorial-facilities-setup.md), wprowadzając zmiany wymienione w sekcji komentarzy pliku. 

### <a name="role-assignment"></a>Przypisanie roli

Akcje funkcji zdefiniowane przez użytkownika podlegają [kontroli dostępu opartej na rolach](./security-role-based-access-control.md) usługi Azure Digital bliźniaczych reprezentacji w celu zabezpieczania danych w ramach usługi. Przypisania ról definiują, które funkcje zdefiniowane przez użytkownika mają odpowiednie uprawnienia do współdziałania z wykresem przestrzennym i jego jednostkami. Na przykład funkcja zdefiniowana przez użytkownika może mieć możliwość i uprawnienia do *tworzenia*, *odczytywania*, *aktualizowania*lub *usuwania* danych grafu w danym miejscu. Poziom dostępu zdefiniowany przez użytkownika jest sprawdzany, gdy funkcja zdefiniowana przez użytkownika prosi wykres o dane lub próbuje wykonać akcję. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach](./security-create-manage-role-assignments.md).

Istnieje możliwość, aby można było wyzwolić funkcję zdefiniowaną przez użytkownika, która nie ma przypisań ról. W takim przypadku funkcja zdefiniowana przez użytkownika nie odczyta żadnych danych z grafu.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o kierowaniu zdarzeń i komunikatów telemetrycznych do innych usług platformy Azure, Odczytaj [zdarzenia i komunikaty dotyczące trasy](./concepts-events-routing.md).

- Aby dowiedzieć się więcej na temat tworzenia dopasowań, funkcji zdefiniowanych przez użytkownika i przypisań ról, Przeczytaj [Przewodnik dotyczący korzystania z funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md).

- Przejrzyj [dokumentację dotyczącą biblioteki klienta funkcji zdefiniowanej przez użytkownika](./reference-user-defined-functions-client-library.md).
