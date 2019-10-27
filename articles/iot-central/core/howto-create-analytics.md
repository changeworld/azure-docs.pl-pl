---
title: Analizowanie danych urządzenia w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Analizuj dane urządzenia w aplikacji IoT Central platformy Azure.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 35c085c74cb6713f52d551a188758a7427a93576
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954034"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak analizować dane urządzeń za pomocą analizy

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Usługa Azure IoT Central zapewnia rozbudowane funkcje analityczne, dzięki którym można zrozumieć duże ilości danych z urządzeń. Aby rozpocząć, odwiedź stronę **Analiza** w okienku po lewej stronie.

## <a name="querying-your-data"></a>Wykonywanie zapytania dotyczącego danych

Musisz wybrać **zestaw urządzeń**, dodać **Filtr** (opcjonalnie) i wybrać **okres** , aby rozpocząć pracę. Gdy skończysz, wybierz pozycję **Pokaż wyniki** , aby rozpocząć wizualizację danych.

* **Zestawy urządzeń:** [Zestaw urządzeń](howto-use-device-sets.md) to zdefiniowana przez użytkownika grupa urządzeń. Na przykład wszystkie chłodziarki w Oakland lub wszystkie turbiny wiatru rev 2,0.

* **Filtry:** Opcjonalnie możesz dodać filtry do wyszukiwania, aby stworzyć właściwy na swoich danych. Jednocześnie można dodać do 10 filtrów. Na przykład w obszarze wszystkie chłodziarki w Oakland Znajdź te, które miały temperaturę powyżej 60 stopni.
* **Okres:** Domyślnie będziemy pobierać dane z ostatnich 10 minut. Tę wartość można zmienić na jeden z wstępnie zdefiniowanych zakresów czasu lub wybrać niestandardowy okres czasu.

  ![Zapytanie analityczne](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Wizualizacja danych

Po wykonaniu zapytania o dane można rozpocząć wizualizację. Możesz pokazać/ukryć pomiary, zmienić sposób agregowania danych, a następnie podzielić dane na różne właściwości.  

* **Podziel według:** Dzielenie danych według właściwości urządzenia pozwala na dalsze przechodzenie do danych. Można na przykład podzielić wyniki według identyfikatora lub lokalizacji urządzenia.

* **Pomiary:** Możesz wybrać opcję wyświetlania/ukrywania maksymalnie 10 różnych elementów telemetrycznych raportowanych przez urządzenia w danym momencie. Pomiary są takie jak temperatury i wilgotności.

* **Agregacja:** Domyślnie dane są agregowane według średniej, ale można wybrać opcję zmiany agregacji danych na inną, aby odpowiadała potrzebom.

   ![Wizualizacja analizy podzielona przez](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Korzystanie z danych

Istnieją różne sposoby zmiany wyników zapytania w celu spełnienia wymagań wizualizacji. Można zmienić widok wykresu i widok siatki, powiększyć i wyprowadzić, odświeżyć zestaw danych i zmodyfikować sposób wyświetlania wierszy.

* **Pokaż siatkę:** Wyniki są dostępne w formacie tabeli, umożliwiając wyświetlanie określonej wartości dla każdego punktu danych. Ten widok jest również zgodny ze standardami dostępności.
* **Pokaż wykres:** Wyniki są wyświetlane w formacie wiersza ułatwiającym identyfikowanie trendów w górę i w dół oraz anomalii.

  ![Wyświetlanie widoku siatki dla analizy](media/howto-create-analytics/analytics-showgrid.png)

Opcja Powiększ umożliwia zalogowanie się do danych. Jeśli znajdziesz okres, na którym chcesz się skoncentrować w zestawie wyników, Użyj kursora, aby pomieścić obszar, w którym chcesz powiększyć, i użyj dostępnych kontrolek, aby wykonać jedną z następujących czynności:

* **Powiększ:** Po wybraniu okresu powiększanie jest włączone i umożliwia powiększanie danych.
* **Pomniejsz:** Ta kontrolka umożliwia pomniejszenie poziomu z ostatniego powiększenia. Na przykład jeśli powiększasz dane trzykrotnie, Pomniejsz w tym czasie zostanie pożądany jeden krok.
* **Resetuj powiększenie:** Po wykonaniu różnych poziomów powiększania można użyć kontrolki resetowania powiększenia, aby powrócić do oryginalnego zestawu wyników.

  ![Powiększanie danych](media/howto-create-analytics/analytics-zoom.png)

Styl linii można zmienić zgodnie z potrzebami. Dostępne są cztery opcje:

* **Wiersz:** Linia płaska między poszczególnymi punktami danych.
* **Wygładź:** Linia zakrzywiona między poszczególnymi punktami.
* **Krok:** Wiersz między każdym punktem na wykresie to krok.
* **Punktowy:** Wszystkie punkty są kreślone na wykresie bez łączenia się z nimi.

  ![Różne typy linii dostępne w analizie](media/howto-create-analytics/analytics-linetypes.png)

Na koniec można rozmieścić dane na osi Y, wybierając jeden z trzech trybów:

* **Stos:** Wykres dla każdej miary jest układany, a każdy wykres ma własną oś Y. Wykresy skumulowane są przydatne, gdy zaznaczono wiele pomiarów i chcesz mieć odrębny widok tych pomiarów.
* **Nieskumulowany:** Wykres dla każdej miary jest wykreślany na jedną oś Y, ale wartości osi Y są zmieniane na podstawie wyróżnionej miary. Wykresy nieskumulowane są przydatne, gdy chcesz nałożyć wiele miar i chcieć zobaczyć wzorce dla tych miar dla tego samego zakresu czasu.
* **Współdzielona oś Y:** Wszystkie wykresy mają tę samą oś Y i wartości osi nie zmieniają się. Udostępnione wykresy osi Y są przydatne, gdy chcesz przyjrzeć się pojedynczej mierze w trakcie dzielenia danych na podzielone przez.

  ![Rozmieszczanie danych na osi y z różnymi trybami wizualizacji](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak utworzyć niestandardową analizę dla aplikacji IoT Central platformy Azure, w tym miejscu zalecanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Przygotowywanie i łączenie aplikacji node. js](howto-connect-nodejs.md)