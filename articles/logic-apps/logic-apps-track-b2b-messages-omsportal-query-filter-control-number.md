---
title: Tworzenie śledzenia zapytań dotyczących komunikatów B2B w usłudze Azure Monitor dzienniki — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie zapytań, które śledzą AS2, X 12 i EDIFACT wiadomości w usłudze Azure Log Analytics dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846623"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Tworzenie śledzenia zapytań dotyczących komunikatów B2B w dziennikach w usłudze Azure Monitor dla usługi Azure Logic Apps

Aby znaleźć AS2, X12 lub EDIFACT komunikatów, należy prześledzić z [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md), można tworzyć zapytania, które akcje na podstawie konkretnych kryteriów filtru. Na przykład możesz znaleźć wiadomości na numer kontrolny wymiany określonej podstawie.

> [!NOTE]
> Ta strona opisane wcześniej kroki dotyczące sposobu wykonywania tych zadań za pomocą programu Microsoft Operations Management Suite (OMS), który jest [wycofywania w styczniu 2019](../azure-monitor/platform/oms-portal-transition.md), zamiast tego zastępuje te kroki z usługą Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikację logiki, która została skonfigurowana za pomocą rejestrowania diagnostycznego. Dowiedz się, [jak utworzyć aplikację logiki](quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla tej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Konto integracji skonfigurowanej z funkcjami monitorowania i rejestrowania. Dowiedz się, [jak utworzyć konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [jak skonfigurować monitorowanie i rejestrowanie dla tego konta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Jeśli jeszcze nie, [publikowania danych diagnostycznych do usługi Azure Monitor dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) i [Konfigurowanie śledzenia w dziennikach w usłudze Azure Monitor komunikatu](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Tworzenie zapytań z filtrami

Aby znaleźć komunikaty na podstawie określonych właściwości lub wartości, można tworzyć zapytania, które używają filtrów. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania Znajdź "log analytics", a następnie wybierz **usługi Log Analytics**.

   ![Wybierz usługi Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. W obszarze **usługi Log Analytics**, Znajdź i wybierz swój obszar roboczy usługi Log Analytics. 

   ![Wybierz obszar roboczy usługi Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. W menu obszaru roboczego w obszarze **ogólne**, wybierz opcję **dzienniki (wersja klasyczna)** lub **dzienniki**. 

   W tym przykładzie pokazano, jak używać klasycznego widoku dzienników. 
   Jeśli wybierzesz **wyświetlanie dzienników** w **optymalnie wykorzystać usługi Log Analytics** sekcji w obszarze **wyszukiwanie i analizowanie dzienników**, otrzymasz **dzienniki (klasyczny)** . 

   ![Wyświetl dzienniki klasycznego](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Pole edycji zapytanie, zacznij wpisywać nazwę pola, które chcesz znaleźć. Gdy zaczniesz pisać, Edytor zapytań zawiera możliwych dopasowań i operacje, których można użyć. Po utworzeniu zapytania, wybierz **Uruchom** lub naciśnij klawisz Enter.

   W tym przykładzie wyszukuje dopasowania WE w **LogicAppB2B**. 
   Dowiedz się więcej o [sposób znaleźć dane w dziennikach w usłudze Azure Monitor](../log-analytics/log-analytics-log-searches.md).

   ![Rozpocznij wpisywanie ciągu zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Aby zmienić przedział czasu, który chcesz wyświetlić w okienku po lewej stronie wybierz z listy czasu trwania lub za pomocą suwaka. 

   ![Zmień przedział czasu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Aby dodać filtr do zapytania, wybierz opcję **Dodaj**. 

   ![Dodawanie filtru do zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. W obszarze **Dodaj filtry**, wprowadź nazwę filtru, które chcesz znaleźć. Jeśli okaże się filtr, wybierz z filtrem. W okienku po lewej stronie wybierz **Dodaj** ponownie.

   Na przykład poniżej przedstawiono inne zapytanie, która wyszukuje **typ == "AzureDiagnostics"** zdarzenia i znajduje wyniki na podstawie numeru kontrolnego wymiany wybierając **event_record_messageProperties_ interchangeControlNumber_s** filtru.

   ![Wybierz wartość filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Po wybraniu **Dodaj**, zapytanie jest aktualizowany z wybrany filtr zdarzeń i wartości. 
   Twoje poprzednie wyniki są teraz filtrowane zbyt. 

   Na przykład to zapytanie szuka **typ == "AzureDiagnostics"** i wyszukuje wyniki na podstawie numeru kontrolnego wymiany przy użyciu **event_record_messageProperties_interchangeControlNumber_s**filtru.

   ![Filtrowane wyniki](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Zapisz zapytanie

Aby zapisać zapytanie w **dzienniki (wersja klasyczna)** wyświetlić, wykonaj następujące kroki:

1. Zapytania na **dzienniki (wersja klasyczna)** wybierz **analizy**. 

   ![Wybierz pozycję "Analytics"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Na pasku zapytań wybierz **Zapisz**.

   ![Wybieranie pozycji „Zapisz”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Podaj szczegóły dotyczące zapytania, na przykład, Nazwij zapytanie, wybierz opcję **zapytania**, a następnie podaj nazwę kategorii. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   ![Wybieranie pozycji „Zapisz”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Aby wyświetlić zapisane kwerendy, wróć do strony kwerendy. Na pasku zapytań wybierz **zapisane wyszukiwania**.

   ![Wybierz pozycję "Zapisane wyszukiwanie"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. W obszarze **zapisane wyszukiwania**, wybierz zapytanie, aby można było wyświetlić wyniki. 

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Aby zaktualizować zapytanie, aby mogli znaleźć różne wyniki, należy edytować zapytanie.

## <a name="find-and-run-saved-queries"></a>Znajdowanie i uruchamianie zapisanych zapytań

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania Znajdź "log analytics", a następnie wybierz **usługi Log Analytics**.

   ![Wybierz usługi Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. W obszarze **usługi Log Analytics**, Znajdź i wybierz swój obszar roboczy usługi Log Analytics. 

   ![Wybierz obszar roboczy usługi Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. W menu obszaru roboczego w obszarze **ogólne**, wybierz opcję **dzienniki (wersja klasyczna)** lub **dzienniki**. 

   W tym przykładzie pokazano, jak używać klasycznego widoku dzienników. 

1. Po otwarciu strony kwerendy, na pasku narzędzi zapytań wybierz **zapisane wyszukiwania**.

   ![Wybierz pozycję "Zapisane wyszukiwanie"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. W obszarze **zapisane wyszukiwania**, wybierz zapytanie, aby można było wyświetlić wyniki. 

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Zapytanie jest uruchamiana automatycznie, ale jeśli zapytanie nie zostanie uruchomiona z jakiegokolwiek powodu, w edytorze zapytań wybierz **Uruchom**.

## <a name="next-steps"></a>Kolejne kroki

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schematy śledzenia niestandardowe](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)