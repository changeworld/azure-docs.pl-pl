---
title: Monitorowanie komunikatów B2B przy użyciu usługi Azure Monitor
description: Rozwiązywanie problemów z komunikatami AS2, X12 i EDIFACT przez konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907983"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla komunikatów B2B w usłudze Azure Logic Apps

Po skonfigurowaniu komunikacji B2B między partnerami handlowymi na koncie integracji partnerzy ci mogą wymieniać wiadomości przy użyciu protokołów, takich jak AS2, X12 i EDIFACT. Aby sprawdzić, czy ta komunikacja działa zgodnie z oczekiwaniami, można skonfigurować [dzienniki usługi Azure Monitor](../azure-monitor/platform/data-platform-logs.md) dla konta integracji. [Usługa Azure Monitor](../azure-monitor/overview.md) ułatwia monitorowanie środowiska chmurowego i lokalnego, dzięki czemu można łatwiej zachować ich dostępność i wydajność. Korzystając z dzienników usługi Azure Monitor, można rejestrować i przechowywać dane dotyczące danych i zdarzeń środowiska wykonawczego, takich jak zdarzenia wyzwalania, uruchamianie zdarzeń i zdarzenia akcji w [obszarze roboczym usługi Log Analytics.](../azure-monitor/platform/resource-logs-collect-workspace.md) W przypadku wiadomości rejestrowanie zbiera również informacje, takie jak:

* Liczba i stan wiadomości
* Stan podziękowań
* Korelacje między wiadomościami i potwierdzeniami
* Szczegółowe opisy błędów dla awarii

Usługa Azure Monitor umożliwia tworzenie [zapytań dziennika ułatwiające znajdowanie](../azure-monitor/log-query/log-query-overview.md) i przeglądanie tych informacji. Można również [użyć tych danych diagnostycznych z innymi usługami platformy Azure,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)takimi jak Usługa Azure Storage i usługi Azure Event Hubs.

