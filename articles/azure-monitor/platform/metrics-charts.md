---
title: Zaawansowane funkcje Eksploratora metryk platformy Azure
description: Dowiedz się więcej o zaawansowanych funkcjach Eksploratora metryk usługi Azure Monitor
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2df1e0bb7d586edb13dc86e163f0e5728608d2a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371603"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Zaawansowane funkcje Eksploratora metryk platformy Azure

> [!NOTE]
> W tym artykule przyjęto założenie, że znasz podstawowe funkcje Eksploratora metryk. Jeśli jesteś nowym użytkownikiem i chcesz dowiedzieć się, jak utworzyć pierwszy wykres metrykowy, zobacz [Wprowadzenie do Eksploratora metryk platformy Azure](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metryki na platformie Azure

[Metryki w usłudze Azure Monitor](data-platform-metrics.md) to seria zmierzonych wartości i zliczeń, które są zbierane i przechowywane w czasie. Istnieją standardowe (lub "platforma") metryki i metryki niestandardowe. Standardowe metryki są dostarczane przez samą platformę Azure. Standardowe metryki odzwierciedlają statystyki kondycji i użycia zasobów platformy Azure. Podczas gdy metryki niestandardowe są wysyłane do platformy Azure przez aplikacje przy użyciu [interfejsu API usługi Application Insights dla zdarzeń niestandardowych i metryk,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)rozszerzenia [diagnostyki systemu Windows Azure (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)lub interfejsu [API REST usługi Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api)

## <a name="create-views-with-multiple-metrics-and-charts"></a>Tworzenie widoków z wieloma metrykami i wykresami

Można tworzyć wykresy, które kreślą wiele linii metryk lub pokazują wiele wykresów metrycznych jednocześnie. Ta funkcja umożliwia:

- skorelować powiązane metryki na tym samym wykresie, aby zobaczyć, jak jedna wartość jest powiązana z inną
- wyświetlanie danych z różnymi jednostkami miary w bliskiej odległości
- wizualnie agregować i porównywać metryki z wielu zasobów

Na przykład jeśli masz 5 kont magazynu i chcesz wiedzieć, ile całkowitego miejsca jest zużywane między nimi, można utworzyć (skumulowany) wykres warstwowy, który pokazuje indywidualną i sumę wszystkich wartości w określonych punktach w czasie.

### <a name="multiple-metrics-on-the-same-chart"></a>Wiele metryk na tym samym wykresie

Najpierw [utwórz nowy wykres](metrics-getting-started.md#create-your-first-metric-chart). Kliknij **pozycję Dodaj metrykę** i powtórz kroki, aby dodać kolejną metrykę na tym samym wykresie.

   > [!NOTE]
   > Zazwyczaj nie chcesz mieć metryki z różnych jednostek miary (tj. "milisekundy" i "kilobajty") lub znacznie różne skale na jednym wykresie. Zamiast tego należy rozważyć użycie wielu wykresów. Kliknij przycisk Dodaj wykres, aby utworzyć wiele wykresów w Eksploratorze metryk.

### <a name="multiple-charts"></a>Wiele wykresów

Kliknij **przycisk Dodaj wykres** i utwórz inny wykres z inną metryką.

### <a name="order-or-delete-multiple-charts"></a>Zamawianie lub usuwanie wielu wykresów

Aby zamówić lub usunąć wiele wykresów, kliknij symbol elipsy ( **...** ), aby otworzyć menu wykresu i wybrać odpowiednią pozycję menu **Przenieś w górę**, Przenieś w **dół**lub **Usuń**.

## <a name="apply-filters-to-charts"></a>Stosowanie filtrów do wykresów

Filtry można stosować do wykresów przedstawiających dane z wymiarami. Na przykład jeśli metryka "Liczba transakcji" ma wymiar "Typ odpowiedzi", który wskazuje, czy odpowiedź z transakcji powiodła się, czy nie powiodła się, filtrowanie w tym wymiarze wykreśliłoby linię wykresu tylko dla udanych (lub tylko nieudanych) transakcji. 

### <a name="to-add-a-filter"></a>Aby dodać filtr

1. Wybierz **dodaj filtr** nad wykresem

2. Wybierz wymiar (właściwość), który chcesz filtrować

   ![obraz metryki](./media/metrics-charts/00006.png)

3. Wybierz wartości wymiarów, które mają zostać uwzględnione podczas drukowania wykresu (w tym przykładzie pokazano odfiltrowanie udanych transakcji magazynu):

   ![obraz metryki](./media/metrics-charts/00007.png)

4. Po wybraniu wartości filtru kliknij z dala od selektora filtru, aby je zamknąć. Teraz wykres pokazuje, ile transakcji magazynu nie powiodło się:

   ![obraz metryki](./media/metrics-charts/00008.png)

5. Można powtórzyć kroki od 1 do 4, aby zastosować wiele filtrów do tych samych wykresów.



## <a name="apply-splitting-to-a-chart"></a>Stosowanie dzielenia do wykresu

Metrykę można podzielić według wymiarów, aby zwizualizować porównanie różnych segmentów metryki ze sobą i zidentyfikować segmenty odchyłe wymiaru.

### <a name="apply-splitting"></a>Stosowanie podziału

1. Kliknij **zastosuj podział** nad wykresem.
 
   > [!NOTE]
   > Dzielenie nie może być używane z wykresami, które mają wiele metryk. Ponadto do dowolnego pojedynczego wykresu można zastosować wiele filtrów, ale tylko jeden wymiar podziału.

2. Wybierz wymiar, na którym chcesz podzielić wykres na segmenty:

   ![obraz metryki](./media/metrics-charts/00010.png)

   Teraz wykres pokazuje teraz wiele linii, po jednym dla każdego segmentu wymiaru:

   ![obraz metryki](./media/metrics-charts/00012.png)

3. Kliknij od selektora **grupowania,** aby go zamknąć.

   > [!NOTE]
   > Użyj filtrowania i dzielenia w tym samym wymiarze, aby ukryć segmenty, które nie mają znaczenia dla scenariusza, i ułatwić odczyt wykresów.

## <a name="lock-boundaries-of-chart-y-axis"></a>Zablokuj granice osi wykresu y

Blokowanie zakresu osi y staje się ważne, gdy wykres pokazuje mniejsze wahania większych wartości. 

Na przykład gdy liczba pomyślnych żądań spadnie z 99,99% do 99,5%, może to oznaczać znaczne obniżenie jakości usług. Jednak zauważenie niewielkich wahań wartości liczbowych byłoby trudne lub nawet niemożliwe z domyślnych ustawień wykresu. W takim przypadku można zablokować najniższą granicę wykresu do 99%, co sprawi, że ten mały spadek będzie bardziej widoczny. 

Innym przykładem jest wahania w dostępnej pamięci, gdzie wartość technicznie nigdy nie osiągnie 0. Ustalenie zakresu do wyższej wartości może ułatwić wykrycie spadków dostępnej pamięci. 

Aby sterować zakresem osi y, użyj "..." wykresu, a następnie wybierz **polecenie Edytuj wykres,** aby uzyskać dostęp do zaawansowanych ustawień wykresu. Zmodyfikuj wartości w sekcji Zakres osi Y lub użyj przycisku **Auto,** aby przywrócić wartości domyślne.

![obraz metryki](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Blokowanie granic osi y dla wykresów, które śledzą różne liczby lub sumy w określonym okresie czasu (a zatem używają liczby, sumy, minimum lub maksymalnych agregacji) zwykle wymaga określenia stałego szczegółowości czasu, a nie polegania na automatycznych wartościach domyślnych. Jest to konieczne, ponieważ wartości na wykresach zmieniają się, gdy szczegółowość czasu jest automatycznie modyfikowana przez użytkownika, który zmienia rozmiar okna przeglądarki lub przechodzi z jednej rozdzielczości ekranu do drugiej. Wynikająca ze zmian w czasie wpływa na wygląd wykresu, unieważniając bieżący wybór zakresu osi y.

## <a name="change-colors-of-chart-lines"></a>Zmienianie kolorów linii wykresu

Po skonfigurowaniu wykresów linie wykresu są automatycznie przypisywane do koloru z domyślnej palety. Można zmienić te kolory.

Aby zmienić kolor linii wykresu, kliknij kolorowy pasek w legendzie odpowiadającej wykresowi. Zostanie otwarte okno dialogowe selektora kolorów. Użyj selektora kolorów, aby skonfigurować kolor linii.

Po skonfigurowaniu kolorów wykresu pozostaną one w ten sposób po przypięciu wykresu do pulpitu nawigacyjnego. W poniższej sekcji pokazano, jak przypiąć wykres.

> [!NOTE]
> Ze względu na ograniczenia naszego harmonogramu wydań i publikowania, zmiana kolorów linii wykresu tymczasowo wymaga podania [https://portal.azure.com/?feature.colorpicker=true](https://portal.azure.com/?feature.colorpicker=true)specjalnego parametru **?feature.colorpicker=true** podczas uruchamiania witryny Azure portal . To ograniczenie zostanie wkrótce usunięte. 

![obraz metryki](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Przypinanie wykresów do pulpitów nawigacyjnych

Po skonfigurowaniu wykresów można dodać go do pulpitów nawigacyjnych, aby można było wyświetlić go ponownie, ewentualnie w kontekście innych danych telemetrycznych monitorowania, lub udostępnić zespołowi.

Aby przypiąć skonfigurowany wykres do pulpitu nawigacyjnego:

Po skonfigurowaniu wykresu kliknij menu **Akcje wykresu** w prawym górnym rogu wykresu, a następnie kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

![obraz metryki](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Tworzenie reguł alertów

Można użyć kryteriów, które zostały ustawione do wizualizacji metryki jako podstawie reguły alertu opartego na metryki. Nowa reguła alertów będzie zawierać zasób docelowy, metrykę, dzielenie i filtrowanie wymiarów z wykresu. Te ustawienia będzie można później zmodyfikować w okienku tworzenia reguły alertu.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Aby utworzyć nową regułę alertu, kliknij pozycję **Nowa reguła alertu**

![Przycisk Nowa reguła alertu wyróżniony na czerwono](./media/metrics-charts/015.png)

Zostaniesz przesiąknięta do okienka tworzenia reguł alertów z podstawowymi wymiarami metryki z wykresu wstępnie wypełnionego, aby ułatwić generowanie niestandardowych reguł alertów.

![Tworzenie reguły alertu](./media/metrics-charts/016.png)

Zapoznaj się z tym [artykułem,](alerts-metric.md) aby dowiedzieć się więcej o konfigurowaniu alertów metryk.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Nie widzę żadnych danych na wykresie.*

* Filtry mają zastosowanie do wszystkich wykresów w okienku. Upewnij się, że podczas ustawiania ostrości na jednym wykresie nie ustawiono filtru, który wyklucza wszystkie dane na innym.

* Jeśli chcesz ustawić różne filtry na różnych wykresach, utwórz je w różnych ostrzach, zapisz je jako oddzielne ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego, aby można je było zobaczyć obok siebie.

* Jeśli posadymać wykres według właściwości, która nie jest zdefiniowana na metryki, to nie będzie nic na wykresie. Spróbuj wyczyścić segmentację (dzielenie) lub wybierz inną właściwość.

## <a name="next-steps"></a>Następne kroki

  Przeczytaj [artykuł Tworzenie niestandardowych pulpitów nawigacyjnych kluczowych wskaźników wydajności,](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących tworzenia pulpitów nawigacyjnych z metrykami.

