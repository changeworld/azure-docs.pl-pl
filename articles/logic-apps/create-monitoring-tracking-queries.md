---
title: Wyświetlanie i tworzenie zapytań dotyczących aplikacji logiki w dziennikach usługi Azure Monitor
description: Wyświetlanie i tworzenie zapytań w dziennikach usługi Azure Monitor dla aplikacji logiki azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908074"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Wyświetlanie i tworzenie zapytań do monitorowania i śledzenia w dziennikach usługi Azure Monitor dla aplikacji logiki Azure

Można wyświetlić podstawowe kwerendy, które dają wyniki z [dzienników usługi Azure Monitor](../log-analytics/log-analytics-overview.md) i tworzyć kwerendy, które filtrują wyniki na podstawie określonych kryteriów. Na przykład można znaleźć wiadomości na podstawie określonego numeru kontroli wymiany. Kwerendy używają [języka zapytania Kusto](https://aka.ms/LogAnalyticsLanguageReference), który można edytować, jeśli chcesz wyświetlić różne wyniki. Aby uzyskać więcej informacji, zobacz [Kwerendy dziennika usługi Azure Monitor](../azure-monitor/log-query/query-language.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Jeśli nie masz obszaru roboczego usługi Log Analytics, dowiedz się, [jak utworzyć obszar roboczy usługi Log Analytics.](../azure-monitor/learn/quick-create-workspace.md)

* Aplikacja logiki, która jest skonfigurowana przy użyciu rejestrowania usługi Azure Monitor i wysyła te informacje do obszaru roboczego usługi Log Analytics. Dowiedz [się, jak skonfigurować dzienniki usługi Azure Monitor dla aplikacji logiki.](../logic-apps/monitor-logic-apps.md)

* Jeśli używasz konta integracji, upewnij się, że konto zostało skonfigurowane za pomocą rejestrowania usługi Azure Monitor, aby wysłać te informacje do obszaru roboczego usługi Log Analytics. Dowiedz się, jak [skonfigurować rejestrowanie usługi Azure Monitor dla konta integracji.](../logic-apps/monitor-b2b-messages-log-analytics.md)

## <a name="view-queries-behind-results"></a>Wyświetlanie zapytań za wynikami

Aby wyświetlić lub edytować kwerendę, która daje wyniki w podsumowaniu obszaru roboczego, wykonaj następujące kroki:

1. Na dowolnej stronie wyników u dołu wybierz pozycję **Zobacz wszystkie**.

   ![Zobacz wszystkie wyniki](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Zostanie otwarta strona Dzienniki i wyświetlona kwerenda za poprzednią stroną wyników.

   ![Strona Dzienniki - widok kwerendy](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na stronie **Dzienniki** można wybrać następujące opcje:

   * Aby wyświetlić wyniki kwerendy jako tabelę, w edytorze kwerend wybierz pozycję **Tabela**.

   * Aby zmienić kwerendę, zaktualizuj ciąg zapytania i wybierz pozycję **Uruchom,** aby wyświetlić wyniki w tabeli.

## <a name="create-your-own-query"></a>Tworzenie własnego zapytania

Aby znaleźć lub filtrować wyniki na podstawie określonych właściwości lub wartości, można utworzyć własną kwerendę, zaczynając od pustej kwerendy lub użyć istniejącej kwerendy. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz obszar roboczy usługi Log Analytics.

1. W menu obszaru roboczego w obszarze **Ogólne**wybierz pozycję **Dzienniki**.

1. Rozpocznij od pustej kwerendy lub wszelkich dostępnych istniejących zapytań.

   * Aby sprawdzić, czy istniejące kwerendy są dostępne, na pasku narzędzi kwerendy wybierz historię**History**kwerend >  **próbkowania,** która pokazuje kwerendy z poprzednich uruchomień kwerendy, lub wybierz **Eksplorator kwerend**, który pokazuje wstępnie utworzone kwerendy.

     Na przykład rozwiązanie Logic Apps B2B udostępnia te wstępnie utworzone kwerendy:

     ![Zacznij od wstępnie utworzonych zapytań "Logic Apps B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Aby rozpocząć od pustej kwerendy, w edytorze zapytań zacznij wpisywać [język zapytania Kusto](../azure-monitor/log-query/query-language.md) dla kwerendy.

     ![Zacznij od pustej kwerendy](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Następne kroki

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schematy śledzenia niestandardowego](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)