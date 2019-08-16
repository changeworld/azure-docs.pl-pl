---
title: Monitorowanie aplikacji logiki za pomocą Azure Monitor-Azure Logic Apps
description: Uzyskaj szczegółowe informacje i Debuguj dane, aby rozwiązywać problemy i diagnozować uruchomienia aplikacji logiki przy użyciu dzienników Azure Monitor
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/16/2019
ms.openlocfilehash: 2f82bd9c0bcacf2c552df84cdd4f8f2cd6a68c8a
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543255"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Uzyskiwanie szczegółowych informacji i debugowanie danych dla aplikacji logiki przy użyciu dzienników Azure Monitor

Aby monitorować i uzyskiwać bogatsze szczegóły debugowania dotyczące aplikacji logiki, Włącz [dzienniki Azure monitor](../log-analytics/log-analytics-overview.md) podczas tworzenia aplikacji logiki. Dzienniki Azure Monitor umożliwiają rejestrowanie i monitorowanie diagnostyki dla aplikacji logiki po zainstalowaniu rozwiązania do zarządzania Logic Apps w Azure Portal. To rozwiązanie zawiera również zagregowane informacje dla aplikacji logiki, które są uruchamiane z określonymi szczegółami, takimi jak stan, czas wykonywania, stan ponownego przesłania i identyfikatory korelacji. W tym artykule przedstawiono sposób włączania dzienników Azure Monitor, aby można było wyświetlać zdarzenia i dane środowiska uruchomieniowego dla uruchamiania aplikacji logiki.

