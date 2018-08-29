---
title: Tworzenie zapytań dotyczących komunikatów B2B w usłudze Log Analytics — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie zapytań, które śledzą AS2, X 12 i EDIFACT komunikatów za pomocą usługi Log Analytics dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: baccd255fc2812eae0de3a98dfcef3dcbc7e1b46
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124274"
---
# <a name="create-queries-for-tracking-as2-x12-and-edifact-messages-in-log-analytics-for-azure-logic-apps"></a>Tworzenie zapytań dotyczących śledzenia AS2, X 12 i EDIFACT wiadomości w usłudze Log Analytics w usłudze Azure Logic Apps

Aby znaleźć AS2, X12 lub EDIFACT komunikatów, należy prześledzić z [usługi Azure Log Analytics](../log-analytics/log-analytics-overview.md), można tworzyć zapytania, które akcje na podstawie konkretnych kryteriów filtru. Na przykład możesz znaleźć wiadomości na numer kontrolny wymiany określonej podstawie.

## <a name="requirements"></a>Wymagania

* Aplikację logiki, która została skonfigurowana za pomocą rejestrowania diagnostycznego. Dowiedz się, [jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla tej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Konto integracji skonfigurowanej z funkcjami monitorowania i rejestrowania. Dowiedz się, [jak utworzyć konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [jak skonfigurować monitorowanie i rejestrowanie dla tego konta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Jeśli jeszcze nie, [publikowania danych diagnostycznych do usługi Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) i [skonfigurować komunikat śledzenia w usłudze Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Po poprzednie wymagania zostały spełnione, należy mieć obszar roboczy w usłudze Log Analytics. Należy używać tego samego obszaru roboczego do śledzenia komunikacji B2B w usłudze Log Analytics. 
>  
> Dowiedz się, jeśli nie masz obszaru roboczego usługi Log Analytics [jak utworzyć obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Tworzenie komunikatu zapytań z filtrami w usłudze Log Analytics

Ten przykład pokazuje, jak możesz znaleźć wiadomości, w oparciu o ich numer kontrolny wymiany.

> [!TIP] 
> Jeśli znasz nazwy swojego obszaru roboczego usługi Log Analytics, przejdź do strony głównej swojego obszaru roboczego (`https://{your-workspace-name}.portal.mms.microsoft.com`) i rozpocząć od kroku 4. W przeciwnym razie są liczone od kroku 1.

1. W [witryny Azure portal](https://portal.azure.com), wybierz **wszystkich usług**. Wyszukaj "log analytics", a następnie wybierz **usługi Log Analytics** jak pokazano poniżej:

   ![Znajdowanie usługi Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. W obszarze **usługi Log Analytics**, Znajdź i wybierz swój obszar roboczy usługi Log Analytics.

   ![Wybierz swój obszar roboczy usługi Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. W obszarze **zarządzania**, wybierz **wyszukiwanie w dzienniku**.

   ![Wybierz Lo wyszukiwania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/azure-portal-page.png)

4. W polu wyszukiwania wprowadź pola, które chcesz znaleźć, a następnie naciśnij klawisz **Enter**. Po rozpoczęciu wpisywania, usługi Log Analytics umożliwia wyświetlanie możliwych dopasowań i operacje, których można użyć. Dowiedz się więcej o [jak znaleźć danych w usłudze Log Analytics](../log-analytics/log-analytics-log-searches.md).

   W tym przykładzie wyszukuje zdarzenia przy użyciu **typu = AzureDiagnostics**.

   ![Rozpocznij wpisywanie ciągu zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

5. Na pasku po lewej stronie Wybierz przedział czasu, który chcesz wyświetlić. Aby dodać filtr do zapytania, wybierz opcję **+ Dodaj**.

   ![Dodawanie filtru do zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

6. W obszarze **Dodaj filtry**, wprowadź nazwę filtru, aby móc odnaleźć filtru chcesz. Wybierz filtr, a następnie wybierz **+ Dodaj**.

   Aby znaleźć numer kontrolny wymiany, w tym przykładzie wyszukuje słowo "wymiana" i wybiera **event_record_messageProperties_interchangeControlNumber_s** jako filtr.

   ![Wybierz filtr](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

7. Na pasku po lewej stronie wybierz wartości filtru, który chcesz użyć, a następnie wybierz **Zastosuj**.

   W tym przykładzie wybiera numer kontrolny wymiany komunikatów, którego potrzebujemy.

   ![Wybierz wartość filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

8. Teraz wróć do zapytania, które tworzysz. Zaktualizowano zapytanie wybrany filtr zdarzeń i wartości. Twoje poprzednie wyniki są teraz filtrowane zbyt.

    ![Wróć do zapytania przy użyciu filtrowane wyniki](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Zapisz zapytanie do użycia w przyszłości

1. Zapytania na **wyszukiwanie w dzienniku** wybierz **Zapisz**. Nazwij zapytanie, wybierz kategorię, a następnie wybierz **Zapisz**.

   ![Wprowadź zapytania, nazwę i kategorię](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Aby wyświetlić zapytanie, wybierz **ulubione**.

   ![Wybierz pozycję "Ulubione"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. W obszarze **zapisane wyszukiwania**, wybierz zapytanie, aby wyświetlić wyniki. Aby zaktualizować zapytanie, aby mogli znaleźć różne wyniki, należy edytować zapytanie.

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Znajdowanie i uruchamianie zapytań zapisanych w usłudze Log Analytics

1. Otwórz stronę główną obszaru roboczego usługi Log Analytics (`https://{your-workspace-name}.portal.mms.microsoft.com`) i wybierz polecenie **wyszukiwanie w dzienniku**.

   ![Na stronie głównej usługi Log Analytics wybierz opcję "Log Search"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   — lub —

   ![W menu wybierz polecenie "Log Search"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Na **wyszukiwanie w dzienniku** strony głównej, wybierz polecenie **ulubione**.

   ![Wybierz pozycję "Ulubione"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. W obszarze **zapisane wyszukiwania**, wybierz zapytanie, aby wyświetlić wyniki. Aby zaktualizować zapytanie, aby mogli znaleźć różne wyniki, należy edytować zapytanie.

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Kolejne kroki

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schematy śledzenia niestandardowe](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)