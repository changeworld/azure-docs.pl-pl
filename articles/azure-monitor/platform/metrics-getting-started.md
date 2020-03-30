---
title: Wprowadzenie do Eksploratora metryk platformy Azure
description: Dowiedz się, jak utworzyć pierwszy wykres metrykowy za pomocą Eksploratora metryk platformy Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248777"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Wprowadzenie do Eksploratora metryk platformy Azure

## <a name="where-do-i-start"></a>Od czego zacząć
Eksplorator metryk usługi Azure Monitor jest składnikiem portalu Microsoft Azure, który umożliwia drukowanie wykresów, wizualnie korelowanie trendów i badanie skoków i spadków wartości metryk. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów. Rozpocznij w następującej kolejności:

1. [Wybierz zasób i metrykę,](#create-your-first-metric-chart) a zobaczysz podstawowy wykres. Następnie [wybierz zakres czasu,](#select-a-time-range) który jest odpowiedni dla twojego dochodzenia.

1. Spróbuj [zastosować filtry wymiarów i podzielić](#apply-dimension-filters-and-splitting). Filtry i podział umożliwiają analizowanie, które segmenty metryki przyczyniają się do ogólnej wartości metryki i identyfikowanie możliwych wartości odstania.

1. Użyj [ustawień zaawansowanych,](#advanced-chart-settings) aby dostosować wykres przed przypinaniem do pulpitów nawigacyjnych. [Skonfiguruj alerty,](alerts-metric-overview.md) aby otrzymywać powiadomienia, gdy wartość metryki przekracza lub spada poniżej progu.

## <a name="create-your-first-metric-chart"></a>Tworzenie pierwszego wykresu metrycznego

Aby utworzyć wykres metryki, z zasobu, grupy zasobów, subskrypcji lub widoku Usługi Azure Monitor, otwórz kartę **Metryki** i wykonaj następujące kroki:

1. Za pomocą selektora zasobów wybierz zasób, dla którego chcesz zobaczyć metryki. (Zasób jest wstępnie wybrany, jeśli zostały otwarte **metryki** w kontekście określonego zasobu).

    > ![Wybieranie zasobu](./media/metrics-getting-started/resource-picker.png)

2. W przypadku niektórych zasobów należy wybrać obszar nazw. Obszar nazw jest tylko sposobem organizowania metryk, dzięki czemu można je łatwo znaleźć. Na przykład konta magazynu mają oddzielne przestrzenie nazw do przechowywania plików, tabel, obiektów blob i kolejek metryki. Wiele typów zasobów ma tylko jedną przestrzeń nazw.

3. Wybierz metrykę z listy dostępnych danych.

    > ![Wybieranie metryki](./media/metrics-getting-started/metric-picker.png)

4. Opcjonalnie można zmienić agregację metryki. Na przykład możesz chcieć, aby wykres pokazywał wartości minimalne, maksymalne lub średnie metryki.

> [!NOTE]
> Użyj przycisku **Dodaj metrykę** i powtórz te kroki, jeśli chcesz wyświetlić wiele metryk wykreślonych na tym samym wykresie. W przypadku wielu wykresów w jednym widoku wybierz przycisk **Dodaj wykres** u góry.

## <a name="select-a-time-range"></a>Wybieranie zakresu czasu

Domyślnie wykres zawiera najnowsze dane 24 godzin metryki. Użyj panelu **selektora czasu,** aby zmienić zakres czasu, powiększyć lub pomniejszyć wykres. 

![Panel Zmienianie zakresu czasu](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Użyj **pędzla czasu,** aby zbadać interesujący obszar wykresu (kolec lub dip). Umieść wskaźnik myszy na początku obszaru, kliknij i przytrzymaj lewy przycisk myszy, przeciągnij na drugą stronę obszaru, a następnie zwolnij przycisk. Wykres powiększy ten zakres czasu. 

## <a name="apply-dimension-filters-and-splitting"></a>Stosowanie filtrów wymiarów i dzielenia

[Filtrowanie](metrics-charts.md#apply-filters-to-charts) i [dzielenie](metrics-charts.md#apply-splitting-to-a-chart) to zaawansowane narzędzia diagnostyczne dla metryk, które mają wymiary. Te funkcje pokazują, jak różne segmenty metryki ("wartości wymiarów") wpływają na ogólną wartość metryki i umożliwiają identyfikację możliwych wartości odstającej.

- **Filtrowanie** pozwala wybrać, które wartości wymiarów są uwzględniane na wykresie. Na przykład można wyświetlić pomyślne żądania podczas tworzenia wykresów metryki *czasu odpowiedzi serwera.* Należy zastosować filtr na sukces wymiaru *żądania.* 

- **Dzielenie** określa, czy wykres wyświetla oddzielne linie dla każdej wartości wymiaru, czy agreguje wartości w jedną linię. Na przykład można wyświetlić jeden wiersz dla średniego czasu odpowiedzi we wszystkich wystąpieniach serwera lub zobaczyć oddzielne wiersze dla każdego serwera. Aby wyświetlić oddzielne wiersze, należy zastosować podział w wymiarze *wystąpienia serwera.*

Zobacz [przykłady wykresów,](metric-chart-samples.md) na których zastosowano filtrowanie i dzielenie. W artykule przedstawiono kroki, które zostały użyte do skonfigurowania wykresów.

## <a name="advanced-chart-settings"></a>Zaawansowane ustawienia wykresu

Można dostosować styl wykresu, tytuł i zmodyfikować zaawansowane ustawienia wykresu. Po zakończeniu dostosowywania przypnij go do pulpitu nawigacyjnego, aby zapisać swoją pracę. Można również skonfigurować alerty metryk. Postępuj zgodnie [z dokumentacją produktu,](metrics-charts.md) aby dowiedzieć się więcej o tych i innych zaawansowanych funkcjach Eksploratora metryk usługi Azure Monitor.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o zaawansowanych funkcjach Eksploratora metryk](metrics-charts.md)
* [Rozwiązywanie problemów z Eksploratorem metryk](metrics-troubleshoot.md)
* [Zobacz listę dostępnych metryk dla usług platformy Azure](metrics-supported.md)
* [Zobacz przykłady skonfigurowanych wykresów](metric-chart-samples.md)
