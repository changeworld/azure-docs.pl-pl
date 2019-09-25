---
title: Monitorowanie komunikatów B2B przy użyciu dzienników Azure Monitor — Azure Logic Apps | Microsoft Docs
description: Monitoruj komunikaty AS2, X12 i EDIFACT dla kont integracji i Azure Logic Apps i skonfiguruj rejestrowanie diagnostyki przy użyciu dzienników Azure Monitor
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: a4a7f951d34455f2e333f2c11e30d24efdfd22c1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261201"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Monitorowanie komunikatów B2B przy użyciu dzienników Azure Monitor w Azure Logic Apps

Po skonfigurowaniu komunikacji B2B między partnerami handlowymi na Twoim koncie integracyjnym partnerzy mogą wymieniać komunikaty ze sobą. Aby sprawdzić, czy ta komunikacja działa w oczekiwany sposób, możesz monitorować komunikaty AS2, X12 i EDIFACT oraz konfigurować rejestrowanie danych diagnostycznych dla konta integracji za pomocą [dzienników Azure monitor](../log-analytics/log-analytics-overview.md). Ta usługa monitoruje środowiska w chmurze i lokalne, pomaga zachować swoją dostępność i wydajność oraz gromadzi szczegóły i zdarzenia środowiska uruchomieniowego w celu zaawansowania debugowania. Możesz również użyć tych danych z innymi usługami, takimi jak Azure Storage i Azure Event Hubs.

