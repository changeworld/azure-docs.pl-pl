---
title: Monitorowanie transakcji B2B i skonfigurować rejestrowanie - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Monitor AS2, X 12 i EDIFACT wiadomości, uruchomić rejestrowania diagnostyki dla Twojego konta integracji
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 6afab12b9e2d6e8686ecbc95be9743afbe70d98c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Monitorowanie i konfigurowanie rejestrowania diagnostyki dla komunikacji B2B w konta integracji

Po skonfigurowaniu B2B komunikacji między dwiema uruchomionych procesów biznesowych lub aplikacji za pośrednictwem konta integracji tych jednostek mogą wymieniać komunikaty ze sobą. Aby potwierdzić tę komunikację działa zgodnie z oczekiwaniami, można skonfigurować monitorowanie AS2, X12, i EDIFACT komunikaty, wraz z rejestrowania diagnostyki dla Twojego konta integracji za pośrednictwem [Azure Log Analytics](../log-analytics/log-analytics-overview.md) usługi. Ta usługa monitoruje chmury i lokalnych środowiskach, pomaga zachować ich dostępności i wydajności i zbiera również szczegóły środowiska uruchomieniowego i zdarzeń dla bardziej zaawansowane funkcje debugowania. Możesz również [użyć danych diagnostycznych z innymi usługami](#extend-diagnostic-data), takie jak usługi Azure Storage i Azure Event Hubs.

## <a name="requirements"></a>Wymagania

* Aplikację logiki, który został skonfigurowany z rejestrowania diagnostyki. Dowiedz się [jak skonfigurować rejestrowanie dla danej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Po tego wymagania zostały spełnione, powinien mieć obszar roboczy w analizy dzienników. Po skonfigurowaniu rejestrowania dla Twojego konta integracji, należy używać tego samego obszaru roboczego analizy dzienników. Dowiedz się, jeśli nie masz obszaru roboczego analizy dzienników [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md).

* Konta integracji połączonego z aplikacji logiki. Dowiedz się [sposobu tworzenia konta integracji z łączem do aplikacji logiki](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Włącz diagnostykę rejestrowania dla Twojego konta integracji

Można włączyć rejestrowanie albo bezpośrednio z Twojego konta integracji lub [za pośrednictwem usługi Azure Monitor](#azure-monitor-service). Azure Monitor udostępnia podstawowe monitorowanie za pomocą danych na poziomie infrastruktury. Dowiedz się więcej o [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Włącz diagnostykę, rejestrowanie bezpośrednio z Twojego konta integracji

1. W [portalu Azure](https://portal.azure.com), Znajdź i wybierz konto integracji. W obszarze **monitorowanie**, wybierz **dzienników diagnostycznych** w sposób pokazany poniżej:

   ![Znajdź i wybierz konto integracji, wybierz polecenie "Dzienniki diagnostyczne"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Po wybraniu konta integracji następujące wartości zostaną zaznaczone automatycznie. Jeśli te wartości są prawidłowe, wybierz **Włącz diagnostykę**. W przeciwnym razie wybierz wartości, które mają:

   1. W obszarze **subskrypcji**, wybierz subskrypcję platformy Azure korzystające z Twoim kontem integracji.
   2. W obszarze **grupy zasobów**, wybierz grupę zasobów, korzystających z Twoim kontem integracji.
   3. W obszarze **typu zasobu**, wybierz pozycję **konta integracji**. 
   4. W obszarze **zasobów**, wybierz konto integracji. 
   5. Wybierz **Włącz diagnostykę**.

   ![Ustawienia diagnostyki dla Twojego konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. W obszarze **ustawień diagnostycznych**, a następnie **stan**, wybierz **na**.

   ![Włącz diagnostykę Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Teraz wybierz obszar roboczy analizy dzienników i danych na potrzeby rejestrowania pokazany:

   1. Wybierz **wysyłać do analizy dzienników**. 
   2. W obszarze **analizy dzienników**, wybierz **Konfiguruj**. 
   3. W obszarze **obszarów roboczych OMS**, wybierz obszar roboczy analizy dzienników na potrzeby rejestrowania.
   4. W obszarze **dziennika**, wybierz pozycję **IntegrationAccountTrackingEvents** kategorii.
   5. Wybierz pozycję **Zapisz**.

   ![Konfigurowanie analizy dzienników, aby wysłać dane diagnostyczne dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Teraz [skonfigurować śledzenie wiadomości B2B w analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Włącz rejestrowanie danych diagnostycznych za pośrednictwem Monitora Azure

1. W [portalu Azure](https://portal.azure.com), w menu głównym Azure wybierz **Monitor**, **dzienników diagnostycznych**. Następnie wybierz konta integracji, jak pokazano poniżej:

   ![Wybierz pozycję "Monitoruj", "Dzienniki diagnostyczne", wybierz konto integracji](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Po wybraniu konta integracji następujące wartości zostaną zaznaczone automatycznie. Jeśli te wartości są prawidłowe, wybierz **Włącz diagnostykę**. W przeciwnym razie wybierz wartości, które mają:

   1. W obszarze **subskrypcji**, wybierz subskrypcję platformy Azure korzystające z Twoim kontem integracji.
   2. W obszarze **grupy zasobów**, wybierz grupę zasobów, korzystających z Twoim kontem integracji.
   3. W obszarze **typu zasobu**, wybierz pozycję **konta integracji**.
   4. W obszarze **zasobów**, wybierz konto integracji.
   5. Wybierz **Włącz diagnostykę**.

   ![Ustawienia diagnostyki dla Twojego konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. W obszarze **ustawień diagnostycznych**, wybierz **na**.

   ![Włącz diagnostykę Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Teraz wybierz analizy dzienników kategorii obszaru roboczego i zdarzenia logowania, jak pokazano:

   1. Wybierz **wysyłać do analizy dzienników**. 
   2. W obszarze **analizy dzienników**, wybierz **Konfiguruj**. 
   3. W obszarze **obszarów roboczych OMS**, wybierz obszar roboczy analizy dzienników na potrzeby rejestrowania.
   4. W obszarze **dziennika**, wybierz pozycję **IntegrationAccountTrackingEvents** kategorii.
   5. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   ![Konfigurowanie analizy dzienników, aby wysłać dane diagnostyczne dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Teraz [skonfigurować śledzenie wiadomości B2B w analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozszerzanie, jak i gdzie używać danych diagnostycznych z innymi usługami

Wraz z Azure Log Analytics można rozszerzyć, jak używasz aplikacji logiki danych diagnostycznych z innymi usługami Azure, na przykład: 

* [Archiwum, które dzienniki diagnostyczne platformy Azure w magazynie Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Strumieniowe przesyłanie dzienników diagnostycznych platformy Azure do usługi Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Następnie monitorowanie za pomocą telemetrii i analiza z innych usług, takich jak get w czasie rzeczywistym [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [usługi Power BI](../log-analytics/log-analytics-powerbi.md). Na przykład:

* [Strumień danych z usługi Event Hubs Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizować dane przesyłane strumieniowo z usługi Stream Analytics i utworzyć pulpit nawigacyjny analiz w czasie rzeczywistym w usłudze Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Oparte na opcje, które chcesz skonfigurować, upewnij się, że możesz pierwszy [utworzyć konto magazynu Azure](../storage/common/storage-create-storage-account.md) lub [tworzenia Centrum zdarzeń Azure](../event-hubs/event-hubs-create.md). Następnie wybierz opcje dla których chcesz wysyłać dane diagnostyczne:

![Wysyłanie danych do magazynu Azure konta lub zdarzenia koncentratora.](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Okresy przechowywania mają zastosowanie tylko wtedy, gdy chcesz użyć konta magazynu.

## <a name="supported-tracking-schemas"></a>Obsługiwane schematy śledzenia

Azure obsługuje te śledzenia typów schematów, które ustalone schematów z wyjątkiem typu niestandardowego.

* [Schemat śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemat śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie wiadomości B2B w analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "wiadomości B2B śledzenie w OMS")
* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw")

