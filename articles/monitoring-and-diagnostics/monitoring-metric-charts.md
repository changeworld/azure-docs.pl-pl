---
title: Eksplorator metryk usługi Azure Monitor
description: Informacje o nowych funkcjach w Eksploratorze metryk usługi Azure Monitor
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: 21b0029ff12915c8416ad2366fbf6c45ddfaa288
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978427"
---
# <a name="azure-monitor-metrics-explorer"></a>Eksplorator metryk usługi Azure Monitor

Niniejszy instruktaż opisano następnej generacji metryk usługi Azure Monitor wykresów środowisko, jest obecnie dostępna w publicznej wersji zapoznawczej. Nowe środowisko obsługuje renderowanie wykresów dla metryk wielowymiarowych i podstawowe metryki z żadnych wymiarów. Możliwe będzie wykreślanie wykresów, które nakładki metryk z różnych typów zasobów, subskrypcji i wiele grup zasobów. Wykresy metryk wielowymiarowych można dostosować przez zastosowanie filtrów wymiaru, a także grupowania. Dowolny wykres, w tym dostosowane wykresy można przypiąć do pulpitów nawigacyjnych.

Jeśli szukasz informacji na temat stare środowisko, który obsługuje tylko podstawowe metryki z żadnych wymiarów, zobacz sekcję zatytułowaną "Uzyskiwać dostęp do metryk za pośrednictwem portalu" w [przewodnik Przegląd usługi Microsoft Azure metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>Co to jest Eksploratora metryk usługi Azure Monitor?

Eksplorator metryk usługi Azure Monitor to składnik systemu Microsoft Azure portal, która umożliwia wykreślanie wykresów, wizualne korelowanie trendów i badanie wzrostów i spadki wartości metryk. Eksplorator metryk jest istotne punkt początkowy, badania różne problemy z wydajnością i dostępności przy użyciu aplikacji i infrastruktury hostowany na platformie Azure lub monitorowane przez usługi Azure Monitor. 

## <a name="what-are-metrics-in-azure"></a>Co to są metryki na platformie Azure?

Metryki w systemie Microsoft Azure są szeregu mierzonych i liczniki, które są zbierane i przechowywane wraz z upływem czasu. Brak metryk standardowa (lub "platforma") i metryki niestandardowe. Metryki standardowe są dostarczane przez samą platformę Azure. Standardowa metryki odzwierciedlają statystyki użycia i kondycji zasobów platformy Azure. Natomiast metryki niestandardowe są wysyłane do platformy Azure przy użyciu aplikacji przy użyciu [interfejsu API usługi Application Insights dla niestandardowych zdarzeń](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Metryki niestandardowe są przechowywane w zasoby usługi Application Insights wraz z innymi metrykami określonych aplikacji.



## <a name="how-do-i-create-a-new-chart"></a>Jak utworzyć nowy wykres?

   > [!NOTE]
   > Niektóre funkcje stare środowisko metryki nie są jeszcze dostępne w nowym Eksploratorze metryk. Nowe środowisko jest w wersji zapoznawczej, możesz kontynuować używanie stary (innych niż wymiarowe) widok metryk usługi Azure Monitor. 

1. Otwórz witrynę Azure portal
2. Przejdź do nowego **Monitor** , a następnie wybierz pozycję **metryki (wersja zapoznawcza)**.

   ![Obraz podglądu metryki](./media/monitoring-metric-charts/0001.png)

3. **Metryki selektor** zostanie automatycznie otwarte dla Ciebie. Wybierz zasób z listy, aby wyświetlić jego skojarzonych z nimi metryk. Tylko zasoby z metryki są wyświetlane na liście.

   ![Obraz podglądu metryki](./media/monitoring-metric-charts/0002.png)

   > [!NOTE]
   >Jeśli masz więcej niż jedną subskrypcję platformy Azure, Eksplorator metryk ściąga się z zasobami we wszystkich subskrypcjach, które są wybrane w ustawieniach portalu -> filtru według listy subskrypcji. Aby je zmienić, kliknij ikonę koła zębatego ustawień portalu na górze ekranu i wybierz pozycję subskrypcje, które chcesz użyć.

4. W przypadku niektórych typów zasobów (czyli konta magazynu i maszyn wirtualnych), przed wybraniem metrykę, musisz wybrać **Namespace**. Każda przestrzeń nazw zawiera swój własny zestaw metryk, które mają zastosowanie tylko w tej przestrzeni nazw, a nie inne przestrzenie nazw.

   Na przykład każdej usługi Azure Storage ma metryki subservices "Blob", "Files", "Kolejki" i "Tabele", które są wszystkie elementy na koncie magazynu. Jednak metryki "Liczba komunikatów w kolejce" dotyczy naturalnie Usługa "W kolejce", a nie innych subservices konta magazynu.

   ![Obraz podglądu metryki](./media/monitoring-metric-charts/0003.png)

5. Wybierz metrykę, z listy. Jeśli znasz część nazwy metryki, które chcesz, możesz zacząć wpisywać go w będzie filtrowana lista dostępnych metryk:

   ![Obraz podglądu metryki](./media/monitoring-metric-charts/0004.png)

6. Po wybraniu metrykę, wykres będą renderowane przy użyciu domyślna agregacja dla wybranej metryki. W tym momencie możesz po prostu kliknąć opuszczenie **selektor metryki** go zamknąć. Wykres można również opcjonalnie przełączyć się do różnych agregacji. Niektóre metryki agregacji przełączanie umożliwia można wybrać wartość, która mają być wyświetlane na wykresie. Na przykład można przełączać się między wartość średnią, minimalną i maksymalną. 

7. Klikając ikonę Dodaj metrykę ![Ikona metryk](./media/monitoring-metric-charts/icon001.png) i powtórzyć kroki od 3 do 6 możesz dodać więcej metryk na tym samym wykresie.

   > [!NOTE]
   > Zwykle nie chcesz mieć metryki różne jednostki miary (np. "milisekund" i "kilobajtów") lub z znacząco odmiennych skalowania na jeden wykres. Zamiast tego należy wziąć pod uwagę przy użyciu wielu wykresów. Kliknij przycisk Dodaj wykres, aby utworzyć wiele wykresów w Eksploratorze metryk.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Jak zastosować filtry do wykresów?

Filtry można stosować do wykresów przedstawiających metryk z wymiarami. Na przykład, jeśli metryki "Liczba transakcji" ma wymiar, "Typ odpowiedzi", który wskazuje, czy odpowiedź z transakcji zakończyło się pomyślnie lub nie powiodło się, następnie filtrowanie tego wymiaru będzie wykreślanie wiersz wykresów dla tylko pomyślne (lub tylko nie powiodło się) transakcji. 

### <a name="to-add-a-filter"></a>Aby dodać filtr:

1. Kliknij ikonę Dodaj filtr ![ikona filtru](./media/monitoring-metric-charts/icon002.png) nad wykresem

2. Wybierz, który wymiar (właściwość), do których chcesz filtrować

   ![metryki obrazu](./media/monitoring-metric-charts/0006.png)

3. Wybierz wartości wymiarów, które mają zostać uwzględnione podczas wykreślania wykresu (w tym przykładzie przedstawiono filtrowanie transakcje magazynowe pomyślnych):

   ![metryki obrazu](./media/monitoring-metric-charts/0007.png)

4. Po wybraniu wartości filtru, kliknij poza selektor filtrów, aby je zamknąć. Teraz wykres pokazuje, ile transakcji magazynu nie powiodło się:

   ![metryki obrazu](./media/monitoring-metric-charts/0008.png)

5. Możesz powtórzyć kroki 1 – 4, aby zastosować wiele filtrów do tych samych wykresów.

## <a name="how-do-i-segment-a-chart"></a>Jak podzielić wykres?

Podziel metryki według wymiaru, aby zwizualizować segmentów jak różne metryki porównania między nimi i zidentyfikować odległe mniejsze segmenty wymiaru. 

### <a name="to-segment-a-chart"></a>Aby podzielić wykres:

1. Kliknij ikonę Dodawanie grupowania  ![metryki obrazu](./media/monitoring-metric-charts/icon003.png) nad wykresem.
 
   > [!NOTE]
   > Wiele filtrów, ale tylko jedna grupa może mieć na dowolnym pojedynczym wykresie.

2. Wybierz wymiar, na którym chcesz podzielić wykresu: 

   ![metryki obrazu](./media/monitoring-metric-charts/0010.png)

   Teraz wykres zawiera teraz wiele wierszy, po jednym dla każdego segmentu wymiaru:

   ![metryki obrazu](./media/monitoring-metric-charts/0012.png)

3. Kliknij przycisk opuszczenie **selektor grupowania** go zamknąć.

   > [!NOTE]
   > Umożliwia filtrowanie i grupowanie w oparciu o ten sam wymiar Ukryj segmentów, które są nieodpowiednie dla danego scenariusza i była łatwiejsza do odczytania wykresy.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Jak przypiąć wykresów do pulpitów nawigacyjnych?

Po skonfigurowaniu wykresów, warto dodać do pulpitów nawigacyjnych, aby można go wyświetlić, prawdopodobnie w kontekście innych telemetrii monitorowania, lub udostępnienia swojemu zespołowi. 

Aby przypiąć wykres skonfigurowanego do pulpitu nawigacyjnego:

Po skonfigurowaniu wykresu, wybierz polecenie **akcje wykresu** menu w prawym rogu wykresu top, a następnie kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

   ![metryki obrazu](./media/monitoring-metric-charts/0013.png)

## <a name="next-steps"></a>Kolejne kroki

  Odczyt [Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) Aby dowiedzieć się więcej na temat najlepszych rozwiązań do tworzenia pulpitów nawigacyjnych informacje z możliwością działania za pomocą metryk.