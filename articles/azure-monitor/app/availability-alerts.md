---
title: Konfigurowanie alertów o dostępności za pomocą usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Konfigurowanie testów sieci Web w usłudze Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 5af6aec2267384c37f664522d075bf26c632e7e9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382879"
---
# <a name="availability-alerts"></a>Alerty dostępności

Usługa [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) wysyła żądania sieci Web do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Może ostrzegać, jeśli aplikacja nie odpowiada lub jeśli reaguje zbyt wolno.

## <a name="enable-alerts"></a>Włączanie alertów

Alerty są teraz domyślnie włączane automatycznie, ale aby w pełni skonfigurować alert, musisz najpierw utworzyć test dostępności.

![Tworzenie środowiska](./media/availability-alerts/create-test.png)

> [!NOTE]
>  W [przypadku nowych ujednoliconych alertów](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)ważność reguły alertów i preferencje powiadomień z [grupami akcji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **muszą być** skonfigurowane w środowiskach alertów. Bez następujących kroków otrzymasz tylko powiadomienia w portalu.

1. Po zapisaniu testu dostępności, na karcie szczegółów kliknij na wielokropek przez test, który właśnie wykonałeś. Kliknij na "edytuj alert".

   ![Edytuj po zapisaniu](./media/availability-alerts/edit-alert.png)

2. Ustaw żądany poziom ważności, opis reguły i co najważniejsze — grupę akcji, która ma preferencje powiadomień, których chcesz użyć dla tej reguły alertu.

   ![Edytuj po zapisaniu](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Alerty o dostępności utworzone za pośrednictwem tego środowiska są oparte na stanie. Oznacza to, że po spełnieniu kryteriów alertu generowany jest pojedynczy alert, gdy witryna zostanie wykryta jako niedostępna. Jeśli witryna jest nadal w dół przy następnej oceny kryteriów alertu nie spowoduje to wygenerowania nowego alertu. Jeśli więc witryna była wyłączana przez godzinę i został skonfigurowany alert e-mail, otrzymasz wiadomość e-mail tylko wtedy, gdy witryna ujrzała witrynę, a następnie wiadomość e-mail, gdy witryna została ponownie skonfigurowana. Nie będą otrzymywać ciągłych alertów przypominających, że witryna jest nadal niedostępna.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alert w lokalizacjach X poza Y, które zgłaszają błędy

Reguła alertu X out of Y jest domyślnie włączona w [nowym ujednoliconym teście alertów](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)podczas tworzenia nowego testu dostępności. Możesz zrezygnować, wybierając opcję "klasyczny" lub wyłączając regułę alertu.

> [!NOTE]
> Skonfiguruj grupy akcji do odbierania powiadomień, gdy alert wyzwala, wykonując powyższe kroki. Bez tego kroku otrzymasz powiadomienia w portalu tylko wtedy, gdy reguła zostanie wyzwyczajona.
>

### <a name="alert-on-availability-metrics"></a>Alert o danych dostępności

Korzystając z [nowych ujednoliconych alertów,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)można również ostrzegać o zagregowanej dostępności segmentów i metrykach czasu trwania testu:

1. Wybierz zasób usługi Application Insights w metryki i wybierz metrykę dostępności:

    ![Wybór wskaźników dostępności](./media/availability-alerts/select-metric.png)

2. Opcja Skonfiguruj alerty z menu spowoduje, że przejdziesz do nowego środowiska, w którym możesz wybrać określone testy lub lokalizacje, aby skonfigurować regułę alertów. W tym miejscu można również skonfigurować grupy akcji dla tej reguły alertów.

### <a name="alert-on-custom-analytics-queries"></a>Alerty dotyczące zapytań do analizy niestandardowej

Korzystając z [nowych ujednoliconych alertów,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)można ostrzegać o [niestandardowych kwerendach dziennika](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Za pomocą zapytań niestandardowych można alertować o dowolnym stanie, który pomaga uzyskać najbardziej niezawodny sygnał problemów z dostępnością. Ma to również zastosowanie, jeśli wysyłasz niestandardowe wyniki dostępności przy użyciu trackavailability SDK.

> [!Tip]
> Metryki dotyczące danych o dostępności obejmują wszelkie niestandardowe wyniki dostępności, które możesz przesłać, dzwoniąc do naszego SDK TrackAvailability. Alerty dotyczące obsługi metryk można używać do alertów o niestandardowych wynikach dostępności.
>

## <a name="automate-alerts"></a>Automatyzacja alertów

Aby zautomatyzować ten proces za pomocą szablonów usługi Azure Resource Manager, zapoznaj się [z dokumentacją szablonu Programu Utwórz metrykę z menedżerem zasobów.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Dedykowany [artykuł dotyczący rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Następne kroki

* [Wieloetapowe testy sieci Web](availability-multistep.md)
* [Testy sieci web url ping](monitor-web-app-availability.md)