> [!NOTE]
> Ta strona może nadal mieć odwołania do Microsoft Operations Management Suite (OMS), które są [wycofywane w styczniu 2019](../azure-monitor/platform/oms-portal-transition.md), ale zastępują te kroki na platformie Azure log Analytics, tam gdzie to możliwe. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja logiki, która jest skonfigurowana z rejestrowaniem diagnostyki. Dowiedz się, [jak utworzyć aplikację logiki](quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla tej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Po spełnieniu powyższych wymagań potrzebny jest również obszar roboczy Log Analytics, który służy do monitorowania i śledzenia komunikacji B2B za pomocą dzienników Azure Monitor. Jeśli nie masz obszaru roboczego Log Analytics, zapoznaj się [z tematem tworzenie log Analytics obszaru roboczego](../azure-monitor/learn/quick-create-workspace.md).

* Konto integracji połączone z aplikacją logiki. Dowiedz się, [jak utworzyć konto integracji z linkiem do aplikacji logiki](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Włącz rejestrowanie diagnostyczne

Rejestrowanie można włączyć bezpośrednio z poziomu konta integracji lub [za pośrednictwem usługi Azure monitor](#azure-monitor-service). Azure Monitor zapewnia podstawowe monitorowanie za pomocą danych poziomu infrastruktury. Dowiedz się więcej o [Azure monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Włącz rejestrowanie na koncie integracji

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoje konto integracji. W obszarze **monitorowanie**, wybierz opcję **ustawień diagnostycznych**.

   ![Znajdź i wybierz swoje konto integracji, wybierz pozycję Ustawienia diagnostyczne](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Teraz Znajdź i wybierz swoje konto integracji. Z listy filtrów wybierz wartości, które mają zastosowanie do konta integracji.
Gdy skończysz, wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   | Właściwość | Value | Opis | 
   |----------|-------|-------------|
   | **Subskrypcja** | <*Azure-subscription-name*> | Subskrypcja platformy Azure skojarzona z kontem integracji | 
   | **Grupa zasobów** | <*Azure-resource-group-name*> | Grupa zasobów platformy Azure dla konta integracji | 
   | **Typ zasobu** | **Integracja kont** | Typ zasobu platformy Azure, w którym ma zostać włączone rejestrowanie | 
   | **Zasób** | <*Integracja — nazwa konta*> | Nazwa zasobu platformy Azure, w którym ma zostać włączona Rejestracja | 
   ||||  

   Na przykład:

   ![Konfigurowanie diagnostyki dla konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Podaj nazwę nowego ustawienia diagnostycznego, wybierz obszar roboczy Log Analytics i dane, które chcesz zarejestrować.

   1. Wybierz pozycję **Wyślij do log Analytics**. 

   1. W obszarze **log Analytics**wybierz pozycję **Konfiguruj**. 

   1. W obszarze **obszary robocze OMS**wybierz obszar roboczy log Analytics, który ma być używany do rejestrowania. 

      > [!NOTE]
      > Obszary robocze pakietu OMS są zastępowane przez Log Analytics obszary robocze. 

   1. W obszarze **Dziennik**wybierz kategorię **IntegrationAccountTrackingEvents** , a następnie wybierz pozycję **Zapisz**.

   Na przykład: 

   ![Konfigurowanie dzienników Azure Monitor, aby można było wysyłać dane diagnostyczne do dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Teraz [Skonfiguruj śledzenie dla wiadomości B2B w dziennikach Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Włącz logowanie za Azure Monitor

1. W [Azure Portal](https://portal.azure.com)w głównym menu platformy Azure wybierz pozycję **Monitoruj**. W obszarze **Ustawienia**wybierz pozycję **Ustawienia diagnostyki**. 

   ![Wybierz pozycję "Monitoruj" > "Ustawienia diagnostyki" > konto integracji](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Teraz Znajdź i wybierz swoje konto integracji. Z listy filtrów wybierz wartości, które mają zastosowanie do konta integracji.
Gdy skończysz, wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   | Właściwość | Value | Opis | 
   |----------|-------|-------------|
   | **Subskrypcja** | <*Azure-subscription-name*> | Subskrypcja platformy Azure skojarzona z kontem integracji | 
   | **Grupa zasobów** | <*Azure-resource-group-name*> | Grupa zasobów platformy Azure dla konta integracji | 
   | **Typ zasobu** | **Integracja kont** | Typ zasobu platformy Azure, w którym ma zostać włączone rejestrowanie | 
   | **Zasób** | <*Integracja — nazwa konta*> | Nazwa zasobu platformy Azure, w którym ma zostać włączona Rejestracja | 
   ||||  

   Na przykład:

   ![Konfigurowanie diagnostyki dla konta integracji](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Podaj nazwę nowego ustawienia diagnostycznego, wybierz obszar roboczy Log Analytics i dane, które chcesz zarejestrować.

   1. Wybierz pozycję **Wyślij do log Analytics**. 

   1. W obszarze **log Analytics**wybierz pozycję **Konfiguruj**. 

   1. W obszarze **obszary robocze OMS**wybierz obszar roboczy log Analytics, który ma być używany do rejestrowania. 

      > [!NOTE]
      > Obszary robocze pakietu OMS są zastępowane przez Log Analytics obszary robocze. 

   1. W obszarze **Dziennik**wybierz kategorię **IntegrationAccountTrackingEvents** , a następnie wybierz pozycję **Zapisz**.

   Na przykład: 

   ![Konfigurowanie dzienników Azure Monitor, aby można było wysyłać dane diagnostyczne do dziennika](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Teraz [Skonfiguruj śledzenie dla wiadomości B2B w dziennikach Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Korzystanie z danych diagnostycznych z innymi usługami

Wraz z Azure Monitorymi dziennikami możesz dołożyć, jak używać danych diagnostycznych aplikacji logiki z innymi usługami platformy Azure, na przykład: 

* [Archiwizowanie dzienników Diagnostyka Azure w usłudze Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Przesyłanie strumieniowe dzienników Diagnostyka Azure do platformy Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Następnie można uzyskać monitorowanie w czasie rzeczywistym, korzystając z danych telemetrycznych i analiz z innych usług, takich jak [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [Power BI](../azure-monitor/platform/powerbi.md). Na przykład:

* [Przesyłanie strumieniowe danych z Event Hubs do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizowanie danych przesyłanych strumieniowo za pomocą Stream Analytics i tworzenie pulpitu nawigacyjnego analizy w czasie rzeczywistym w programie Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na podstawie opcji, które chcesz skonfigurować, upewnij się, że najpierw [utworzono konto usługi Azure Storage](../storage/common/storage-create-storage-account.md) lub [utworzono centrum zdarzeń platformy Azure](../event-hubs/event-hubs-create.md). Następnie możesz wybrać miejsca docelowe, w których chcesz wysyłać dane diagnostyczne.
Okresy przechowywania są stosowane tylko w przypadku wybrania opcji korzystania z konta magazynu.

![Wysyłanie danych do konta usługi Azure Storage lub centrum zdarzeń](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Obsługiwane schematy śledzenia

Platforma Azure obsługuje te typy schematów śledzenia, które mają stałe schematy, z wyjątkiem typów niestandardowych.

* [Schemat śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemat śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Śledzenie komunikatów B2B w dziennikach Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Śledzenie komunikatów B2B w dziennikach Azure Monitor")
* [Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw")

