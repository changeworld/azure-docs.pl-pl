---
title: Omówienie metryki w Monitorze systemu Azure
description: Dowiedz się, jak dostosować monitorowania wykresów na platformie Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: robb
ms.component: metrics
ms.openlocfilehash: 878ba004e7572ad78f574c15fd76c8868b281117
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262260"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Omówienie metryk w Microsoft Azure
Wszystkie usługi Azure śledzić kluczowych metryk, które umożliwiają monitorowanie kondycji, wydajności, dostępności i użyciu usług. Te metryki można wyświetlić w portalu Azure, i umożliwia także [interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) lub [zestawu .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) do programowy dostęp pełny zestaw metryki.

W przypadku niektórych usług może być konieczne Włącz diagnostykę, aby zobaczyć wszystkie metryki. Dla innych użytkowników, takich jak maszyny wirtualne otrzymasz podstawowego zestawu metryki, ale wymagają pełnego Ustaw metryki wysokiej częstotliwości. Zobacz [włączania monitorowania i diagnostyki](insights-how-to-use-diagnostics.md) Aby dowiedzieć się więcej.

## <a name="using-monitoring-charts"></a>Przy użyciu monitorowania wykresów
Można dowolną metryki wykresu je w dowolnym okresie możesz wybrać.

1. W [Azure Portal](https://portal.azure.com/), kliknij przycisk **Przeglądaj**, i następnie zasobu interesuje Cię monitorowania.
2. **Monitorowanie** sekcja zawiera najważniejsze metryki dla każdego z zasobów platformy Azure. Na przykład aplikacja sieci web ma **żądań i błędów**, gdzie jako maszynę wirtualną, musi **procent użycia procesora CPU** i **odczytu i zapisu dysku**: ![obiektyw monitorowanie](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Kliknięcie dowolnego wykresu, zostanie wyświetlona **Metryka** bloku. W bloku, oprócz wykresu jest jest tabela przedstawiająca agregacji metryki (na przykład średnią, minimalną i maksymalną przez zakres czasu, który wybrano). Poniżej są reguły alertów dla zasobu.
    ![Metryki bloku](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Aby dostosować wiersze, które są wyświetlane, kliknij przycisk **Edytuj** przycisk na wykresie lub, **Edytuj wykres** polecenia w bloku metryki.
5. W bloku Edycja kwerendy należy wykonać trzy czynności:
   
   * Zmień zakres czasu
   * Przełączanie między wygląd paska i wierszy
   * Wybierz inną metics ![edytowanie zapytań](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Zmienić zakres czasowy jest tak proste, jak wybranie innego zakresu (takie jak **ostatniej godziny**) i kliknięcie **zapisać** w dolnej części bloku. Można również wybrać **niestandardowe**, który umożliwia wybranie dowolnego okresu przez ostatnie 2 tygodnie. Na przykład można sprawdzić całego dwa tygodnie, lub, po prostu 1 godziny od wczoraj. Wpisz w polu tekstowym Wprowadź inną godzinę.
    ![Zakres czasu niestandardowych](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Poniżej zakres czasu kanał można wybrać dowolną liczbę metryki, aby wyświetlić na wykresie.
8. Po kliknięciu przycisku Zapisz zmiany zostaną zapisane dla tego zasobu. Na przykład jeśli masz dwie maszyny wirtualne, a w przypadku zmiany wykresu w jednym, nie wpłynie ono innych.

## <a name="creating-side-by-side-charts"></a>Tworzenie wykresów side-by-side
Zaawansowane dostosowania w portalu można dodawać wykresy dowolną liczbę można dowolnie.

1. W **...**  menu w górnej części bloku kliknij **Dodaj Kafelki**:  
    ![Dodawanie Menu](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Następnie możesz wybrać wybrać wykres z **galerii** po prawej stronie ekranu: ![galerii](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Jeśli nie widzisz metryka ma, można dodać jeden z istniejących metryki i **Edytuj** na wykresie, aby pokazać Metryka należy.

## <a name="monitoring-usage-quotas"></a>Monitorowanie wykorzystania przydziały
Większość metryki pokazują trendów w czasie, ale niektóre dane, takie jak przydziały do użycia, są w chwili informacji o progu.

W bloku zasobów, które mają przydziały również widoczna przydziały do użycia:

![Sposób użycia](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Tak jak w metryki, możesz użyć [interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) lub [zestawu .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) do programowy dostęp pełny zestaw przydziały do użycia.

## <a name="next-steps"></a>Kolejne kroki
* [Odbieranie powiadomień o alertach](insights-receive-alert-notifications.md) po każdej zmianie metryki przekracza próg.
* [Włączanie monitorowania i diagnostyki](insights-how-to-use-diagnostics.md) zbierania szczegółowych o dużej częstotliwości metryk usługi.
* [Automatyczne skalowanie liczby wystąpień](insights-how-to-scale.md) się upewnić, że usługa jest dostępna i elastyczny.
* [Monitorowanie wydajności aplikacji](../application-insights/app-insights-azure-web-apps.md) Jeśli chcesz zrozumieć, dokładnie tak jak wykonywanie kodu w chmurze.
* Użyj [usługi Application Insights dla aplikacji JavaScript i stron sieci web](../application-insights/app-insights-web-track-usage.md) uzyskać analytics klienta dotyczących przeglądarek, które strony sieci web.
* [Monitorowanie dostępności i czasu odpowiedzi dowolnej strony sieci web](../application-insights/app-insights-monitor-web-app-availability.md) z usługi Application Insights, można sprawdzić, czy strony nie działa.

