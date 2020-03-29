---
title: Monitorowanie stanu, historia wyświetlania i konfigurowanie alertów
description: Rozwiązywanie problemów z aplikacjami logiki przez sprawdzanie stanu uruchamiania, przeglądanie historii wyzwalaczy i włączanie alertów w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907775"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitorowanie stanu uruchamiania, przeglądanie historii wyzwalaczy i konfigurowanie alertów dla aplikacji Azure Logic Apps

Po [utworzeniu i uruchomieniu aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)można sprawdzić stan uruchomienia tej aplikacji logiki, [uruchomić historię,](#review-runs-history) [historię wyzwalaczy](#review-trigger-history)i wydajność. Aby otrzymywać powiadomienia o awariach lub innych możliwych [problemach, skonfiguruj alerty](#add-azure-alerts). Na przykład można utworzyć alert, który wykrywa "gdy więcej niż pięć uruchomień kończy się niepowodzeniem w ciągu godziny."

Aby monitorować zdarzenia w czasie rzeczywistym i bogatsze debugowanie, skonfiguruj rejestrowanie diagnostyki dla aplikacji logiki przy użyciu [dzienników usługi Azure Monitor.](../azure-monitor/overview.md) Ta usługa platformy Azure ułatwia monitorowanie środowiska chmury i środowiska lokalnego, dzięki czemu można łatwiej zachować ich dostępność i wydajność. Następnie można znaleźć i wyświetlić zdarzenia, takie jak zdarzenia wyzwalania, uruchamianie zdarzeń i zdarzenia akcji. Przechowując te informacje w [dziennikach usługi Azure Monitor,](../azure-monitor/platform/data-platform-logs.md)można utworzyć [zapytania dziennika,](../azure-monitor/log-query/log-query-overview.md) które ułatwiają znajdowanie i analizowanie tych informacji. Te dane diagnostyczne można również używać z innymi usługami platformy Azure, takimi jak Usługa Azure Storage i usługi Azure Event Hubs. Aby uzyskać więcej informacji, zobacz [Monitorowanie aplikacji logiki przy użyciu usługi Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Historia przebiegów recenzji

Za każdym razem, gdy wyzwalacz jest uruchamiany dla elementu lub zdarzenia, aparat aplikacji logiki tworzy i uruchamia oddzielne wystąpienie przepływu pracy dla każdego elementu lub zdarzenia. Domyślnie każde wystąpienie przepływu pracy jest uruchamiane równolegle, dzięki czemu żaden przepływ pracy nie musi czekać przed rozpoczęciem uruchamiania. Można przejrzeć, co się stało podczas tego uruchomienia, w tym stan dla każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe dla każdego kroku.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz aplikację logiki w projektancie aplikacji logiki.

   Aby znaleźć aplikację logiki , w głównym `logic apps`polu wyszukiwania platformy Azure, wprowadź , a następnie wybierz **pozycję Aplikacje logiki**.

   ![Znajdowanie i wybieranie usługi "Aplikacje logiki"](./media/monitor-logic-apps/find-your-logic-app.png)

   Witryna Azure portal zawiera wszystkie aplikacje logiki, które są skojarzone z subskrypcji platformy Azure. Tę listę można filtrować na podstawie nazwy, subskrypcji, grupy zasobów, lokalizacji itd.

   ![Wyświetlanie aplikacji logiki skojarzonych z subskrypcjami](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Wybierz aplikację logiki, a następnie wybierz pozycję **Przegląd**.

   W okienku przeglądu w obszarze **Historia przebiegów**są wyświetlane wszystkie przebiegi przeszłych, bieżących i oczekujących dla aplikacji logiki. Jeśli na liście jest wyświetlanych wiele przebiegów i nie możesz znaleźć odpowiedniego wpisu, spróbuj przefiltrować listę. Jeśli nie znajdziesz oczekiwanych danych, spróbuj wybrać **pozycję Odśwież** na pasku narzędzi.

   ![Omówienie, uruchamianie historii i inne informacje o aplikacji logiki](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Oto możliwe stany uruchamiania aplikacji logiki:

   | Stan | Opis |
   |--------|-------------|
   | **Anulowano** | Przepływ pracy został uruchomiony, ale odebrano żądanie anulowania |
   | **Niepowodzenie** | Co najmniej jedna akcja nie powiodła się, a nie skonfigurowano żadnych późniejszych akcji w przepływie pracy w celu obsługi błędu |
   | **Uruchomiono** | Przepływ pracy jest aktualnie uruchomiony. <p>Ten stan może również pojawić się dla ograniczonych przepływów pracy lub z powodu bieżącego planu cenowego. Aby uzyskać więcej informacji, zobacz [limity akcji na stronie cennika](https://azure.microsoft.com/pricing/details/logic-apps/). Po [skonfigurowaniu rejestrowania diagnostyki](../logic-apps/monitor-logic-apps.md)można uzyskać informacje o zdarzeniach przepustnicy, które się zdarzają. |
   | **Powodzenie** | Wszystkie działania zakończyły się sukcesem. <p>**Uwaga:** Jeśli wystąpiły błędy w określonej akcji, późniejsza akcja w przepływie pracy obsłużyła ten błąd. |
   | **Oczekiwanie** | Przepływ pracy nie został uruchomiony lub został wstrzymany, na przykład z powodu wcześniejszego przepływu pracy, który jest nadal uruchomiony. |
   |||

1. Aby przejrzeć kroki i inne informacje dotyczące określonego przebiegu, w obszarze **Historia przebiegów**wybierz ten bieg.

   ![Wybierz określony przebieg do przejrzenia](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   Okienko **uruchamiania aplikacji Logika** pokazuje każdy krok w wybranym przebiegu, stan uruchomienia każdego kroku i czas wykonany dla każdego kroku do uruchomienia, na przykład:

   ![Każda akcja w określonym przebiegu](./media/monitor-logic-apps/logic-app-run-pane.png)

   Aby wyświetlić te informacje w formularzu listy, na pasku narzędzi **Uruchamianie aplikacji Logika** wybierz pozycję **Uruchom szczegóły**.

   ![Na pasku narzędzi wybierz "Uruchom szczegóły"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   Widok Uruchom szczegóły pokazuje każdy krok, ich stan i inne informacje.

   ![Przejrzyj szczegóły dotyczące każdego kroku w biegu](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Na przykład można uzyskać właściwość **identyfikator korelacji** uruchomienia, która może być potrzebna podczas korzystania z [interfejsu API REST dla aplikacji logiki.](https://docs.microsoft.com/rest/api/logic)

1. Aby uzyskać więcej informacji o określonym kroku, wybierz jedną z opcji:

   * W okienku **uruchamiania aplikacji Logika** wybierz krok, aby kształt się rozwinął. Teraz można wyświetlać informacje, takie jak dane wejściowe, wyjścia i wszelkie błędy, które wystąpiły w tym kroku, na przykład:

     ![W okienku uruchamiania aplikacji logiki wyświetl krok nie powiódł się](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * W okienku **szczegółów uruchamiania aplikacji logiki** wybierz odpowiedni krok.

     ![W okienku szczegółów uruchamiania wyświetl krok nie powiódł się](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Teraz można wyświetlać informacje, takie jak dane wejściowe i wyjściowe dla tego kroku, na przykład:

   > [!NOTE]
   > Wszystkie szczegóły środowiska uruchomieniowego i zdarzenia są szyfrowane w ramach usługi Logic Apps. Są one odszyfrowywane tylko wtedy, gdy użytkownik żąda wyświetlenia tych danych. Można [ukryć dane wejściowe i wyjściowe w historii uruchamiania](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) lub kontrolować dostęp użytkowników do tych informacji za pomocą [kontroli dostępu opartej na rolach platformy Azure (RBAC)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Przeglądanie historii wyzwalaczy

Każde uruchomienie aplikacji logiki rozpoczyna się od wyzwalacza. Historia wyzwalaczy zawiera listę wszystkich prób wyzwalacza wykonanych przez aplikację logiki oraz informacje o danych wejściowych i wyjściowych dla każdej próby wyzwalacza.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz aplikację logiki w projektancie aplikacji logiki.

   Aby znaleźć aplikację logiki , w głównym `logic apps`polu wyszukiwania platformy Azure, wprowadź , a następnie wybierz **pozycję Aplikacje logiki**.

   ![Znajdowanie i wybieranie usługi "Aplikacje logiki"](./media/monitor-logic-apps/find-your-logic-app.png)

   Witryna Azure portal zawiera wszystkie aplikacje logiki, które są skojarzone z subskrypcji platformy Azure. Tę listę można filtrować na podstawie nazwy, subskrypcji, grupy zasobów, lokalizacji itd.

   ![Wyświetlanie aplikacji logiki skojarzonych z subskrypcjami](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Wybierz aplikację logiki, a następnie wybierz pozycję **Przegląd**.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. W sekcji **Podsumowanie** w obszarze **Ocena**wybierz pozycję **Zobacz historię wyzwalaczy**.

   ![Wyświetlanie historii wyzwalaczy aplikacji logiki](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Okienko historii wyzwalaczy pokazuje wszystkie próby wyzwalania, które zostały wykonane przez aplikację logiki. Za każdym razem, gdy wyzwalacz jest uruchamiany dla elementu lub zdarzenia, aparat aplikacji logiki tworzy oddzielne wystąpienie aplikacji logiki, która uruchamia przepływ pracy. Domyślnie każde wystąpienie jest uruchamiane równolegle, dzięki czemu żaden przepływ pracy nie musi czekać przed rozpoczęciem uruchamiania. Więc jeśli aplikacja logiki wyzwala na wiele elementów w tym samym czasie, wpis wyzwalacza z tej samej daty i godziny pojawia się dla każdego elementu.

   ![Wiele prób wyzwalania dla różnych elementów](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Oto możliwe stany próby wyzwalacza:

   | Stan | Opis |
   |--------|-------------|
   | **Niepowodzenie** | Wystąpił błąd. Aby przejrzeć wszystkie wygenerowane komunikaty o **błędach**dla wyzwalacza, wybierz tę próbę wyzwalacza i wybierz pozycję Wyjścia . Na przykład można znaleźć dane wejściowe, które nie są prawidłowe. |
   | **Pominięto** | Wyzwalacz sprawdził punkt końcowy, ale nie znalazł żadnych danych. |
   | **Powodzenie** | Wyzwalacz sprawdził punkt końcowy i znalazł dostępne dane. Zazwyczaj obok tego stanu pojawia się również stan "Zwolniony". Jeśli nie, definicja wyzwalacza `SplitOn` może mieć warunek lub polecenie, które nie zostały spełnione. <p>Ten stan można zastosować do ręcznego wyzwalacza, wyzwalacza cyklu lub wyzwalacza sondowania. Wyzwalacz można uruchomić pomyślnie, ale sam przebieg może nadal zakończyć się niepowodzeniem, gdy akcje generują błędy nieobsługiwalne. |
   |||

   > [!TIP]
   > Wyzwalacz można ponownie sprawdzić bez oczekiwania na następny cykl. Na pasku narzędzi przeglądu wybierz pozycję **Uruchom wyzwalacz**i wybierz wyzwalacz, który wymusza sprawdzenie. Możesz też wybrać **pozycję Uruchom** na pasku narzędzi Projektanta aplikacji logiki.

1. Aby wyświetlić informacje o określonej próbie wyzwalacza, w okienku wyzwalacza wybierz zdarzenie wyzwalacza. Jeśli na liście jest wiele prób wyzwalania i nie możesz znaleźć odpowiedniego wpisu, spróbuj przefiltrować listę. Jeśli nie znajdziesz oczekiwanych danych, spróbuj wybrać **pozycję Odśwież** na pasku narzędzi.

   ![Wyświetlanie określonej próby wyzwalania](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Teraz można przeglądać informacje o wybranym zdarzeniu wyzwalającym, na przykład:

   ![Wyświetlanie określonych informacji o wyzwalaczu](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Konfigurowanie alertów monitorowania

Aby otrzymywać alerty na podstawie określonych metryk lub przekroczonych progów dla aplikacji logiki, [skonfiguruj alerty w usłudze Azure Monitor.](../azure-monitor/platform/alerts-overview.md) Dowiedz się więcej o [metrykach na platformie Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Aby skonfigurować alerty bez korzystania z [usługi Azure Monitor,](../log-analytics/log-analytics-overview.md)wykonaj następujące kroki.

1. W menu aplikacji logiki w obszarze **Monitorowanie**wybierz pozycję **Alerty** > **Nowa reguła alertu**.

   ![Dodawanie alertu dla aplikacji logiki](./media/monitor-logic-apps/add-new-alert-rule.png)

1. W okienku **Tworzenie reguły** w obszarze **Zasób**wybierz aplikację logiki, jeśli nie została jeszcze wybrana. W obszarze **Warunek**wybierz **pozycję Dodaj,** aby można było zdefiniować warunek wyzwalający alert.

   ![Dodawanie warunku reguły](./media/monitor-logic-apps/add-condition-for-rule.png)

1. W okienku **logiki konfiguruj sygnał** znajdź i wybierz sygnał, dla którego chcesz uzyskać alert. Możesz użyć pola wyszukiwania lub posortować sygnały alfabetycznie, wybierz nagłówek kolumny **Nazwa sygnału.**

   Na przykład jeśli chcesz wysłać alert, gdy wyzwalacz nie powiedzie się, wykonaj następujące kroki:

   1. W kolumnie **Nazwa sygnału** znajdź i wybierz sygnał **Wyzwalacze nie powiodło się.**

      ![Wybieranie sygnału do tworzenia alertu](./media/monitor-logic-apps/find-and-select-signal.png)

   1. W okienku informacyjnym, które otwiera się dla wybranego sygnału, w obszarze **Logika alertu**skonfiguruj warunek, na przykład:

   1. W obszarze **Operator**wybierz **opcję Większa lub równa**.

   1. W przypadku **typu Agregacja**wybierz pozycję **Zlicz .**

   1. W polu Wartość `1` **progowa**wprowadź .

   1. W **obszarze Podgląd stanu**upewnij się, że stan jest poprawny.

   1. W obszarze **Oceniane na podstawie**, ustaw interwał i częstotliwość uruchamiania reguły alertu. W przypadku **szczegółowości agregacji (Okres)** wybierz okres grupowania danych. W obszarze **Częstotliwość oceny**wybierz, jak często chcesz sprawdzić stan.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Gotowe**.

   Oto gotowy warunek:

   ![Konfigurowanie warunku alertu](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Strona **Reguła utwórz** pokazuje teraz warunek, który został utworzony, i koszt uruchomienia tego alertu.

   ![Nowy alert na stronie "Utwórz regułę"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Określ nazwę, opcjonalny opis i poziom ważności alertu. Po wyłącz włącz **regułę po utworzeniu** lub wyłącz, dopóki nie będziesz gotowy do włączenia reguły.

1. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**.

> [!TIP]
> Aby uruchomić aplikację logiki z alertu, można dołączyć [wyzwalacz żądania](../connectors/connectors-native-reqres.md) w przepływie pracy, który umożliwia wykonywanie zadań, takich jak następujące przykłady:
> 
> * [Opublikuj w Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Wysyłanie wiadomości SMS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Dodawanie wiadomości do kolejki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Następne kroki

* [Monitoruj aplikacje logiki przy użyciu usługi Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)