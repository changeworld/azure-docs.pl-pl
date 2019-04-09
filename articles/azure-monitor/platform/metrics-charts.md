---
title: Zaawansowane funkcje Eksploratora metryk usługi Azure
description: Dowiedz się więcej o zaawansowanych funkcji Eksploratora metryk usługi Azure Monitor
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 67e4281b24a7489cf202d82bdddbe99992aac095
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271683"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Zaawansowane funkcje Eksploratora metryk usługi Azure

> [!NOTE]
> W tym artykule założono, że znasz podstawowe funkcje Eksploratora metryk. Jeśli dla nowych użytkowników i chcesz dowiedzieć się, jak tworzenie pierwszego wykresu metryk, zobacz [wprowadzenie do Eksploratora metryk usługi Azure](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metryki na platformie Azure

[Metryki w usłudze Azure Monitor](data-platform-metrics.md) serie mierzonych i liczniki, które są zbierane i przechowywane wraz z upływem czasu. Brak metryk standardowa (lub "platforma") i metryki niestandardowe. Metryki standardowe są dostarczane przez samą platformę Azure. Standardowa metryki odzwierciedlają statystyki użycia i kondycji zasobów platformy Azure. Natomiast metryki niestandardowe są wysyłane do platformy Azure przy użyciu aplikacji przy użyciu [interfejsu API usługi Application Insights dla niestandardowych zdarzeń i metryk](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [rozszerzenia diagnostyki Azure Windows (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), lub [platformy Azure Monitorowanie interfejsu API REST](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Tworzenie widoków z wieloma metryki i wykresy

Możesz tworzyć wykresy, które wykreślenia w wielu wierszach metryk lub Pokaż jednocześnie wiele wykresów metryki. Ta funkcja umożliwia:

- korelowanie powiązane metryki na tym samym wykresie, aby zobaczyć, jak jedna wartość jest powiązany z innym
- Wyświetlanie metryk z różnych jednostek miary w bliskim sąsiedztwie
- wizualnie agregacji i Porównaj metryki z wielu zasobów

Na przykład jeśli masz 5 kont magazynu, i chcesz wiedzieć, ile całkowita ilość miejsca jest używane między nimi, można utworzyć wykres warstwowy (skumulowane), który pokazuje poszczególne i sumę wszystkich wartości w określonym punkcie w czasie.

### <a name="multiple-metrics-on-the-same-chart"></a>Wiele metryk na tym samym wykresie

Po pierwsze, [Utwórz nowy wykres](metrics-getting-started.md#create-your-first-metric-chart). Kliknij przycisk **Dodaj metrykę** i powtórz kroki Aby dodać kolejną metrykę na tym samym wykresie.

   > [!NOTE]
   > Zwykle nie chcesz mieć metryki różne jednostki miary (np. "milisekund" i "kilobajtów") lub z znacząco odmiennych skalowania na jeden wykres. Zamiast tego należy wziąć pod uwagę przy użyciu wielu wykresów. Kliknij przycisk Dodaj wykres, aby utworzyć wiele wykresów w Eksploratorze metryk.

### <a name="multiple-charts"></a>Wiele wykresów

Kliknij przycisk **Dodaj wykres** i utwórz inny wykres z inną metrykę.

### <a name="order-or-delete-multiple-charts"></a>Kolejność lub usunąć wiele wykresów

Kolejność lub usunąć wiele wykresów, kliknij wielokropek ( **...**  ) symbolu, aby otworzyć menu wykresu, a następnie wybierz element menu odpowiednie **Przenieś w górę**, **Przenieś w dół**, lub **Usuń**.

## <a name="apply-filters-to-charts"></a>Zastosuj filtry do wykresów

Filtry można stosować do wykresów przedstawiających metryk z wymiarami. Na przykład, jeśli metryki "Liczba transakcji" ma wymiar, "Typ odpowiedzi", który wskazuje, czy odpowiedź z transakcji zakończyło się pomyślnie lub nie powiodło się, następnie filtrowanie tego wymiaru będzie wykreślanie wiersz wykresów dla tylko pomyślne (lub tylko nie powiodło się) transakcji. 

### <a name="to-add-a-filter"></a>Aby dodać filtr

1. Wybierz **Dodaj filtr** powyżej wykresu

2. Wybierz, który wymiar (właściwość), do których chcesz filtrować

   ![metryki obrazu](./media/metrics-charts/00006.png)

3. Wybierz wartości wymiarów, które mają zostać uwzględnione podczas wykreślania wykresu (w tym przykładzie przedstawiono filtrowanie transakcje magazynowe pomyślnych):

   ![metryki obrazu](./media/metrics-charts/00007.png)

4. Po wybraniu wartości filtru, kliknij poza selektor filtrów, aby je zamknąć. Teraz wykres pokazuje, ile transakcji magazynu nie powiodło się:

   ![metryki obrazu](./media/metrics-charts/00008.png)

5. Możesz powtórzyć kroki 1 – 4, aby zastosować wiele filtrów do tych samych wykresów.



## <a name="apply-splitting-to-a-chart"></a>Zastosuj rozdzielenie do wykresu

Podziel metryki według wymiaru, aby zwizualizować segmentów jak różne metryki porównania między nimi i zidentyfikować odległe mniejsze segmenty wymiaru.

### <a name="apply-splitting"></a>Zastosuj rozdzielenie

1. Kliknij pozycję **zastosować podział** powyżej wykresu.
 
   > [!NOTE]
   > Dzielenie nie można używać z wykresów, które mają wiele metryk. Ponadto można mieć wiele filtrów, ale tylko jeden wymiar podziału stosowane do dowolnego pojedynczego wykresu.

2. Wybierz wymiar, na którym chcesz podzielić wykresu:

   ![metryki obrazu](./media/metrics-charts/00010.png)

   Teraz wykres zawiera teraz wiele wierszy, po jednym dla każdego segmentu wymiaru:

   ![metryki obrazu](./media/metrics-charts/00012.png)

3. Kliknij przycisk opuszczenie **selektor grupowania** go zamknąć.

   > [!NOTE]
   > Aby ukryć segmentów, które są nieodpowiednie dla danego scenariusza i była łatwiejsza do odczytania wykresy, należy użyć filtrowanie i rozdzielenie tego samego wymiaru.

## <a name="lock-boundaries-of-chart-y-axis"></a>Granice blokady osi y wykresu

Blokowanie zakres osi y staje się ważne, gdy na wykresie widać mniejszych wahania większe wartości. 

Na przykład gdy liczba pomyślnych żądań rozwinięta z dostępność przez 99,99% 99,5%, może reprezentować znaczny spadek jakości usług. Jednakże obserwowanie fluktuacje mała wartość liczbowa może być trudne lub niemożliwe nawet z domyślnych ustawień wykresu. W takim przypadku można zablokować najniższy granic wykresu do 99%, co spowoduje, że ten mały spadek bardziej oczywisty. 

Innym przykładem jest fluktuacje w dostępnej pamięci, gdy wartość będzie z technicznego punktu widzenia nigdy nie dotrzeć do 0. Ustalania zakresu na wartość większą może ułatwić spadnie w dostępnej pamięci na ekranie. 

Aby kontrolować zakres osi y, użyj "..." menu wykresu, a następnie wybierz **Edytuj wykres** dostęp do zaawansowanych ustawień wykresu. Zmodyfikuj wartości w sekcji zakres osi y lub użyj **automatycznie** przycisk, aby przywrócić ustawienia domyślne.

![metryki obrazu](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Blokowanie granice osi y wykresy, które śledzą różne liczby lub sumuje w przedziale czasu (i dlatego liczba używanych, sum, minimalnych i maksymalnych agregacji) zwykle wymagają określania stopnia szczegółowości stały czas, a nie opierając się na automatyczne ustawienia domyślne. Jest to konieczne, ponieważ wartości na wykresach zmianie, gdy stopień szczegółowości czasu automatycznie zostanie zmodyfikowany przez użytkownika zmiany rozmiaru okna przeglądarki lub ruch rozdzielczość ekranu co do innego. Wartość wynikowa zmiana efekty stopień szczegółowości czasu wygląd wykresu, powodując unieważnienie bieżące zaznaczenie zakresu osi y.

## <a name="pin-charts-to-dashboards"></a>Przypnij wykresy do pulpitów nawigacyjnych

Po skonfigurowaniu wykresów, warto dodać do pulpitów nawigacyjnych, aby można go wyświetlić, prawdopodobnie w kontekście innych telemetrii monitorowania, lub udostępnienia swojemu zespołowi.

Aby przypiąć wykres skonfigurowanego do pulpitu nawigacyjnego:

Po skonfigurowaniu wykresu, wybierz polecenie **akcje wykresu** menu w prawym rogu wykresu top, a następnie kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

![metryki obrazu](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Tworzenie reguł alertów

Możesz użyć kryteriów, ustawionych w celu wizualizacji metryk, jako podstawę metryki na podstawie reguły alertu. Nowa reguła alertu będzie zawierać Twojego zasobu docelowego, metryki, dzielenie i wymiary filtru z wykresu. Będzie można zmodyfikować te ustawienia później na panelu Tworzenie reguły alertu.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Aby utworzyć nową regułę alertu, kliknij przycisk **reguły nowy Alert**

![Nowy przycisk reguły alertu wyróżniony czerwonym kolorem](./media/metrics-charts/015.png)

Możesz zostaną pobrane do okienka tworzenia reguły alertu z bazowego wymiarami metryk z wykresu wstępnie wypełniane, aby ułatwić Generowanie niestandardowych reguł alertów.

![Utwórz regułę alertu](./media/metrics-charts/016.png)

Zapoznaj się z tą [artykułu](alerts-metric.md) Aby dowiedzieć się więcej o konfigurowaniu alertów dotyczących metryk.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Nie widzę żadnych danych na wykresie.*

* Filtry mają zastosowanie do wszystkich schematów w okienku. Upewnij się, że podczas, gdy są najbardziej istotne na jeden wykres, nie został ustawiony filtr, który nie obejmuje wszystkich danych na innym.

* Jeśli chcesz ustawić różne filtry na różnych wykresów, należy je utworzyć w różnych bloków, zapisać je jako osobne Ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego tak, aby można je wyświetlić obok siebie.

* Jeśli wykres pozwala na segmentację według właściwości, która nie jest zdefiniowana w metryki, będą nic na wykresie. Spróbuj wyczyścić segmentacji (dzielenie), lub wybierz inną właściwość.

## <a name="next-steps"></a>Kolejne kroki

  Odczyt [Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) Aby dowiedzieć się więcej na temat najlepszych rozwiązań do tworzenia pulpitów nawigacyjnych informacje z możliwością działania za pomocą metryk.

