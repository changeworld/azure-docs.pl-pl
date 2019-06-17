---
title: Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Monitorowanie AS2, X 12 i EDIFACT wiadomości dla konta integracji i Azure Logic Apps i konfigurowanie rejestrowania diagnostycznego przy użyciu dzienników usługi Azure Monitor
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: 12799a308157c3c0e19de1f82c0fe3df44fad37e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106304"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor w usłudze Azure Logic Apps

Po skonfigurowaniu komunikacji B2B między partnerami handlowymi na koncie integracji tych partnerów mogą wymieniać komunikaty ze sobą. Aby sprawdzić, czy ta komunikacja działa w oczekiwany sposób, można monitorować AS2, X12, i EDIFACT komunikatów i konfigurowanie logowania dla konta integracji z diagnostyki [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md). Ta usługa monitoruje środowiska w chmurze i lokalnych zachowania ich dostępności i wydajności i zbiera szczegóły środowiska uruchomieniowego i zdarzenia dla bardziej zaawansowane debugowanie. Można również użyć tych danych z innymi usługami, takimi jak usługi Azure Storage i Azure Event Hubs.

> [!NOTE]
> Na tej stronie może nadal mieć odwołania do programu Microsoft Operations Management Suite (OMS), czyli [wycofywania w styczniu 2019](../azure-monitor/platform/oms-portal-transition.md), lecz zastępuje te kroki z usługą Azure Log Analytics, jeśli jest to możliwe. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikację logiki, która została skonfigurowana za pomocą rejestrowania diagnostycznego. Dowiedz się, [jak utworzyć aplikację logiki](quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla tej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Po użytkownik spełnia poprzednie wymagania, należy również obszar roboczy usługi Log Analytics, która jest używana do monitorowania i śledzenia komunikacji B2B przy użyciu dzienników usługi Azure Monitor. Dowiedz się, jeśli nie masz obszaru roboczego usługi Log Analytics [jak utworzyć obszar roboczy usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Konto integracji jest połączony z aplikacji logiki. Dowiedz się, [sposób tworzenia konta integracji z linkiem do aplikacji logiki](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Włączanie rejestrowania diagnostycznego

Można włączyć rejestrowanie albo bezpośrednio z Twojego konta integracji lub [za pośrednictwem usługi Azure Monitor](#azure-monitor-service). Usługa Azure Monitor zapewnia podstawowe monitorowanie za pomocą danych na poziomie infrastruktury. Dowiedz się więcej o [usługi Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Włączanie rejestrowania z konta integracji

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz swoje konto integracji. W obszarze **monitorowanie**, wybierz opcję **ustawień diagnostycznych**.

   ![Znajdź i wybierz swoje konto integracji, wybierz pozycję "Ustawienia diagnostyczne"](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Teraz Znajdź i wybierz swoje konto integracji. Na listach filtru wybierz wartości, które są stosowane na koncie integracji.
Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Subskrypcja** | <*Azure-subscription-name*> | Subskrypcja platformy Azure, która jest skojarzona z kontem integracji | 
   | **Grupa zasobów** | <*Azure-resource-group-name*> | Grupy zasobów platformy Azure dla konta integracji | 
   | **Typ zasobu** | **Integracja kont** | Typ dla zasobów platformy Azure, której chcesz włączyć rejestrowanie | 
   | **Zasób** | <*integration-account-name*> | Nazwa dla swoich zasobów platformy Azure, w której chcesz włączyć rejestrowanie | 
   ||||  

   Na przykład:

   ![Konfigurowanie diagnostyki dla konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Podaj nazwę dla nowego ustawienia diagnostycznego, a następnie wybierz obszar roboczy usługi Log Analytics i dane, które mają być rejestrowane.

   1. Wybierz **wysyłanie do usługi Log Analytics**. 

   1. W obszarze **usługi Log Analytics**, wybierz opcję **Konfiguruj**. 

   1. W obszarze **obszarów roboczych pakietu OMS**, wybierz obszar roboczy usługi Log Analytics, którego chcesz użyć do rejestrowania. 

      > [!NOTE]
      > Obszary robocze OMS są zastępowane przez obszary robocze usługi Log Analytics. 

   1. W obszarze **dziennika**, wybierz opcję **IntegrationAccountTrackingEvents** kategorii i wybierz polecenie **Zapisz**.

   Na przykład: 

   ![Konfigurowanie dzienników usługi Azure Monitor, co umożliwia wysyłanie danych diagnostycznych do dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Teraz [skonfigurować śledzenie wiadomości B2B w usłudze Azure Monitor dziennikach](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Włącz rejestrowanie za pomocą usługi Azure Monitor

1. W [witryny Azure portal](https://portal.azure.com), w menu głównym platformy Azure wybierz **Monitor**. W obszarze **ustawienia**, wybierz opcję **ustawień diagnostycznych**. 

   ![Wybierz pozycję "Monitor" > "Ustawienia diagnostyki" > konta integracji](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Teraz Znajdź i wybierz swoje konto integracji. Na listach filtru wybierz wartości, które są stosowane na koncie integracji.
Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Subskrypcja** | <*Azure-subscription-name*> | Subskrypcja platformy Azure, która jest skojarzona z kontem integracji | 
   | **Grupa zasobów** | <*Azure-resource-group-name*> | Grupy zasobów platformy Azure dla konta integracji | 
   | **Typ zasobu** | **Integracja kont** | Typ dla zasobów platformy Azure, której chcesz włączyć rejestrowanie | 
   | **Zasób** | <*integration-account-name*> | Nazwa dla swoich zasobów platformy Azure, w której chcesz włączyć rejestrowanie | 
   ||||  

   Na przykład:

   ![Konfigurowanie diagnostyki dla konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Podaj nazwę dla nowego ustawienia diagnostycznego, a następnie wybierz obszar roboczy usługi Log Analytics i dane, które mają być rejestrowane.

   1. Wybierz **wysyłanie do usługi Log Analytics**. 

   1. W obszarze **usługi Log Analytics**, wybierz opcję **Konfiguruj**. 

   1. W obszarze **obszarów roboczych pakietu OMS**, wybierz obszar roboczy usługi Log Analytics, którego chcesz użyć do rejestrowania. 

      > [!NOTE]
      > Obszary robocze OMS są zastępowane przez obszary robocze usługi Log Analytics. 

   1. W obszarze **dziennika**, wybierz opcję **IntegrationAccountTrackingEvents** kategorii i wybierz polecenie **Zapisz**.

   Na przykład: 

   ![Konfigurowanie dzienników usługi Azure Monitor, co umożliwia wysyłanie danych diagnostycznych do dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Teraz [skonfigurować śledzenie wiadomości B2B w usłudze Azure Monitor dziennikach](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Dane diagnostyczne używane z innymi usługami

Wraz z dzienników usługi Azure Monitor można rozszerzyć, wykorzystania danych diagnostycznych z aplikacji logiki z innymi usługami platformy Azure, na przykład: 

* [Archiwum, które dzienniki diagnostyczne platformy Azure w usłudze Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Dzienniki diagnostyczne platformy Azure Stream do usługi Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Następnie monitorowanie przy użyciu danych telemetrycznych i analitycznych z innych usług, takich jak get w czasie rzeczywistym [usługi Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [usługi Power BI](../azure-monitor/platform/powerbi.md). Na przykład:

* [Stream dane z usługi Event Hubs do usługi Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizuj dane przesyłane strumieniowo przy użyciu usługi Stream Analytics i utworzyć pulpit nawigacyjny analizy w czasie rzeczywistym w usłudze Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na podstawie opcji chcesz mieć zainstalowanego, upewnij się, że możesz pierwszy [Tworzenie konta usługi Azure storage](../storage/common/storage-create-storage-account.md) lub [Tworzenie Centrum zdarzeń Azure](../event-hubs/event-hubs-create.md). Następnie możesz wybrać miejsc docelowych, które chcesz wysyłać dane diagnostyczne.
Okresy przechowywania mają zastosowanie tylko wtedy, gdy użytkownik chce użyć konta magazynu.

![Wysłać dane do Centrum konta lub zdarzenia usługi Azure storage](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Obsługiwane schematy śledzenia

Platforma Azure obsługuje te śledzenie typów schematu, które ustalone schematów z wyjątkiem typu niestandardowego.

* [Schemat śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemat śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie komunikatów B2B w usłudze Azure Monitor dziennikach](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "komunikatów B2B śledzenie w dziennikach w usłudze Azure Monitor")
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")

