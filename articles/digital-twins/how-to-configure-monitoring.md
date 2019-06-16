---
title: Jak skonfigurować monitorowanie w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować monitorowanie w reprezentacji urządzeń cyfrowych platformy Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 5dc2587a0c127106d5afb41e20eca43919065f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118784"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Jak skonfigurować monitorowanie w reprezentacji urządzeń cyfrowych platformy Azure

Twins cyfrowych platformy Azure obsługuje niezawodne rejestrowanie, monitorowanie i analizy. Rozwiązania deweloperzy mogą używać usługi Azure Monitor dzienników, dzienniki diagnostyczne, rejestrowanie aktywności i innych usług, do obsługi złożonych potrzeb monitorowania aplikacji IoT. Opcje rejestrowania można łączyć z zapytania lub wyświetla rekordy w kilku usługach oraz zapewniają szczegółowe rejestrowanie pokrycie dla wielu usług.

Ten artykuł zawiera podsumowanie, rejestrowania i monitorowania opcje i sposób łączenia ich w sposoby określonych Twins cyfrowych platformy Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Przejrzyj dzienniki aktywności

Azure [dzienników aktywności](../azure-monitor/platform/activity-logs-overview.md) zapewniają szybki wgląd w poziom subskrypcji zdarzeń i operacji historii dla każdego wystąpienia usług platformy Azure.

Zdarzenia na poziomie subskrypcji, obejmują:

* Tworzenie zasobów
* Usuwanie zasobu
* Tworzenie kluczy tajnych aplikacji
* Integrowanie z innymi usługami

Rejestrowanie aktywności bliźniaki cyfrowych platformy Azure jest domyślnie włączona i można znaleźć w witrynie Azure portal przez:

1. Wybieranie wystąpienia Twins cyfrowych platformy Azure.
1. Wybieranie **dziennika aktywności** Aby wyświetlić ekran w panelu:

    [![Dziennik aktywności](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Aby uzyskać Zaawansowane rejestrowanie aktywności:

1. Wybierz **dzienniki** opcję, aby wyświetlić **przegląd działań usługi Log Analytics**:

    [![Wybór](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Przegląd działań usługi Log Analytics** podsumowuje dane dzienników aktywności podstawowe:

    [![Przegląd działań usługi log analytics]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Użyj **dzienników aktywności** dla szybki wgląd w zdarzenia na poziomie subskrypcji.

## <a name="enable-customer-diagnostic-logs"></a>Włączanie dzienników diagnostycznych klienta

Azure [ustawień diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md) można ustawić dla każdego wystąpienia platformy Azure uzupełnić rejestrowania aktywności. Gdy dzienniki aktywności odnoszą się do poziomu subskrypcji zdarzeń, rejestrowanie diagnostyczne zapewnia wgląd w historię operacyjnej samych zasobów.

Przykłady rejestrowania diagnostycznego:

* Czas wykonywania dla funkcji zdefiniowanej przez użytkownika
* Kod stanu odpowiedzi pomyślne żądania interfejsu API
* Pobieranie klucza aplikacji z magazynu

Aby włączyć dzienniki diagnostyczne na potrzeby wystąpienie:

1. Przenieś zasób w witrynie Azure portal.
1. Wybierz **ustawień diagnostycznych**:

    [![Ustawienia diagnostyczne jeden](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Wybierz **Włącz diagnostykę** do zbierania danych (jeśli wcześniej nie jest włączona).
1. Wprowadź żądane pola i wybierz, jak i, w którym zostaną zapisane dane:

    [![Ustawienia diagnostyczne dwóch](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Dzienniki diagnostyczne często są zapisywane przy użyciu [usługi Azure File Storage](../storage/files/storage-files-deployment-guide.md) i udostępniane [dzienniki usługi Azure Monitor](../azure-monitor/log-query/get-started-portal.md). Można wybrać obu opcji.

>[!TIP]
>Użyj **dzienniki diagnostyczne** uzyskać wgląd w operacje zasobów.

## <a name="azure-monitor-and-log-analytics"></a>Monitorowanie i rejestrowanie funkcja analizy usługi Azure

Aplikacje IoT łączenia różnych zasobów, urządzenia, lokalizacji i dane w jedną. Szczegółowe rejestrowanie udostępnia szczegółowe informacje na temat każdej konkretne, usługi lub składnik architektury cała aplikacja, ale Przegląd ujednoliconego często jest wymagany do obsługi i debugowania.

Usługa Azure Monitor obejmuje zaawansowane usługi log analytics, która umożliwia rejestrowanie źródeł, aby wyświetlać i analizować w jednej lokalizacji. Usługa Azure Monitor, dlatego jest bardzo przydatna w celu analizowania dzienników w zaawansowanych aplikacji IoT.

Przykłady użycia:

* Zapytania wielu historie dziennik diagnostyczny
* Wyświetlanie dzienników dla kilku funkcji zdefiniowanych przez użytkownika
* Wyświetlanie dzienników dla dwóch lub więcej usług w określonym przedziale czasu

Pełny dziennik zapytań jest oferowana w ramach [dzienniki usługi Azure Monitor](../azure-monitor/log-query/log-query-overview.md). Aby skonfigurować te zaawansowane funkcje:

1. Wyszukaj **usługi Log Analytics** w witrynie Azure portal.
1. Zostanie wyświetlony dostępnych **obszaru roboczego usługi Log Analytics** wystąpień. Wybierz jedną, a następnie wybierz pozycję **dzienniki** zapytania:

    [![Usługa log analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Jeśli nie masz jeszcze **obszaru roboczego usługi Log Analytics** wystąpienia, można utworzyć obszaru roboczego, wybierając **Dodaj** przycisku:

    [![Tworzenie pakietu OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Raz swoje **obszaru roboczego usługi Log Analytics** aprowizowano wystąpienie, możesz użyć zaawansowanych zapytań, aby odnaleźć wpisów w dziennikach wielokrotności lub wyszukiwanie przy użyciu kryteriów określonych za pomocą **Zarządzanie dziennikami**:

   [![Zarządzanie dziennikami](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Aby uzyskać więcej informacji o operacjach zaawansowanych zapytań, zobacz [wprowadzenie do zapytań](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Może wystąpić opóźnienie 5 minut, podczas wysyłania zdarzeń do **obszaru roboczego usługi Log Analytics** po raz pierwszy.

Dzienniki monitora platformy Azure udostępniają zaawansowane błędów i usług powiadomień o alertach, które można wyświetlić, wybierając **diagnozowanie i rozwiązywanie problemów**:

   [![Alert i błąd powiadomienia](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Użyj **obszaru roboczego usługi Log Analytics** do historii dziennika zapytań dla wielu aplikacji funkcji, subskrypcji lub usług.

## <a name="other-options"></a>Inne opcje

Twins cyfrowych platformy Azure obsługuje również specyficzne dla aplikacji, rejestrowanie i przeprowadzanie inspekcji bezpieczeństwa. Aby uzyskać szczegółowe omówienie do Twojego wystąpienia usługi Azure cyfrowego bliźniaczych reprezentacji wszystkie opcje rejestrowania platformy Azure, zobacz [inspekcji usługi Azure log](../security/azure-log-audit.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o usłudze Azure [dzienników aktywności](../azure-monitor/platform/activity-logs-overview.md).

- Dowiedz się więcej na konfiguracji ustawień diagnostyki platformy Azure, czytając [Przegląd dzienników diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md).

- Przeczytaj więcej na temat [dzienniki usługi Azure Monitor](../azure-monitor/log-query/get-started-portal.md).
