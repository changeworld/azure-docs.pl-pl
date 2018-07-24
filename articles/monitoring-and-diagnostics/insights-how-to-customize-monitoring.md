---
title: Przegląd metryk w usłudze Azure Monitor
description: Dowiedz się, jak dostosować wykresy monitorowania na platformie Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: robb
ms.component: metrics
ms.openlocfilehash: 44daf6461a062e75435ec6f70fbc3cf10327e799
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213048"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Przegląd metryk na platformie Microsoft Azure
Wszystkich usług platformy Azure śledzą kluczowe metryki, które umożliwiają monitorowanie kondycji, wydajności, dostępności i użycia usługi. Można wyświetlić te metryki w witrynie Azure portal i można również użyć [interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) lub [zestawu .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) do programowy dostęp do pełnego zestawu metryk.

W przypadku niektórych usług może być konieczne Włącz diagnostykę, aby zobaczyć wszystkie metryki. Dla innych użytkowników, takie jak maszyny wirtualne otrzymasz zestaw podstawowych metryk, ale należy włączyć pełnego ustawić metryki o wysokiej częstotliwości. Zobacz [Włączanie monitorowania i diagnostyki](insights-how-to-use-diagnostics.md) Aby dowiedzieć się więcej.

## <a name="using-monitoring-charts"></a>Korzystanie z wykresów monitorowania
Można wykresu dowolnej metryki je w przedziale czasu, możesz wybrać.

1. W [witryny Azure Portal](https://portal.azure.com/), kliknij przycisk **Przeglądaj**, a następnie zasobem interesuje Cię monitorowania.
2. **Monitorowanie** sekcja zawiera najważniejsze metryki dla poszczególnych zasobów platformy Azure. Na przykład aplikacja sieci web ma **żądania i błędy**, gdzie jako maszyna wirtualna może mieć **procent użycia procesora CPU** i **odczytu i zapisu na dysku**: ![obiektywu monitorowanie](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Kliknij dowolny wykres przedstawia **metryki** bloku. W bloku, oprócz wykres to jest tabela przedstawiająca agregacji metryk (takie jak średnia, minimalnej i maksymalnej w całym zakresie czasu, który został wybrany). Poniżej są reguły alertów dla zasobu.
    ![Blok metryki](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Aby dostosować wierszy, które są wyświetlane, kliknij przycisk **Edytuj** przycisku na wykresie, lub, **Edytuj wykres** polecenia w bloku metryk.
5. W bloku Edytuj zapytanie, może wykonać trzy czynności:
   
   * Zmień zakres czasu
   * Przełącz wygląd między pasek i linia
   * Wybierz inny metics ![Edytuj zapytanie](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Zmienić zakres czasowy jest bardzo proste — wystarczy wybrać inny zakres (takie jak **ostatniej godziny**) i kliknięcie **Zapisz** w dolnej części bloku. Można również wybrać **niestandardowe**, który umożliwia wybranie dowolnego okresu czasu w ciągu ostatnich 2 tygodni. Na przykład można sprawdzić całego ciągu dwóch tygodni, lub, po prostu za godzinę od wczoraj. Wpisz w polu tekstowym, aby wprowadzić inną godzinę.
    ![Niestandardowy zakres czasu](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Poniżej zakres czasu kanał możesz wybrać dowolną liczbę metryk, aby wyświetlić na wykresie.
8. Po kliknięciu przycisku zapisywania zmiany zostaną zapisane dla tego zasobu. Na przykład jeśli masz dwie maszyny wirtualne, a w przypadku zmiany wykresu w jednym, go nie ma wpływu na drugi.

## <a name="creating-side-by-side-charts"></a>Tworzenie wykresów side-by-side
Za pomocą zaawansowanych dostosowań w portalu można dodać tyle wykresy, jak chcesz.

1. W **...**  menu w górnej części bloku kliknij pozycję **Dodaj Kafelki**:  
    ![Dodawanie Menu](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Następnie można wybrać wykres z **galerii** po prawej stronie ekranu: ![galerii](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Jeśli nie widzisz metrykę należy zawsze możesz dodać jeden z wstępnie zdefiniowanych metryk i **Edytuj** wykres, aby wyświetlić wartość metryki należy.

## <a name="monitoring-usage-quotas"></a>Monitorowanie limity przydziału użycia
Większość metryk dowiesz się trendy w czasie, ale niektóre dane, takie jak przydziały użycia są w momencie informacji o progu.

Widać również limity przydziału użycia w bloku zasobów, które mają limity przydziału:

![Sposób użycia](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Tak samo, jak za pomocą metryk, można [interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) lub [zestawu .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) do programowy dostęp do pełnego zestawu przydziały użycia.

## <a name="next-steps"></a>Kolejne kroki
* [Otrzymywanie powiadomień o alertach](insights-receive-alert-notifications.md) zawsze, gdy Metryka przekracza próg.
* [Włączanie monitorowania i diagnostyki](insights-how-to-use-diagnostics.md) zbierać szczegółowe metryki o wysokiej częstotliwości w Twojej usłudze.
* [Automatyczne skalowanie liczby wystąpień](insights-how-to-scale.md) się upewnić, że usługa jest dostępna i działa prawidłowo.
* [Monitorowanie wydajności aplikacji](../application-insights/app-insights-azure-web-apps.md) Jeśli chcesz zrozumieć, dokładnie tak, jak Twój kod działa w chmurze.
* Użyj [usługi Application Insights dla aplikacji JavaScript i stron sieci web](../application-insights/app-insights-web-track-usage.md) można pobrać klienta analiz dotyczących przeglądarek, które strony sieci web.
* [Monitorowanie dostępności i czasu odpowiedzi dowolnej strony sieci web](../application-insights/app-insights-monitor-web-app-availability.md) za pomocą Application Insights, dzięki czemu można sprawdzić Jeśli strona nie działa.

