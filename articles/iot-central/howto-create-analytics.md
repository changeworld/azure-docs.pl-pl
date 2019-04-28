---
title: Analizuj dane urządzenia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Analizuj dane urządzenia w aplikacji usługi Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 05915e2059e45c002a9dfa71a538ed6391caa157
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886531"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak używać usługi analytics do analizowania danych urządzenia

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Usługa Azure IoT Central zapewnia bogate możliwości analizy możliwości sensu dużych ilości danych z urządzeń. Aby rozpocząć pracę, odwiedź stronę **Analytics** w menu nawigacji po lewej stronie.

## <a name="querying-your-data"></a>Wykonywanie zapytań do danych

Musisz wybrać **zestaw urządzeń**, Dodaj **filtru** (opcjonalnie) i wybierz **okres** na rozpoczęcie pracy. Po wykonaniu tych czynności wybierz **Pokaż wyniki** można uruchomić wizualizowania danych.

* **Zestawy urządzeń:** A [zestaw urządzeń](howto-use-device-sets.md) jest zdefiniowane przez użytkownika grupy urządzeń. Na przykład wszystkie lodówki w Oakland lub wszystkich wer. 2.0 turbiny wiatru.

* **Filtry:** Opcjonalnie można dodać filtry do wyszukiwania w taki sposób, aby stworzyć na podstawie posiadanych danych. W czasie, można dodać filtry do 10. Na przykład w ramach wszystkich lodówki w Oakland, znaleźć go te, które miały temperatura powyżej 60 stopni.
* **Okres:** Firma Microsoft, który domyślnie będzie pobierać dane z ostatnich 10 minut. Możesz zmienić tę wartość do jednego z zakresów wstępnie zdefiniowanego czasu, lub wybrać niestandardowy okres.

  ![Zapytanie analizy](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Wizualizowanie danych

Po zostało zniekształcone danych będzie możliwe jej uruchomienie go wizualizacji. Użytkownik może Pokaż/Ukryj pomiarów, zmiana sposobu, w których dane są agregowane i dalsze podzielenie danych za pomocą właściwości innego urządzenia.  

* **Podziel według:** Dzielenie danych według właściwości urządzenia pozwala na dalsze Przechodzenie do szczegółów w swoje dane. Na przykład możesz podzielić wyniki według Identyfikatora urządzenia lub lokalizacji.

* **Pomiary:** Można je pokazać lub ukryć maksymalnie 10 elementów różnych danych telemetrycznych zgłaszane przez urządzenia w danym momencie. Pomiary są elementy, takie jak temperatury i wilgotności.

* **Agregacji:** Domyślnie możemy agregować dane według jego średniej, ale można zmienić sposobu agregacji danych się czymś innym, odpowiednio do potrzeb.

   ![Wizualizacja Analytics podział według](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakcje z danymi

Należy zmienić wyniki zapytania do własnych potrzeb wizualizacji na różne sposoby. Można zamiennie z widoku wykresu i widoku siatki, powiększać i pomniejszać, odświeżanie zestawu danych i zmienić sposób wyświetlania wierszy.

* **Pokaż siatkę:** Wyniki są dostępne w postaci tabeli, które umożliwia wyświetlenie określonej wartości dla każdego punktu danych. Ten widok jest również zgodna z standardów ułatwień dostępu.
* **Pokaż wykres:** Wyniki są wyświetlane w format wiersza, aby ułatwić identyfikację w górę lub dół trendów i anomalii.

  ![Wyświetlanie widoku siatki do celów analizy](media/howto-create-analytics/analytics-showgrid.png)

Powiększenie umożliwia do strony głównej w na podstawie posiadanych danych. Jeśli okaże się okres czasu, którą chcesz się skupić w ramach zestawu wyników, należy użyć kursor do pobrania obszar, który chcesz powiększyć, a następnie wykonaj jedną z następujących czynności za pomocą dostępne kontrolki:

* **Powiększ:** Po wybraniu okres czasu, Powiększ jest włączone i pozwala powiększyć do swoich danych.
* **Pomniejsz:** Ten formant umożliwia jeden poziom pomniejszenie usługi ostatni powiększenia. Na przykład jeśli masz powiększenia w do swoich danych pomniejszenie przyjmuje trzy razy, należy utworzyć kopię jeden krok w danym momencie.
* **Resetuj Powiększenie:** Po zostało wykonane różne poziomy powiększania, można użyć resetowania powiększenia, aby powrócić do oryginalnego zestawu wyników.

  ![Wykonaj powiększanie na podstawie danych](media/howto-create-analytics/analytics-zoom.png)

Możesz zmienić styl linii do własnych potrzeb. Dostępne są cztery opcje:

* **Wiersz:** Płaski wiersz między poszczególnymi punktami danych.
* **Gładki:** Linię krzywej w każdym punkcie.
* **Krok:** Wiersz między każdy punkt wykresu stanowi krok.
* **Wykres punktowy:** Wszystkie punkty są oznaczane na wykresach na wykresie bez linii, łącząc je.

  ![Innym wierszu typów dostępnych w usłudze Analytics](media/howto-create-analytics/analytics-linetypes.png)

Ponadto można rozmieścić dane w osi y, wybierając jedną z trzech trybów:

* **Skumulowany:** Wykres dla każdego pomiaru jest skumulowany i wykresów mają własne osi y. Skumulowane wykresy są przydatne, gdy masz wiele miar zaznaczone i mają widoków pomiarów.
* **Nieskumulowanym:** Wykres dla Każda miara jest wykreślony względem jedną oś y, ale wartości dla osi y są zmieniane w oparciu o wyróżnione miary. Wykresy nieskumulowanych są przydatne do nakładki wiele miar i chcesz zobaczyć wzorców w tych środków dla tego samego zakresu czasu.
* **Oś y udostępnione:** Wszystkich wykresów udostępnianie tej samej osi y i nie zmieniaj wartości dla osi. Udostępnione wykresy osi y są przydatne, gdy chcesz Przyjrzyj się pojedynczej mierze podczas tworzenia wycinków danych przy użyciu podziału przez.

  ![Porządkowanie danych na osi y w trybach inny typ wizualizacji](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak utworzyć niestandardowe analizy dotyczące aplikacji usługi Azure IoT Central tutaj sugerowane następnym krokiem jest:

> [!div class="nextstepaction"]
> [Przygotuj i łączenie aplikacji Node.js](howto-connect-nodejs.md)