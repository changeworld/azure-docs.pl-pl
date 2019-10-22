---
title: Tworzenie zapytań śledzenia dla komunikatów B2B — Azure Logic Apps
description: Twórz zapytania, które śledzą komunikaty AS2, X12 i EDIFACT w usłudze Azure Log Analytics dla Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 7d7bb53d24a113ea78b5bac3f9682fbb61ce2de9
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680100"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Twórz zapytania śledzenia dotyczące komunikatów B2B w Azure Monitor dziennikach dla Azure Logic Apps

Aby znaleźć komunikaty AS2, X12 lub EDIFACT, które są śledzone za pomocą [dzienników Azure monitor](../log-analytics/log-analytics-overview.md), można utworzyć zapytania, które filtrują akcje na podstawie określonych kryteriów. Na przykład można znaleźć komunikaty na podstawie określonego numeru kontrolnego wymiany.

> [!NOTE]
> Ta strona zawiera wcześniej procedurę wykonywania tych zadań z użyciem Microsoft Operations Management Suite (OMS), która jest [wycofywana w styczniu 2019](../azure-monitor/platform/oms-portal-transition.md), zastępuje te kroki przy użyciu usługi Azure log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja logiki, która jest skonfigurowana z rejestrowaniem diagnostyki. Dowiedz się, [jak utworzyć aplikację logiki](quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla tej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Konto integracji, które jest skonfigurowane z monitorowaniem i rejestrowaniem. Dowiedz się, [jak utworzyć konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [jak skonfigurować monitorowanie i rejestrowanie dla tego konta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Jeśli nie zostało to jeszcze zrobione, [Opublikuj dane diagnostyczne do Azure monitor dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) i [Skonfiguruj śledzenie komunikatów w dziennikach Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Twórz zapytania z filtrami

Aby znaleźć komunikaty na podstawie określonych właściwości lub wartości, można utworzyć zapytania, które używają filtrów. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania Znajdź pozycję "log Analytics" i wybierz pozycję **log Analytics**.

   ![Wybierz Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. W obszarze **log Analytics**Znajdź i wybierz obszar roboczy log Analytics. 

   ![Wybierz Log Analytics obszar roboczy](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. W menu obszaru roboczego w obszarze **Ogólne**wybierz pozycję **dzienniki (klasyczne)** lub **dzienniki**. 

   Ten przykład pokazuje, jak używać widoku dzienników klasycznych. 
   Jeśli wybierzesz opcję **Wyświetl dzienniki** w sekcji **Maksymalizuj swoją log Analytics środowiska** , w obszarze **Wyszukiwanie i analizowanie dzienników**uzyskasz dostęp do **dzienników (widok klasyczny)** . 

   ![Wyświetlanie dzienników klasycznych](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. W polu edycji zapytania Zacznij wpisywać nazwę pola, które chcesz znaleźć. Po rozpoczęciu wpisywania Edytor zapytań pokazuje możliwe dopasowania i operacje, których można użyć. Po utworzeniu zapytania wybierz polecenie **Uruchom** lub naciśnij klawisz ENTER.

   Ten przykład wyszukuje dopasowania w **LogicAppB2B**. 
   Dowiedz się więcej o [sposobach znajdowania danych w dziennikach Azure monitor](../log-analytics/log-analytics-log-searches.md).

   ![Rozpocznij wpisywanie ciągu zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Aby zmienić przedział czasu, który chcesz wyświetlić, w okienku po lewej stronie wybierz pozycję z listy czas trwania lub przeciągnij suwak. 

   ![Zmień przedział czasu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Aby dodać filtr do zapytania, wybierz pozycję **Dodaj**. 

   ![Dodaj filtr do zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. W obszarze **Dodaj filtry**wprowadź nazwę filtru, który chcesz znaleźć. Jeśli znajdziesz filtr, wybierz ten filtr. W lewym okienku wybierz pozycję **Dodaj** ponownie.

   Na przykład poniżej znajduje się inne zapytanie, które wyszukuje zdarzenia **typu = = "AzureDiagnostics"** i wyszukuje wyniki na podstawie numeru kontrolnego wymiany, wybierając filtr **event_record_messageProperties_interchangeControlNumber_s** .

   ![Wybierz wartość filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Po wybraniu polecenia **Dodaj**zapytanie zostanie zaktualizowane z wybranym zdarzeniem filtru i wartością. 
   Twoje poprzednie wyniki są teraz filtrowane. 

   Na przykład to zapytanie wyszukuje **Typ = = "AzureDiagnostics"** i wyszukuje wyniki na podstawie numeru kontrolnego wymiany przy użyciu filtru **event_record_messageProperties_interchangeControlNumber_s** .

   ![Wyniki filtrowania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Zapisz zapytanie

Aby zapisać zapytanie w widoku **dzienniki (klasyczne)** , wykonaj następujące kroki:

1. Z zapytania na stronie **dzienniki (klasyczne)** wybierz pozycję **Analiza**. 

   ![Wybierz pozycję "Analiza"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Na pasku narzędzi zapytania wybierz pozycję **Zapisz**.

   ![Wybieranie pozycji „Zapisz”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Podaj szczegółowe informacje o zapytaniu, na przykład Podaj **nazwę zapytania i**Podaj nazwę kategorii. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   ![Wybieranie pozycji „Zapisz”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Aby wyświetlić zapisane zapytania, Wróć do strony zapytania. Na pasku narzędzi zapytania wybierz pozycję **zapisane wyszukiwania**.

   ![Wybierz pozycję "zapisane wyszukiwania"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. W obszarze **zapisane wyszukiwania**wybierz zapytanie, aby wyświetlić wyniki. 

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Aby zaktualizować zapytanie, aby można było znaleźć różne wyniki, Edytuj zapytanie.

## <a name="find-and-run-saved-queries"></a>Znajdź i uruchom zapisane zapytania

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania Znajdź pozycję "log Analytics" i wybierz pozycję **log Analytics**.

   ![Wybierz Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. W obszarze **log Analytics**Znajdź i wybierz obszar roboczy log Analytics. 

   ![Wybierz Log Analytics obszar roboczy](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. W menu obszaru roboczego w obszarze **Ogólne**wybierz pozycję **dzienniki (klasyczne)** lub **dzienniki**. 

   Ten przykład pokazuje, jak używać widoku dzienników klasycznych. 

1. Po otwarciu strony zapytania na pasku narzędzi zapytania wybierz pozycję **zapisane wyszukiwania**.

   ![Wybierz pozycję "zapisane wyszukiwania"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. W obszarze **zapisane wyszukiwania**wybierz zapytanie, aby wyświetlić wyniki. 

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Zapytanie jest uruchamiane automatycznie, ale jeśli zapytanie nie jest uruchamiane z jakiegokolwiek powodu, w edytorze zapytań wybierz polecenie **Uruchom**.

## <a name="next-steps"></a>Następne kroki

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)