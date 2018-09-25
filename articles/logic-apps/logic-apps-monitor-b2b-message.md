---
title: Monitorowanie komunikatów B2B i konfigurowanie logowania — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Monitoruj AS2, X 12 i EDIFACT wiadomości. Konfigurowanie rejestrowania diagnostycznego dla konta integracji w usługach Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 07/21/2017
ms.openlocfilehash: fd48dfaf7b863ab026da1758751509d9911ae01e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948013"
---
# <a name="monitor-b2b-messages-and-set-up-logging-for-integration-accounts-in-azure-logic-apps"></a>Monitorowanie komunikatów B2B i skonfigurować rejestrowanie dla konta integracji w usłudze Azure Logic Apps

Po skonfigurowaniu komunikacji B2B między dwiema uruchomionych procesów biznesowych lub aplikacji za pośrednictwem Twojego konta integracji tych jednostek mogą wymieniać komunikaty ze sobą. Aby upewnić się, ta komunikacja działa zgodnie z oczekiwaniami, możesz skonfigurować monitorowanie dla pola AS2-X12, i komunikatów EDIFACT, wraz z rejestrowania diagnostycznego dla konta integracji przez [usługi Azure Log Analytics](../log-analytics/log-analytics-overview.md) usługi. Ta usługa monitoruje środowiska chmurowe i środowisk, dzięki czemu możesz zachować ich dostępności i wydajności, lokalnych i zbiera także informacje o szczegóły środowiska uruchomieniowego i zdarzenia dla bardziej zaawansowane debugowanie. Możesz również [korzystanie z Twoich danych diagnostycznych z innymi usługami](#extend-diagnostic-data), takich jak Azure Storage i Azure Event Hubs.

## <a name="requirements"></a>Wymagania

* Aplikację logiki, która została skonfigurowana za pomocą rejestrowania diagnostycznego. Dowiedz się, [jak skonfigurować rejestrowanie dla tej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Po tego wymagania zostały spełnione, należy mieć obszar roboczy w usłudze Log Analytics. Należy używać tego samego obszaru roboczego usługi Log Analytics, podczas konfigurowania logowania na koncie integracji. Dowiedz się, jeśli nie masz obszaru roboczego usługi Log Analytics [jak utworzyć obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

* Konto integracji jest połączony z aplikacji logiki. Dowiedz się, [sposób tworzenia konta integracji z linkiem do aplikacji logiki](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Włącz diagnostykę logowania dla konta integracji

Można włączyć rejestrowanie albo bezpośrednio z Twojego konta integracji lub [za pośrednictwem usługi Azure Monitor](#azure-monitor-service). Usługa Azure Monitor zapewnia podstawowe monitorowanie za pomocą danych na poziomie infrastruktury. Dowiedz się więcej o [usługi Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Włącz diagnostykę rejestrowania bezpośrednio z Twojego konta integracji

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz swoje konto integracji. W obszarze **monitorowanie**, wybierz **dzienniki diagnostyczne** jak pokazano poniżej:

   ![Znajdź i wybierz swoje konto integracji, wybierz pozycję "Dzienniki diagnostyczne"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Po wybraniu konta integracji, następujące wartości zostaną zaznaczone automatycznie. Jeśli te wartości są prawidłowe, wybierz opcję **Włącz diagnostykę**. W przeciwnym razie wybierz wartości, które mają:

   1. W obszarze **subskrypcji**, wybierz subskrypcję platformy Azure, która jest używana z kontem integracji.
   2. W obszarze **grupy zasobów**, wybierz grupę zasobów, która jest używana z kontem integracji.
   3. W obszarze **typ zasobu**, wybierz opcję **kont integracji**. 
   4. W obszarze **zasobów**, wybierz swoje konto integracji. 
   5. Wybierz **Włącz diagnostykę**.

   ![Konfigurowanie diagnostyki dla konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. W obszarze **ustawień diagnostycznych**, a następnie **stan**, wybierz **na**.

   ![Włącz diagnostykę platformy Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Teraz wybierz obszar roboczy usługi Log Analytics i dane mają być używane do rejestrowania, jak pokazano:

   1. Wybierz **wysyłanie do usługi Log Analytics**. 
   2. W obszarze **usługi Log Analytics**, wybierz **Konfiguruj**. 
   3. W obszarze **obszarów roboczych pakietu OMS**, wybierz obszar roboczy usługi Log Analytics na potrzeby rejestrowania.
   4. W obszarze **dziennika**, wybierz opcję **IntegrationAccountTrackingEvents** kategorii.
   5. Wybierz pozycję **Zapisz**.

   ![Konfigurowanie usługi Log Analytics, co umożliwia wysyłanie danych diagnostycznych do dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Teraz [skonfigurować śledzenie wiadomości B2B w usłudze Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Włączanie rejestrowania diagnostycznego za pośrednictwem usługi Azure Monitor

1. W [witryny Azure portal](https://portal.azure.com), w menu głównym platformy Azure wybierz **Monitor**, **dzienniki diagnostyczne**. Następnie wybierz swoje konto integracji, jak pokazano poniżej:

   ![Wybierz pozycję "Monitoruj", "Dzienniki diagnostyczne", wybierz swoje konto integracji](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Po wybraniu konta integracji, następujące wartości zostaną zaznaczone automatycznie. Jeśli te wartości są prawidłowe, wybierz opcję **Włącz diagnostykę**. W przeciwnym razie wybierz wartości, które mają:

   1. W obszarze **subskrypcji**, wybierz subskrypcję platformy Azure, która jest używana z kontem integracji.
   2. W obszarze **grupy zasobów**, wybierz grupę zasobów, która jest używana z kontem integracji.
   3. W obszarze **typ zasobu**, wybierz opcję **kont integracji**.
   4. W obszarze **zasobów**, wybierz swoje konto integracji.
   5. Wybierz **Włącz diagnostykę**.

   ![Konfigurowanie diagnostyki dla konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. W obszarze **ustawień diagnostycznych**, wybierz **na**.

   ![Włącz diagnostykę platformy Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Teraz należy wybrać kategorię obszar roboczy i zdarzeń usługi Log Analytics do rejestrowania, jak pokazano:

   1. Wybierz **wysyłanie do usługi Log Analytics**. 
   2. W obszarze **usługi Log Analytics**, wybierz **Konfiguruj**. 
   3. W obszarze **obszarów roboczych pakietu OMS**, wybierz obszar roboczy usługi Log Analytics na potrzeby rejestrowania.
   4. W obszarze **dziennika**, wybierz opcję **IntegrationAccountTrackingEvents** kategorii.
   5. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   ![Konfigurowanie usługi Log Analytics, co umożliwia wysyłanie danych diagnostycznych do dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Teraz [skonfigurować śledzenie wiadomości B2B w usłudze Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozszerzanie, jak i, w którym można korzystać z danych diagnostycznych z innymi usługami

Wraz z usługi Azure Log Analytics można rozszerzyć, wykorzystania danych diagnostycznych z aplikacji logiki z innymi usługami platformy Azure, na przykład: 

* [Archiwum, które dzienniki diagnostyczne platformy Azure w usłudze Azure Storage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Dzienniki diagnostyczne platformy Azure Stream do usługi Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Następnie monitorowanie przy użyciu danych telemetrycznych i analitycznych z innych usług, takich jak get w czasie rzeczywistym [usługi Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [usługi Power BI](../log-analytics/log-analytics-powerbi.md). Na przykład:

* [Stream dane z usługi Event Hubs do usługi Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizuj dane przesyłane strumieniowo przy użyciu usługi Stream Analytics i utworzyć pulpit nawigacyjny analizy w czasie rzeczywistym w usłudze Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Oparte na opcje, które chcesz skonfigurować, upewnij się, że możesz pierwszy [Tworzenie konta usługi Azure storage](../storage/common/storage-create-storage-account.md) lub [Tworzenie Centrum zdarzeń Azure](../event-hubs/event-hubs-create.md). Następnie wybierz opcje dla której chcesz wysyłać dane diagnostyczne:

![Wysłać dane do Centrum konta lub zdarzenia usługi Azure storage](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Okresy przechowywania mają zastosowanie tylko wtedy, gdy użytkownik chce użyć konta magazynu.

## <a name="supported-tracking-schemas"></a>Obsługiwane schematy śledzenia

Platforma Azure obsługuje te śledzenie typów schematu, które ustalone schematów z wyjątkiem typu niestandardowego.

* [Schemat śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemat śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie komunikatów B2B w usłudze Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "komunikatów śledzenia B2B w pakiecie OMS")
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")