Aby skonfigurować rejestrowanie dla konta integracji, [zainstaluj rozwiązanie Logic Apps B2B](#install-b2b-solution) w witrynie Azure portal. To rozwiązanie zawiera zagregowane informacje dotyczące zdarzeń komunikatów B2B. Następnie, aby włączyć rejestrowanie i tworzenie zapytań dotyczących tych informacji, należy skonfigurować [dzienniki usługi Azure Monitor](#set-up-resource-logs).

W tym artykule pokazano, jak włączyć rejestrowanie usługi Azure Monitor dla konta integracji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Jeśli nie masz obszaru roboczego usługi Log Analytics, dowiedz się, [jak utworzyć obszar roboczy usługi Log Analytics.](../azure-monitor/learn/quick-create-workspace.md)

* Aplikacja logiki, która jest skonfigurowana przy użyciu rejestrowania usługi Azure Monitor i wysyła te informacje do obszaru roboczego usługi Log Analytics. Dowiedz [się, jak skonfigurować dzienniki usługi Azure Monitor dla aplikacji logiki.](../logic-apps/monitor-logic-apps.md)

* Konto integracji, które jest połączone z aplikacją logiki. Dowiedz [się, jak połączyć konto integracji z aplikacją logiki](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Zainstaluj rozwiązanie Logic Apps B2B

Zanim dzienniki usługi Azure Monitor będą mogły śledzić komunikaty B2B dla aplikacji logiki, dodaj rozwiązanie **Logic Apps B2B** do obszaru roboczego usługi Log Analytics.

1. W polu wyszukiwania [portalu Azure](https://portal.azure.com) `log analytics workspaces`wprowadź , a następnie wybierz **obszary robocze usługi Log Analytics**.

   ![Wybierz "Obszary robocze analizy dzienników"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. W obszarze **Obszary robocze usługi Log Analytics**wybierz obszar roboczy.

   ![Wybieranie obszaru roboczego usługi Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. W okienku Przegląd w obszarze **Wprowadzenie do usługi Log Analytics** > **konfigurowanie rozwiązań monitorujących**wybierz pozycję **Wyświetl rozwiązania**.

   ![W okienku Przegląd wybierz "Wyświetl rozwiązania"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. W okienku Przegląd wybierz pozycję **Dodaj**.

   ![W okienku przeglądu dodaj nowe rozwiązanie](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Po otwarciu **portalu Marketplace** w `logic apps b2b`polu wyszukiwania wprowadź pozycję , i wybierz **pozycję Logic Apps B2B**.

   ![W marketplace wybierz "Logic Apps Management"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. W okienku opisu rozwiązania wybierz pozycję **Utwórz**.

   ![Wybierz "Utwórz", aby dodać rozwiązanie "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Przejrzyj i potwierdź obszar roboczy usługi Log Analytics, w którym chcesz zainstalować rozwiązanie, i wybierz pozycję **Utwórz** ponownie.

   ![Wybierz "Utwórz" dla "Aplikacje logiki B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Po wdrożeniu rozwiązania do grupy zasobów platformy Azure, która zawiera obszar roboczy usługi Log Analytics, rozwiązanie pojawi się w okienku podsumowania obszaru roboczego. Podczas przetwarzania wiadomości B2B liczba wiadomości w tym okienku jest aktualizowana.

   ![Okienko podsumowania obszaru roboczego](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurowanie dzienników usługi Azure Monitor

Można włączyć rejestrowanie usługi Azure Monitor bezpośrednio z konta integracji.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz swoje konto integracji.

   ![Znajdowanie i wybieranie konta integracyjnego](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. W menu konta integracyjnego w obszarze **Monitorowanie**wybierz pozycję **Ustawienia diagnostyczne**. Wybierz **pozycję Dodaj ustawienie diagnostyczne**.

   ![W sekcji "Monitorowanie" wybierz "Ustawienia diagnostyki"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Aby utworzyć to ustawienie, wykonaj następujące czynności:

   1. Podaj nazwę tego ustawienia.

   1. Wybierz **pozycję Wyślij do analizy dzienników**.

   1. W przypadku **subskrypcji**wybierz subskrypcję platformy Azure skojarzoną z obszarem roboczym usługi Log Analytics.

   1. W obszarze **roboczym usługi Log Analytics**wybierz obszar roboczy, którego chcesz użyć.

   1. W obszarze **dziennik**wybierz kategorię **IntegrationAccountTrackingEvents,** która określa kategorię zdarzenia, którą chcesz zarejestrować.

   1. Po zakończeniu wybierz pozycję **Zapisz**.

   Przykład: 

   ![Konfigurowanie dzienników usługi Azure Monitor w celu zbierania danych diagnostycznych](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Wyświetl stan wiadomości

Po uruchomieniu aplikacji logiki można wyświetlić stan i dane dotyczące tych wiadomości w obszarze roboczym usługi Log Analytics.

1. W polu wyszukiwania [witryny Azure portal](https://portal.azure.com) znajdź i otwórz obszar roboczy usługi Log Analytics.

1. W menu obszaru roboczego wybierz pozycję **Podsumowanie** > obszaru roboczego**Aplikacje logiki B2B**.

   ![Okienko podsumowania obszaru roboczego](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Jeśli kafelek Logic Apps B2B nie pokazuje od razu wyników po uruchomieniu, spróbuj wybrać **opcję Odśwież** lub poczekaj przez krótki czas, zanim spróbuj ponownie.

   Domyślnie **kafelek Logic Apps B2B** zawiera dane na podstawie jednego dnia. Aby zmienić zakres danych na inny interwał, wybierz kontrolkę zakresu u góry strony:

   ![Interwał zmian](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Po wyświetlenia pulpitu nawigacyjnego stanu wiadomości można wyświetlić więcej szczegółów dla określonego typu wiadomości, który pokazuje dane na podstawie jednego dnia. Wybierz kafelek dla **AS2**, **X12**lub **EDIFACT**.

   ![Wyświetlanie stanów wiadomości](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Zostanie wyświetlona lista wiadomości dla wybranego kafelka. Oto na przykład, jak może wyglądać lista komunikatów AS2:

   ![Stany i szczegóły dotyczące wiadomości AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Aby dowiedzieć się więcej o właściwościach dla każdego typu wiadomości, zobacz następujące opisy właściwości wiadomości:

   * [Właściwości komunikatu AS2](#as2-message-properties)
   * [Właściwości wiadomości X12](#x12-message-properties)
   * [Właściwości komunikatu EDIFACT](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Opisy właściwości i formaty nazw komunikatów AS2, X12 i EDIFACT

Dla każdego typu wiadomości oto opisy właściwości i formaty nazw pobranych plików wiadomości.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Opisy właściwości komunikatu AS2

Oto opisy właściwości dla każdej wiadomości AS2.

| Właściwość | Opis |
|----------|-------------|
| **Nadawca** | Partner gościa określony w **ustawieniach odbierania**lub partner hosta określony w **ustawieniach wysyłania** dla umowy AS2 |
| **Odbiornik** | Partner hosta określony w **ustawieniach odbierania**lub partner gościa określony w **ustawieniach wysyłania** dla umowy AS2 |
| **Aplikacja logiki** | Aplikacja logiki, w której są skonfigurowane akcje AS2 |
| **Stan** | Stan wiadomości AS2 <br>Powodzenie = Odebrano lub wysłano prawidłową wiadomość AS2. Nie jest skonfigurowany numer MDN. <br>Powodzenie = Odebrano lub wysłano prawidłową wiadomość AS2. MDN jest skonfigurowany i odebrany lub mdn jest wysyłany. <br>Nie powiodło się = Odebrano nieprawidłową wiadomość AS2. Nie jest skonfigurowany numer MDN. <br>Oczekujące = Odebrano lub wysłano prawidłową wiadomość AS2. MDN jest skonfigurowany i mdn oczekuje. |
| **Ack** | Stan wiadomości MDN <br>Zaakceptowane = Odebrano lub wysłane dodatni mdn. <br>Oczekujące = oczekiwanie na odbiór lub wysłanie mdn. <br>Odrzucone = Odebrano lub wysłano ujemną nazwę MDN. <br>Nie wymagane = MDN nie jest skonfigurowany w umowie. |
| **Kierunek** | Kierunek komunikatu AS2 |
| **Identyfikator śledzenia** | Identyfikator, który koreluje wszystkie wyzwalacze i akcje w aplikacji logiki |
| **Identyfikator komunikatu** | Identyfikator wiadomości AS2 z nagłówków wiadomości AS2 |
| **Sygnatura czasowa** | Czas, w którym akcja AS2 przetworzyła wiadomość |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Opisy właściwości komunikatu X12

Oto opisy właściwości dla każdego komunikatu X12.

| Właściwość | Opis |
|----------|-------------|
| **Nadawca** | Partner gościa określony w **ustawieniach odbierania**lub partner hosta określony w **ustawieniach wysyłania** dla umowy X12 |
| **Odbiornik** | Partner hosta określony w **ustawieniach odbierania**lub partner gościa określony w **ustawieniach wysyłania** dla umowy X12 |
| **Aplikacja logiki** | Aplikacja logiki, w której są skonfigurowane akcje X12 |
| **Stan** | Stan wiadomości X12 <br>Powodzenie = Odebrano lub wysłano prawidłową wiadomość X12. Nie jest skonfigurowany funkcjonalny ack. <br>Powodzenie = Odebrano lub wysłano prawidłową wiadomość X12. Funkcjonalne ack jest skonfigurowany i odbierany, lub funkcjonalna ack jest wysyłany. <br>Nie powiodło się = Odebrano lub wysłano nieprawidłową wiadomość X12. <br>Oczekujące = Odebrano lub wysłano prawidłową wiadomość X12. Funkcjonalna ack jest skonfigurowana i oczekuje się funkcjonalnego ack. |
| **Ack** | Stan funkcjonalnego Ack (997) <br>Zaakceptowane = Odebrano lub wysłane pozytywne funkcjonalne ack. <br>Odrzucone = Odebrano lub wysłane negatywne ack funkcjonalne. <br>Oczekujące = Oczekiwanie ack funkcjonalne, ale nie odebrane. <br>Oczekujące = Wygenerował funkcjonalne ack, ale nie można wysłać do partnera. <br>Nie wymagane = Funkcjonalne ack nie jest skonfigurowany. |
| **Kierunek** | Kierunek wiadomości X12 |
| **Identyfikator śledzenia** | Identyfikator, który koreluje wszystkie wyzwalacze i akcje w aplikacji logiki |
| **Typ msg** | Typ wiadomości EDI X12 |
| **Icn** | Numer kontrolny wymiany dla komunikatu X12 |
| **Tscn (tscn)** | Numer kontroli zestawu transakcji dla wiadomości X12 |
| **Sygnatura czasowa** | Czas, w którym akcja X12 przetworzyła wiadomość |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Opisy właściwości komunikatu EDIFACT

Oto opisy właściwości dla każdego komunikatu EDIFACT.

| Właściwość | Opis |
|----------|-------------|
| **Nadawca** | Partner gościa określony w **ustawieniach odbierania**lub partner hosta określony w **ustawieniach wysyłania** dla umowy EDIFACT |
| **Odbiornik** | Partner hosta określony w **ustawieniach odbierania**lub partner gościa określony w **ustawieniach wysyłania** dla umowy EDIFACT |
| **Aplikacja logiki** | Aplikacja logiki, w której są skonfigurowane akcje EDIFACT |
| **Stan** | Stan komunikatu EDIFACT <br>Sukces = Odebrano lub wysłano prawidłową wiadomość EDIFACT. Nie jest skonfigurowany funkcjonalny ack. <br>Sukces = Odebrano lub wysłano prawidłową wiadomość EDIFACT. Funkcjonalne ack jest skonfigurowany i odbierany, lub funkcjonalna ack jest wysyłany. <br>Nie powiodło się = Odebrano lub wysłano nieprawidłową wiadomość EDIFACT <br>Oczekujące = Odebrano lub wysłano prawidłową wiadomość EDIFACT. Funkcjonalna ack jest skonfigurowana i oczekuje się funkcjonalnego ack. |
| **Ack** | Stan Ack funkcjonalnego (CONTRL) <br>Zaakceptowane = Odebrano lub wysłane pozytywne funkcjonalne ack. <br>Odrzucone = Odebrano lub wysłane negatywne ack funkcjonalne. <br>Oczekujące = Oczekiwanie ack funkcjonalne, ale nie odebrane. <br>Oczekujące = Wygenerował funkcjonalne ack, ale nie można wysłać do partnera. <br>Nie wymagane = Funkcjonalny Ack nie jest skonfigurowany. |
| **Kierunek** | Kierunek komunikatu EDIFACT |
| **Identyfikator śledzenia** | Identyfikator, który koreluje wszystkie wyzwalacze i akcje w aplikacji logiki |
| **Typ msg** | Typ komunikatu EDIFACT |
| **Icn** | Numer kontroli wymiany dla komunikatu EDIFACT |
| **Tscn (tscn)** | Numer kontroli zestawu transakcji dla komunikatu EDIFACT |
| **Sygnatura czasowa** | Czas, w którym akcja EDIFACT przetworzyła komunikat |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zapytań monitorowania i śledzenia](../logic-apps/create-monitoring-tracking-queries.md)