---
title: Jak skonfigurować monitorowanie — azure digital twins | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować opcje monitorowania i rejestrowania dla usługi Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511862"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Jak skonfigurować monitorowanie w usłudze Azure Digital Twins

Usługa Azure Digital Twins obsługuje niezawodne rejestrowanie, monitorowanie i analizy. Deweloperzy rozwiązań mogą używać dzienników usługi Azure Monitor, dzienników diagnostycznych, rejestrowania aktywności i innych usług do obsługi złożonych potrzeb monitorowania aplikacji IoT. Opcje rejestrowania można łączyć w celu wykonywania zapytań lub wyświetlania rekordów w kilku usługach oraz w celu zapewnienia szczegółowego rejestrowania dla wielu usług.

W tym artykule podsumowano opcje rejestrowania i monitorowania oraz sposób łączenia ich w sposób specyficzny dla usługi Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Przeglądanie dzienników aktywności

[Dzienniki aktywności](../azure-monitor/platform/platform-logs-overview.md) platformy Azure zapewniają szybkie wgląd w historię zdarzeń i operacji na poziomie subskrypcji dla każdego wystąpienia usługi platformy Azure.

Zdarzenia na poziomie subskrypcji obejmują:

* Tworzenie zasobów
* Usuwanie zasobów
* Tworzenie wpisów tajnych aplikacji
* Integracja z innymi usługami

Rejestrowanie aktywności dla usługi Azure Digital Twins jest domyślnie włączone i można je znaleźć za pośrednictwem witryny Azure portal przez:

1. Wybieranie wystąpienia usługi Azure Digital Twins.
1. Wybieranie **dziennika aktywności** w celu wyświetlenia panelu wyświetlacza:

    [![Dziennik aktywności](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

W przypadku zaawansowanego rejestrowania aktywności:

1. Wybierz opcję **Dzienniki,** aby wyświetlić **przegląd analizy dzienników aktywności:**

    [![Wybór](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Omówienie analizy dzienników aktywności** podsumowuje podstawowe dane dziennika aktywności:

    [![Omówienie analizy dziennika aktywności]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Użyj **dzienników aktywności,** aby uzyskać szybki wgląd w zdarzenia na poziomie subskrypcji.

## <a name="enable-customer-diagnostic-logs"></a>Włączanie dzienników diagnostycznych klientów

[Ustawienia diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) platformy Azure można ustawić dla każdego wystąpienia platformy Azure, aby uzupełnić rejestrowanie działań. Dzienniki aktywności dotyczą zdarzeń na poziomie subskrypcji, rejestrowanie diagnostyczne zapewnia wgląd w historię operacyjną samych zasobów.

Przykłady rejestrowania diagnostycznego obejmują:

* Czas wykonywania funkcji zdefiniowanej przez użytkownika
* Kod stanu odpowiedzi pomyślnego żądania interfejsu API
* Pobieranie klucza aplikacji z przechowalni

Aby włączyć dzienniki diagnostyczne dla wystąpienia:

1. Przynieś zasób w witrynie Azure portal.
1. Wybierz **ustawienia diagnostyczne:**

    [![Ustawienia diagnostyczne jeden](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Wybierz **włącz diagnostykę,** aby zbierać dane (jeśli nie były wcześniej włączone).
1. Wypełnij żądane pola i wybierz sposób i miejsce zapisywania danych:

    [![Ustawienia diagnostyczne dwa](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Dzienniki diagnostyczne są często zapisywane przy użyciu [usługi Azure File Storage](../storage/files/storage-files-deployment-guide.md) i udostępniane [dziennikom usługi Azure Monitor.](../azure-monitor/log-query/get-started-portal.md) Można wybrać obie opcje.

>[!TIP]
>Użyj **dzienników diagnostycznych,** aby uzyskać wgląd w operacje zasobów.

## <a name="azure-monitor-and-log-analytics"></a>Monitorowanie i analiza dzienników platformy Azure

Aplikacje IoT łączą różne zasoby, urządzenia, lokalizacje i dane w jeden. Rejestrowanie szczegółowe zawiera szczegółowe informacje na temat każdego określonego elementu, usługi lub składnika ogólnej architektury aplikacji, ale ujednolicony przegląd jest często wymagane do konserwacji i debugowania.

Usługa Azure Monitor zawiera zaawansowana usługa analizy dzienników, która umożliwia przeglądanie i analizowanie źródeł rejestrowania w jednej lokalizacji. Usługa Azure Monitor jest zatem bardzo przydatne do analizowania dzienników w zaawansowanych aplikacji IoT.

Przykłady zastosowania obejmują:

* Wykonywanie zapytań o wiele historii dzienników diagnostycznych
* Wyświetlanie dzienników dla kilku funkcji zdefiniowanych przez użytkownika
* Wyświetlanie dzienników dla dwóch lub więcej usług w określonym przedziale czasowym

Pełne wykonywanie zapytań dziennika odbywa się za pośrednictwem [dzienników usługi Azure Monitor.](../azure-monitor/log-query/log-query-overview.md) Aby skonfigurować te zaawansowane funkcje:

1. Wyszukaj **usługę Log Analytics** w witrynie Azure portal.
1. Zostaną wyświetlone dostępne wystąpienia **obszaru roboczego usługi Log Analytics.** Wybierz jeden z nich i wybierz pozycję Dzienniki do **kwerendy:**

    [![Analiza dzienników](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Jeśli nie masz jeszcze wystąpienia **obszaru roboczego usługi Log Analytics,** możesz utworzyć obszar roboczy, wybierając przycisk **Dodaj:**

    [![Tworzenie oms](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Po zainicjowaniu obsługi administracyjnej wystąpienia **obszaru roboczego usługi Log Analytics** można użyć zaawansowanych zapytań do znajdowania wpisów w dziennikach wielokrotności lub wyszukiwania przy użyciu określonych kryteriów przy użyciu **zarządzania dziennikami:**

   [![Zarządzanie dziennikami](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Aby uzyskać więcej informacji na temat zaawansowanych operacji kwerend, przeczytaj [wprowadzenie do zapytań](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Podczas wysyłania zdarzeń do **obszaru roboczego usługi Log Analytics** może wystąpić 5-minutowe opóźnienie.

Dzienniki usługi Azure Monitor zapewniają również zaawansowane usługi powiadamiania o błędach i alertach, które można wyświetlać, wybierając **opcję Diagnozuj i rozwiązuj problemy:**

   [![Powiadomienia o alertach i błędach](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Użyj **obszaru roboczego usługi Log Analytics,** aby zbadać historię dzienników dla wielu funkcji aplikacji, subskrypcji lub usług.

## <a name="other-options"></a>Inne opcje

Usługa Azure Digital Twins obsługuje również rejestrowanie specyficzne dla aplikacji i inspekcję zabezpieczeń. Aby uzyskać szczegółowe omówienie wszystkich opcji rejestrowania platformy Azure dostępnych dla wystąpienia usługi Azure Digital Twins, przeczytaj artykuł [inspekcji dziennika platformy Azure.](../security/fundamentals/log-audit.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach aktywności platformy](../azure-monitor/platform/platform-logs-overview.md)Azure .

- Zanurz się głębiej w ustawieniach diagnostycznych platformy Azure, czytając [przegląd dzienników diagnostycznych](../azure-monitor/platform/platform-logs-overview.md).

- Dowiedz się więcej o [dziennikach usługi Azure Monitor](../azure-monitor/log-query/get-started-portal.md).