W tym temacie przedstawiono sposób konfigurowania dzienników Azure Monitor podczas tworzenia aplikacji logiki. Aby włączyć dzienniki Azure Monitor dla istniejących aplikacji logiki, wykonaj następujące kroki, aby [włączyć rejestrowanie diagnostyczne i wysyłać dane środowiska uruchomieniowego aplikacji logiki do dzienników Azure monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Ta strona została wcześniej opisana krok po kroku dotyczące wykonywania tych zadań z użyciem Microsoft Operations Management Suite (OMS), który został [wycofany w styczniu 2019](../azure-monitor/platform/oms-portal-transition.md)i zastępuje te kroki [Azure monitor dziennikami](../azure-monitor/platform/data-platform-logs.md), które zastąpiły termin log Analytics. Dane dziennika nadal są przechowywane w obszarze roboczym Log Analytics i są nadal zbierane i analizowane przez tę samą usługę Log Analytics. Aby uzyskać więcej informacji, zobacz [Azure monitor terminologia](../azure-monitor/terminology.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem potrzebny jest obszar roboczy Log Analytics. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Włącz rejestrowanie dla nowych aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)Utwórz aplikację logiki. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób** >  > **aplikacja logiki**.

   ![Utwórz nową aplikację logiki](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. W obszarze **aplikacja logiki**wykonaj następujące kroki:

   1. Podaj nazwę aplikacji logiki i wybierz subskrypcję platformy Azure.

   1. Utwórz lub wybierz grupę zasobów platformy Azure. Wybierz lokalizację aplikacji logiki.

   1. W obszarze **log Analytics**wybierz pozycję **włączone**.

   1. Z listy **obszarów roboczych log Analytics** wybierz obszar roboczy, w którym chcesz wysłać dane z aplikacji logiki.

      ![Podawanie informacji na temat aplikacji logiki](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Po zakończeniu tego kroku platforma Azure utworzy aplikację logiki, która jest teraz skojarzona z obszarem roboczym Log Analytics. Ten krok powoduje również automatyczne zainstalowanie rozwiązania do zarządzania Logic Apps w obszarze roboczym.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Aby wyświetlić uruchomienia aplikacji logiki, [wykonaj te kroki](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Zainstaluj Logic Apps rozwiązanie do zarządzania

Jeśli podczas tworzenia aplikacji logiki masz już włączone dzienniki Azure Monitor, Pomiń ten krok. Masz już zainstalowaną Logic Apps rozwiązanie do zarządzania.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania Znajdź pozycję "obszary robocze usługi log Analytics" i wybierz pozycję **log Analytics obszary robocze**.

   ![Wybierz pozycję "Log Analytics obszary robocze"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. W obszarze **obszary robocze log Analytics**wybierz swój obszar roboczy.

   ![Wybierz obszar roboczy Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. W okienku Przegląd w obszarze **wprowadzenie do log Analytics** > **konfigurowania rozwiązań monitorowania**wybierz pozycję **Wyświetl rozwiązania**.

   ![Wybierz pozycję "Wyświetl rozwiązania"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. W obszarze **Przegląd**wybierz pozycję **Dodaj**.

   ![Wybierz pozycję "Dodaj"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. Po otwarciu **portalu Marketplace** w polu wyszukiwania wprowadź "Logic Apps Management" i wybierz pozycję **Zarządzanie Logic Apps**.

   ![Wybierz pozycję "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. W okienku opis rozwiązania wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Utwórz", aby utworzyć "Logic Apps zarządzanie"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Przejrzyj i Potwierdź obszar roboczy Log Analytics, w którym chcesz zainstalować rozwiązanie, a następnie wybierz pozycję **Utwórz** ponownie.

   ![Wybierz pozycję "Utwórz", aby utworzyć "Logic Apps zarządzanie"](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Gdy platforma Azure wdroży rozwiązanie w grupie zasobów platformy Azure, która zawiera obszar roboczy Log Analytics, rozwiązanie zostanie wyświetlone w okienku podsumowania obszaru roboczego.

   ![Okienko podsumowania obszaru roboczego](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Wyświetl informacje o uruchomieniu aplikacji logiki

Po uruchomieniu aplikacji logiki można wyświetlić stan i licznik dla tych przebiegów na kafelku **zarządzanie Logic Apps** .

1. Przejdź do obszaru roboczego log Analytics i wybierz > opcję **Podsumowanie obszaru roboczego** **Logic Apps zarządzanie**.

   ![Stan i liczba uruchomień aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   W tym miejscu są uruchamiane aplikacje logiki pogrupowane według nazwy lub stanu wykonania. Ta strona zawiera także szczegółowe informacje o błędach w akcjach lub wyzwalaczach dla uruchomienia aplikacji logiki.

   ![Podsumowanie stanu dla uruchomionych aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Aby wyświetlić wszystkie uruchomienia dla określonej aplikacji logiki lub stanu, wybierz wiersz aplikacji logiki lub stanu.

   Oto przykład, w którym są wyświetlane wszystkie uruchomienia dla określonej aplikacji logiki:

   ![Wyświetlanie przebiegów dla aplikacji logiki lub stanu](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Ta strona zawiera następujące zaawansowane opcje:

   * **Śledzone właściwości:**

     Ta kolumna zawiera śledzone właściwości, które są pogrupowane według akcji dla aplikacji logiki. Aby wyświetlić śledzone właściwości, wybierz pozycję **Widok**. Aby wyszukać śledzone właściwości, Użyj filtru kolumn.

     ![Wyświetlanie śledzonych właściwości aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Wszystkie nowo dodane właściwości śledzone mogą upłynąć od 10-15 minut, zanim pojawią się po raz pierwszy. Dowiedz się [, jak dodać śledzone właściwości do aplikacji logiki](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Ponownie prześlij** Można ponownie przesłać co najmniej jeden przebieg aplikacji logiki, który zakończył się niepowodzeniem lub nadal działa. Zaznacz pola wyboru dla przebiegów, które chcesz ponownie przesłać, a następnie wybierz pozycję **Prześlij ponownie**.

     ![Prześlij ponownie uruchomienia aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Aby odfiltrować te wyniki, można wykonać filtrowanie po stronie klienta i po stronie serwera.

   * **Filtr po stronie klienta**: Dla każdej kolumny wybierz odpowiednie filtry, na przykład:

     ![Przykładowe filtry kolumn](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtr po stronie serwera**: Aby wybrać określony przedział czasu lub ograniczyć liczbę uruchomionych uruchomień, użyj kontrolki zakres w górnej części strony. Domyślnie w danym momencie są wyświetlane tylko 1 000 rekordów.

     ![Zmień przedział czasu](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Aby wyświetlić wszystkie akcje i ich szczegóły dotyczące określonego uruchomienia, wybierz wiersz dla uruchomienia aplikacji logiki.

   Oto przykład pokazujący wszystkie akcje dla określonego uruchomienia aplikacji logiki:

   ![Wyświetl akcje dla uruchomienia aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. Na dowolnej stronie wyników, aby wyświetlić zapytanie za wyniki lub zobaczyć wszystkie wyniki, wybierz pozycję **Zobacz wszystko**, co spowoduje otwarcie strony Wyszukiwanie w dziennikach.

   ![Zobacz wszystkie na stronach wyników](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)

   Na stronie przeszukiwanie dzienników można wybrać następujące opcje:

   * Aby wyświetlić wyniki zapytania w tabeli, wybierz pozycję **tabela**.

   * Aby zmienić zapytanie, można edytować ciąg zapytania na pasku wyszukiwania. Aby uzyskać lepsze środowisko, wybierz pozycję **Analiza**zaawansowana.

     ![Wyświetl akcje i szczegóły dla uruchomienia aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)

     Na stronie usługi log Analytics można aktualizować zapytania i przeglądać wyniki z tabeli. To zapytanie używa [języka zapytań Kusto](https://aka.ms/LogAnalyticsLanguageReference), który można edytować, jeśli chcesz wyświetlić różne wyniki.

     ![Log Analytics — widok zapytania](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie komunikatów B2B](../logic-apps/logic-apps-monitor-b2b-message.md)