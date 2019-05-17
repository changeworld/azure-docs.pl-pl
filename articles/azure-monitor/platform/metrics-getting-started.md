---
title: Wprowadzenie do Eksploratora metryk platformy Azure
description: Dowiedz się, jak tworzenie pierwszego wykresu metryki z Eksploratorem metryk platformy Azure.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 3306e888970d99132d17d4ccf967f074302412ca
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595455"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Wprowadzenie do Eksploratora metryk platformy Azure

## <a name="where-do-i-start"></a>Gdzie rozpocząć
Eksplorator metryk usługi Azure Monitor to składnik systemu Microsoft Azure portal, która umożliwia wykreślania wykresy i wizualne korelowanie trendów i badanie wzrostów i spadki wartości metryk. Skorzystaj z Eksploratora metryk do badania kondycji i wykorzystania zasobów. Uruchom w następującej kolejności:

1. [Wybierz zasób i metryki](#create-your-first-metric-chart) zobaczyć podstawowego wykresu. Następnie [wybierz zakres czasu](#select-a-time-range) jest odpowiednie dla badania.

1. Spróbuj [stosowanie filtrów wymiaru i dzielenia](#apply-dimension-filters-and-splitting). Filtry i dzielenia pozwalają analizować, jakie segmenty metryki przyczyniają się do wartości ogólne metryki i zidentyfikować elementy odstające możliwe.

1. Użyj [Zaawansowane ustawienia](#advanced-chart-settings) dostosować wykres przed przypięciem do pulpitów nawigacyjnych. [Konfigurowanie alertów](alerts-metric-overview.md) Aby otrzymywać powiadomienia, gdy wartość metryki przekracza lub spadnie poniżej wartości progowej.

## <a name="create-your-first-metric-chart"></a>Tworzenie pierwszego wykresu metryki

Aby utworzyć wykres metryk z zasobu, grupy zasobów, subskrypcji lub widok usługi Azure Monitor, otwórz **metryki** kartę i wykonaj następujące czynności:

1. Za pomocą selektora zasobów, wybierz zasób, dla którego chcesz wyświetlać metryki. (Zasób jest wstępnie wybrane, jeśli otworzysz **metryki** w kontekście określonego zasobu).

    > ![Wybierz zasób](./media/metrics-getting-started/resource-picker.png)

2. Dla niektórych zasobów należy wybrać przestrzeni nazw. Przestrzeń nazw jest po prostu sposób organizowania metryk, aby można je łatwo odnaleźć. Na przykład konta magazynu mają oddzielne przestrzenie nazw do przechowywania plików, tabel, obiektów blob i kolejek metryki. Wiele typów zasobów mieć tylko jedną przestrzeń nazw.

3. Wybierz metrykę, z listy dostępnych metryk.

    > ![Wybierz metrykę](./media/metrics-getting-started/metric-picker.png)

4. Opcjonalnie można zmienić agregacji metryk. Na przykład możesz zechcieć wykresu do wyświetlania wartości minimalna, maksymalna lub średniej metryki.

> [!NOTE]
> Użyj **Dodaj metrykę** przycisku i powtórz te kroki, jeśli chcesz wyświetlić wiele metryk, w tym samym wykresie. W przypadku wielu wykresów w jednym widoku, wybierz **Dodaj wykres** przycisk u góry.

## <a name="select-a-time-range"></a>Wybierz zakres czasu

Domyślnie wykres przedstawia w ostatnich 24 godzin dane metryk. Użyj **selektor czasu** panelu, aby zmienić zakres czasu, powiększyć lub pomniejszyć na wykresie. 

![Panel zakresów czasu zmiany](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Zastosuj filtry wymiarów i dzielenia

[Filtrowanie](metrics-charts.md#apply-filters-to-charts) i [podział](metrics-charts.md#apply-splitting-to-a-chart) są zaawansowane narzędzia diagnostyczne dla metryk, który ma wymiarów. Te funkcje Pokaż różnych wpływ metryki segmentów ("wartości wymiaru") całkowitej wartości metryki i pozwalają zidentyfikować elementy odstające możliwe.

- **Filtrowanie** pozwala wybrać, wartości wymiarów, które znajdują się na wykresie. Na przykład możesz chcieć Pokaż liczba pomyślnych żądań, gdy wykresy *czas odpowiedzi serwera* metryki. Będziesz potrzebować zastosować filtr na *Powodzenie żądanie* wymiaru. 

- **Dzielenie** kontrolki, czy na wykresie kreślone są oddzielne wiersze dla każdej wartości wymiaru lub wartości są zagregowane w jednym wierszu. Można na przykład znajdują się jeden wiersz Średni czas odpowiedzi we wszystkich wystąpieniach serwera lub zobacz osobnych wierszach dla każdego serwera. Czy chcesz zastosować, dzielenie na *wystąpienie serwera* wymiaru, aby zobaczyć w osobnych wierszach.

Zobacz [przykładów wykresów](metric-chart-samples.md) , filtrowania i dzielenia zastosowano. W tym artykule opisano kroki zostały służące do konfigurowania na wykresach.

## <a name="advanced-chart-settings"></a>Ustawienia zaawansowane wykresu

Można dostosować styl wykresu, tytuł i zmodyfikuj ustawienia zaawansowane wykresu. Po zakończeniu wprowadzania dostosowań, należy ją przypiąć do pulpitu nawigacyjnego, aby zapisać swoją pracę. Można również skonfigurować alerty metryki. Postępuj zgodnie z [dokumentacji produktu](metrics-charts.md) Aby dowiedzieć się więcej na temat tych i innych zaawansowanych funkcji Eksploratora metryk usługi Azure Monitor.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o zaawansowanych funkcji Eksploratora metryk](metrics-charts.md)
* [Rozwiązywanie problemów z Eksploratora metryk](metrics-troubleshoot.md)
* [Wyświetlenie listy dostępnych metryk dla usług platformy Azure](metrics-supported.md)
* [Zobacz przykłady, skonfigurowanego wykresów](metric-chart-samples.md)
