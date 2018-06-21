---
title: Kwerenda dotycząca wiadomości B2B Log Analytics — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie zapytań do śledzenia AS2, X 12 i EDIFACT wiadomości w analizy dzienników
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 48cca9919bd09906bdcc3faaaef186ec109c9169
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294313"
---
# <a name="query-for-as2-x12-and-edifact-messages-in-log-analytics"></a>Wykonanie kwerendy AS2, X 12 i EDIFACT wiadomości w analizy dzienników

Aby znaleźć AS2, X12 lub EDIFACT wiadomości, że podczas śledzenia z [Azure Log Analytics](../log-analytics/log-analytics-overview.md), można utworzyć kwerendy, które akcje na podstawie określonych kryteriów filtrowania. Na przykład można znaleźć na podstawie różnych kontroli określonych wymiany wiadomości.

## <a name="requirements"></a>Wymagania

* Aplikację logiki, który został skonfigurowany z rejestrowania diagnostyki. Dowiedz się [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla danej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Konta integracji, które skonfigurowano przy użyciu rejestrowania i monitorowania. Dowiedz się [sposobu tworzenia konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [jak skonfigurować monitorowanie i rejestrowanie dla tego konta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Jeśli nie jest jeszcze, [publikowania danych diagnostycznych do analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) i [Konfigurowanie śledzenia w analizy dzienników wiadomości](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Po zostały spełnione wymagania poprzedniej, powinien mieć obszar roboczy w analizy dzienników. Do komunikacji B2B w analizy dzienników śledzenia, należy używać tego samego obszaru roboczego. 
>  
> Dowiedz się, jeśli nie masz obszaru roboczego analizy dzienników [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Tworzenie kwerend komunikatów z filtrów w analizy dzienników

Ten przykład przedstawia, jak można znaleźć na podstawie ich liczby kontroli wymiany wiadomości.

> [!TIP] 
> Jeśli znasz nazwę obszaru roboczego analizy dzienników, przejdź do strony głównej obszaru roboczego (`https://{your-workspace-name}.portal.mms.microsoft.com`) i uruchomić w kroku 4. W przeciwnym razie Rozpocznij w kroku 1.

1. W [portalu Azure](https://portal.azure.com), wybierz **wszystkie usługi**. Wyszukaj "analizy dzienników", a następnie wybierz pozycję **analizy dzienników** w sposób pokazany poniżej:

   ![Znajdź analizy dzienników](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. W obszarze **analizy dzienników**, Znajdź i zaznacz pozycję obszaru roboczego analizy dzienników.

   ![Wybierz obszar roboczy analizy dzienników](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. W obszarze **zarządzania**, wybierz **wyszukiwania dziennika**.

   ![Wybierz Lo wyszukiwania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/azure-portal-page.png)

4. W polu wyszukiwania wprowadź pola, które chcesz odnaleźć, a następnie naciśnij klawisz **Enter**. Po rozpoczęciu wprowadzania, analizy dzienników pokazuje, pasujących i operacje, które są dostępne. Dowiedz się więcej o [jak wyszukiwania danych analizy dzienników](../log-analytics/log-analytics-log-searches.md).

   W tym przykładzie wyszukuje zdarzeń o **typu = AzureDiagnostics**.

   ![Wpisz ciąg zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

5. Na pasku po lewej stronie Wybierz przedział czasu, który chcesz wyświetlić. Aby dodać filtr do zapytania, wybierz **+ Dodaj**.

   ![Dodaj filtr do zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

6. W obszarze **Dodaj filtry**, wprowadź nazwę filtru, aby umożliwić znalezienie filtr ma. Wybierz filtr, a następnie wybierz pozycję **+ Dodaj**.

   Aby znaleźć numer formantu wymiany, w tym przykładzie wyszukuje dla słowa "wymiany" i wybiera **event_record_messageProperties_interchangeControlNumber_s** jako filtr.

   ![Wybierz filtr](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

7. Na pasku po lewej stronie wybierz wartości filtru, który chcesz użyć, a następnie wybierz pozycję **Zastosuj**.

   W tym przykładzie wybiera numer kontroli wymiany wiadomości, którą chcemy udostępnić.

   ![Wybierz wartości filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

8. Teraz wróć do zapytania, które tworzysz. Kwerenda została zaktualizowana wybrany filtr zdarzeń i wartość. Poprzednie wyniki teraz są zbyt filtrowane.

    ![Wróć do filtrowane wyniki zapytania](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Zapisz zapytanie do użytku w przyszłości

1. Z kwerendy na **wyszukiwania dziennika** wybierz pozycję **zapisać**. Nadaj nazwę kwerendy, wybierz kategorię i wybierz **zapisać**.

   ![Nadaj zapytania, nazwę i kategorii](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Aby wyświetlić kwerendy, wybierz **ulubione**.

   ![Wybierz pozycję "Ulubione"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. W obszarze **zapisane wyszukiwania**, wybierz zapytanie, dzięki czemu można wyświetlić wyniki. Aby zaktualizować zapytania, aby umożliwić znalezienie różne wyniki, Edytuj zapytanie.

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Znajdź i uruchom zapisane kwerendy w analizy dzienników

1. Otwórz stronę główną obszaru roboczego analizy dzienników (`https://{your-workspace-name}.portal.mms.microsoft.com`) i wybierz polecenie **wyszukiwania dziennika**.

   ![Na stronie głównej Analiza dzienników wybierz polecenie "Dziennik wyszukiwania"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   — lub —

   ![W menu wybierz "Dziennik wyszukiwania"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Na **wyszukiwania dziennika** strony głównej, wybierz **ulubione**.

   ![Wybierz pozycję "Ulubione"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. W obszarze **zapisane wyszukiwania**, wybierz zapytanie, dzięki czemu można wyświetlić wyniki. Aby zaktualizować zapytania, aby umożliwić znalezienie różne wyniki, Edytuj zapytanie.

   ![Wybierz zapytanie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Kolejne kroki

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Śledzenie niestandardowych schematów](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)