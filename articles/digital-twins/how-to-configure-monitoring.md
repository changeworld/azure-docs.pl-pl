---
title: Jak skonfigurować monitorowanie — usługa Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się, jak skonfigurować opcje monitorowania i rejestrowania dla usługi Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: ed376a3f500f6d6af3d0eab7f98b68e856513600
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547082"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Jak skonfigurować monitorowanie w usłudze Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji obsługuje niezawodne rejestrowanie, monitorowanie i analizę. Deweloperzy rozwiązań mogą korzystać z dzienników Azure Monitor, dzienników diagnostycznych, rejestrowania aktywności i innych usług w celu obsługi złożonych potrzeb związanych z monitorowaniem aplikacji IoT. Opcje rejestrowania można łączyć z kwerendą lub wyświetlać rekordy w kilku usługach i zapewnić szczegółowe pokrycie rejestrowania w wielu usługach.

W tym artykule przedstawiono podsumowanie opcji rejestrowania i monitorowania oraz sposób ich łączenia w sposób specyficzny dla usługi Azure Digital bliźniaczych reprezentacji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Przegląd dzienników aktywności

[Dzienniki aktywności](../azure-monitor/platform/activity-logs-overview.md) platformy Azure zapewniają szybki wgląd w szczegółowe dane historii zdarzeń i operacji na poziomie subskrypcji dla każdego wystąpienia usługi platformy Azure.

Zdarzenia na poziomie subskrypcji obejmują:

* Tworzenie zasobów
* Usuwanie zasobu
* Tworzenie wpisów tajnych aplikacji
* Integrowanie z innymi usługami

Rejestrowanie aktywności usługi Azure Digital bliźniaczych reprezentacji jest domyślnie włączone i można je znaleźć w Azure Portal przez:

1. Wybieranie wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
1. Wybieranie **dziennika aktywności** w celu wyświetlenia panelu wyświetlania:

    [![dziennik aktywności](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

W przypadku zaawansowanego rejestrowania aktywności:

1. Wybierz opcję **dzienniki** , aby wyświetlić **Activity Log Analytics przegląd**:

    [![wybór](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Omówienie Activity Log Analytics** podsumowuje podstawowe dane dziennika aktywności:

    [Omówienie analizy dzienników aktywności ![]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Użyj **dzienników aktywności** , aby szybko uzyskać wgląd w zdarzenia na poziomie subskrypcji.

## <a name="enable-customer-diagnostic-logs"></a>Włączanie dzienników diagnostycznych klienta

[Ustawienia diagnostyczne](../azure-monitor/platform/resource-logs-overview.md) platformy Azure można ustawić dla każdego wystąpienia platformy Azure w celu uzupełnienia rejestrowania aktywności. Dzienniki aktywności odnoszą się do zdarzeń na poziomie subskrypcji, rejestrowanie diagnostyczne zapewnia wgląd w historię operacyjną samych zasobów.

Przykłady rejestrowania diagnostycznego obejmują:

* Czas wykonywania dla funkcji zdefiniowanej przez użytkownika
* Kod stanu odpowiedzi dla pomyślnego żądania interfejsu API
* Pobieranie klucza aplikacji z magazynu

Aby włączyć dzienniki diagnostyczne dla wystąpienia:

1. Wywołaj zasób w Azure Portal.
1. Wybierz pozycję **Ustawienia diagnostyczne**:

    [![ustawień diagnostycznych](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Wybierz pozycję **Włącz diagnostykę** , aby zbierać dane (jeśli nie zostały wcześniej włączone).
1. Wypełnij żądane pola i wybierz sposób i miejsce, w którym zostaną zapisane dane:

    [![ustawienia diagnostyki dwa](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Dzienniki diagnostyczne są często zapisywane przy użyciu [usługi Azure File Storage](../storage/files/storage-files-deployment-guide.md) i udostępniane w [dziennikach Azure monitor](../azure-monitor/log-query/get-started-portal.md). Można wybrać obie opcje.

>[!TIP]
>Korzystaj z **dzienników diagnostycznych** w celu uzyskiwania wglądu w operacje na zasobach.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitor i log Analytics

Aplikacje IoT dzielą różne zasoby, urządzenia, lokalizacje i dane w jeden. Szczegółowe informacje o poszczególnych elementach, usługach i składnikach ogólnej architektury aplikacji, ale także ujednolicone Omówienie są często wymagane do konserwacji i debugowania.

Azure Monitor obejmuje zaawansowane usługi log Analytics, które umożliwiają wyświetlanie i analizowanie źródeł rejestrowania w jednej lokalizacji. W związku z tym Azure Monitor jest wysoce przydatny do analizowania dzienników w zaawansowanych aplikacjach IoT.

Przykłady użycia obejmują:

* Wykonywanie zapytania dotyczącego wielu historii dzienników diagnostycznych
* Wyświetlanie dzienników dla kilku funkcji zdefiniowanych przez użytkownika
* Wyświetlanie dzienników dla co najmniej dwóch usług w określonym przedziale czasu

Pełne zapytania dzienników są udostępniane za poorednictwem [dzienników Azure monitor](../azure-monitor/log-query/log-query-overview.md). Aby skonfigurować te zaawansowane funkcje:

1. Wyszukaj **log Analytics** w Azure Portal.
1. Zobaczysz dostępne wystąpienia **obszaru roboczego log Analytics** . Wybierz jeden z nich i wybierz pozycję **dzienniki** do zapytania:

    [![usługi log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Jeśli nie masz jeszcze wystąpienia **obszaru roboczego log Analytics** , możesz utworzyć obszar roboczy, wybierając przycisk **Dodaj** :

    [![utworzyć pakietu OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Po zainicjowaniu wystąpienia **obszaru roboczego log Analytics** można używać zaawansowanych zapytań do znajdowania wpisów w wielu dziennikach lub wyszukiwanie przy użyciu określonych kryteriów przy użyciu funkcji **zarządzania dziennikami**:

   [![zarządzanie dziennikami](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Aby uzyskać więcej informacji na temat zaawansowanych operacji związanych z zapytaniami, zobacz [Rozpoczynanie pracy z zapytaniami](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> W przypadku wysyłania zdarzeń do **log Analytics obszaru roboczego** po raz pierwszy może wystąpić 5 minut.

Dzienniki Azure Monitor zapewniają także zaawansowane usługi błędów i powiadamiania o alertach, które można wyświetlić, wybierając opcję **Diagnozuj i rozwiąż problemy**:

   [Alerty ![i powiadomienia o błędach](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Użyj **log Analytics obszaru roboczego** , aby zbadać historie dziennika dla wielu funkcji, subskrypcji lub usług aplikacji.

## <a name="other-options"></a>Inne opcje

Usługa Azure Digital bliźniaczych reprezentacji obsługuje również rejestrowanie i inspekcję zabezpieczeń specyficzne dla aplikacji. Szczegółowe omówienie wszystkich opcji rejestrowania platformy Azure dostępnych w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji można znaleźć w artykule dotyczącym [inspekcji dzienników platformy Azure](../security/fundamentals/log-audit.md) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach aktywności](../azure-monitor/platform/activity-logs-overview.md)platformy Azure.

- Zapoznaj się z [omówieniem dzienników diagnostycznych](../azure-monitor/platform/resource-logs-overview.md), szczegółowe do ustawień diagnostycznych platformy Azure.

- Przeczytaj więcej na temat [dzienników Azure monitor](../azure-monitor/log-query/get-started-portal.md).
