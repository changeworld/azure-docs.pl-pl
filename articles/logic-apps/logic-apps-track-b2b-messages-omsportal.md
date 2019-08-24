---
title: Śledzenie komunikatów B2B przy użyciu dzienników Azure Monitor — Azure Logic Apps | Microsoft Docs
description: Śledzenie komunikacji B2B dla kont integracji i Azure Logic Apps przy użyciu usługi Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 33c4efb2b783b5071513f069beac9cdf73c373a8
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997844"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Śledzenie komunikatów B2B przy użyciu dzienników usługi Azure Monitor

Po skonfigurowaniu komunikacji B2B między partnerami handlowymi na koncie integracji ci partnerzy mogą wymieniać komunikaty z protokołami, takimi jak AS2, X12 i EDIFACT. Aby sprawdzić, czy te komunikaty są przetwarzane prawidłowo, możesz śledzić te komunikaty za pomocą [dzienników Azure monitor](../log-analytics/log-analytics-overview.md). Można na przykład użyć tych funkcji śledzenia opartych na sieci Web do śledzenia komunikatów:

* Liczba komunikatów i stan
* Stan potwierdzeń
* Skorelowanie komunikatów z potwierdzeniami
* Szczegółowe opisy błędów dla niepowodzeń
* Możliwości wyszukiwania

> [!NOTE]
> Ta strona zawiera wcześniej procedurę wykonywania tych zadań z użyciem Microsoft Operations Management Suite (OMS), która jest [wycofywana w styczniu 2019](../azure-monitor/platform/oms-portal-transition.md), zastępuje te kroki przy użyciu usługi Azure log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja logiki, która jest skonfigurowana z rejestrowaniem diagnostyki. Dowiedz się, [jak utworzyć aplikację logiki](quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla tej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Konto integracji, które jest skonfigurowane z monitorowaniem i rejestrowaniem. Dowiedz się, [jak utworzyć konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [jak skonfigurować monitorowanie i rejestrowanie dla tego konta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Jeśli nie zostało to jeszcze zrobione, [Opublikuj dane diagnostyczne w dziennikach Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Po spełnieniu powyższych wymagań potrzebny jest również obszar roboczy Log Analytics, który służy do śledzenia komunikacji B2B przez Log Analytics. Jeśli nie masz obszaru roboczego Log Analytics, zapoznaj się [z tematem tworzenie log Analytics obszaru roboczego](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Zainstaluj Logic Apps B2B rozwiązanie

Aby dzienniki Azure Monitor śledzić wiadomości B2B dla aplikacji logiki, Dodaj rozwiązanie **Logic Apps B2B** do dzienników Azure monitor. Dowiedz się więcej [na temat dodawania rozwiązań do dzienników Azure monitor](../azure-monitor/learn/quick-create-workspace.md).

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania Znajdź pozycję "log Analytics" i wybierz pozycję **log Analytics**.

   ![Wybierz Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. W obszarze **log Analytics**Znajdź i wybierz obszar roboczy log Analytics. 

   ![Wybierz Log Analytics obszar roboczy](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. W obszarze **wprowadzenie do log Analytics** > **konfigurowania rozwiązań monitorowania**wybierz pozycję **Wyświetl rozwiązania**.

   ![Wybierz pozycję "Wyświetl rozwiązania"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Na stronie Przegląd wybierz pozycję **Dodaj**, co spowoduje otwarcie listy **rozwiązań do zarządzania** . Z tej listy wybierz pozycję **Logic Apps B2B**. 

   ![Wybierz Logic Apps B2B rozwiązanie](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Jeśli nie możesz znaleźć rozwiązania, w dolnej części listy wybierz pozycję **Załaduj więcej** do momentu wyświetlenia rozwiązania.

1. Wybierz pozycję **Utwórz**, potwierdź obszar roboczy log Analytics, w którym chcesz zainstalować rozwiązanie, a następnie wybierz pozycję **Utwórz** ponownie.   

   ![Wybierz pozycję "Utwórz", aby uzyskać Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Jeśli nie chcesz korzystać z istniejącego obszaru roboczego, możesz również utworzyć nowy obszar roboczy.

1. Gdy skończysz, Wróć do strony **Przegląd** obszaru roboczego. 

   Rozwiązanie Logic Apps B2B teraz pojawia się na stronie Przegląd. 
   Po przetworzeniu komunikatów B2B liczba komunikatów na tej stronie jest aktualizowana.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Wyświetlanie informacji o wiadomości B2B

Po przetworzeniu komunikatów B2B można wyświetlić stan i szczegóły tych komunikatów na kafelku **Logic Apps B2B** .

1. Przejdź do obszaru roboczego analizy logiki, a następnie otwórz stronę przegląd. Wybierz **Logic Apps B2B**.

   ![Liczba zaktualizowanych komunikatów](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Domyślnie na kafelku **Logic Apps B2B** są wyświetlane dane oparte na jednym dniu. Aby zmienić zakres danych na inny interwał, wybierz kontrolę zakresu w górnej części strony:
   > 
   > ![Zmień interwał](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Po wyświetleniu pulpitu nawigacyjnego stan komunikatu można wyświetlić więcej szczegółów dotyczących określonego typu wiadomości, który pokazuje dane na podstawie jednego dnia. Wybierz kafelek dla **AS2**, **X12**lub **EDIFACT**.

   ![Wyświetl stan komunikatu](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Zostanie wyświetlona lista komunikatów dla wybranego kafelka. 
   Aby dowiedzieć się więcej o właściwościach poszczególnych typów wiadomości, zobacz następujące opisy właściwości komunikatów:

   * [Właściwości komunikatu AS2](#as2-message-properties)
   * [Właściwości komunikatu X12](#x12-message-properties)
   * [Właściwości komunikatu EDIFACT](#EDIFACT-message-properties)

   Załóżmy na przykład, że lista komunikatów AS2 może wyglądać następująco:

   ![Wyświetlanie komunikatów AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Aby wyświetlić lub wyeksportować dane wejściowe i wyjściowe dla określonych wiadomości, wybierz te komunikaty i wybierz pozycję **Pobierz**. Po wyświetleniu monitu Zapisz plik zip na komputerze lokalnym, a następnie Wyodrębnij ten plik. 

   Wyodrębniony folder zawiera folder dla każdego wybranego komunikatu. 
   W przypadku skonfigurowania potwierdzeń folder wiadomości zawiera również pliki z informacjami o potwierdzeniu. 
   Każdy folder wiadomości ma co najmniej następujące pliki: 
   
   * Pliki z możliwością odczytu przez człowieka z danymi ładunku wejściowego i ładunku wyjściowego
   * Zakodowane pliki z danymi wejściowymi i wyjściowymi

   Dla każdego typu komunikatu można znaleźć w tym miejscu foldery i nazwy plików:

   * [AS2 — format nazw folderów i plików](#as2-folder-file-names)
   * [X12 — format nazw folderów i plików](#x12-folder-file-names)
   * [EDIFACT — format nazw folderów i plików](#edifact-folder-file-names)

   ![Pobierz pliki komunikatów](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Aby wyświetlić wszystkie akcje, które mają ten sam identyfikator uruchomienia, na stronie **przeszukiwanie dzienników** wybierz komunikat z listy komunikat.

   Można sortować te akcje według kolumny lub wyszukiwać określone wyniki.

   ![Akcje o tym samym IDENTYFIKATORze przebiegu](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Aby wyszukiwać wyniki przy użyciu wstępnie skompilowanych zapytań, wybierz pozycję **Ulubione**.

   * Dowiedz się [, jak tworzyć zapytania przez dodanie filtrów](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Lub Dowiedz się więcej o tym, [jak znaleźć dane za pomocą wyszukiwania w dziennikach w](../log-analytics/log-analytics-log-searches.md)dziennikach Azure monitor.

   * Aby zmienić zapytanie w polu wyszukiwania, zaktualizuj zapytanie przy użyciu kolumn i wartości, które mają być używane jako filtry.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Opisy właściwości i formaty nazw dla komunikatów AS2, X12 i EDIFACT

Dla każdego typu komunikatu poniżej przedstawiono opisy właściwości i formaty nazw pobranych plików wiadomości.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Opisy właściwości komunikatów AS2

Poniżej przedstawiono opisy właściwości dla każdego komunikatu AS2.

| Właściwość | Opis |
| --- | --- |
| Nadawcy | Partner gościa określony w **ustawieniach odbierania**lub partner hosta określony w polu **Wyślij ustawienia** dla umowy AS2 |
| Odbiornik | Partner hosta określony w obszarze **Ustawienia odbierania**lub partner gościa określony w polu **Wyślij ustawienia** dla umowy AS2 |
| Aplikacja logiki | Aplikacja logiki, w której są konfigurowane akcje AS2 |
| State | Stan komunikatu AS2 <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat AS2. Nie skonfigurowano żadnych powiadomienia MDN. <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat AS2. POWIADOMIENIA MDN jest skonfigurowany i odbierany lub wysyłany jest powiadomienia MDN. <br>Niepowodzenie = Odebrano nieprawidłowy komunikat AS2. Nie skonfigurowano żadnych powiadomienia MDN. <br>Oczekiwanie: Odebrano lub wysłano prawidłowy komunikat AS2. POWIADOMIENIA MDN jest skonfigurowany, a powiadomienia MDN jest oczekiwany. |
| Komunikaty | Stan komunikatu powiadomienia MDN <br>Zaakceptowano = odebrano lub wysłano pozytywny powiadomienia MDN. <br>Oczekiwanie = oczekiwanie na otrzymanie lub wysłanie powiadomienia MDN. <br>Odrzucono = odebrano lub wysłano ujemną powiadomienia MDN. <br>Niewymagane = powiadomienia MDN nie jest skonfigurowany w umowie. |
| Direction | Kierunek komunikatu AS2 |
| Identyfikator korelacji | Identyfikator, który jest skorelowany ze wszystkimi wyzwalaczami i akcjami w aplikacji logiki |
| Identyfikator komunikatu | Identyfikator komunikatu AS2 z nagłówków wiadomości AS2 |
| Timestamp | Godzina przetworzenia komunikatu przez akcję AS2 |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formaty nazw AS2 dla pobranych plików komunikatów

Poniżej przedstawiono formaty nazw pobranych plików i folderów wiadomości AS2.

| Folder lub plik | Format nazwy |
| :------------- | :---------- |
| Folder wiadomości | Sender \_\_[odbiornik]\_AS2\_[korelacja-identyfikator] [Identyfikator komunikatu] [timestamp] \_ |
| Dane wejściowe, wyjściowe i w przypadku konfiguracji, pliki potwierdzania | **Ładunek wejściowy**\_: [nadawca] [odbiornik\_]\_AS2 [korelacja\_-ID] input_payload. txt </p>**Ładunek wyjściowy**: [nadawca\_] [odbiornik\_]\_AS2 [korelacja-\_ID\_] Output ładunek. txt </p></p>**Dane wejściowe**: [nadawca]\_[odbiornik\_]\_AS2 [korelacja\_-ID] Inputs. txt </p></p>Dane **wyjściowe**: [nadawca]\_[odbiornik]\_AS2\_[korelacja\_-ID] Output. txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Opisy właściwości komunikatów X12

Poniżej przedstawiono opisy właściwości dla każdego komunikatu X12.

| Właściwość | Opis |
| --- | --- |
| Nadawcy | Partner gościa określony w **ustawieniach odbierania**lub partner hosta określony w polu **Wyślij ustawienia** dla umowy X12 |
| Odbiornik | Partner hosta określony w obszarze **Ustawienia odbierania**lub partner gościa określony w polu **Wyślij ustawienia** dla umowy X12 |
| Aplikacja logiki | Aplikacja logiki, w której są konfigurowane akcje X12 |
| State | Stan komunikatu X12 <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat X12. Nie skonfigurowano żadnego potwierdzenia funkcjonalności. <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat X12. Zostanie skonfigurowana i odebrana potwierdzenie funkcjonalne lub zostanie wysłane potwierdzenie funkcjonalne. <br>Niepowodzenie = odebrano lub wysłano nieprawidłowy komunikat X12. <br>Oczekiwanie: Odebrano lub wysłano prawidłowy komunikat X12. Zostanie skonfigurowane potwierdzenie funkcjonalne i oczekiwana jest potwierdzenie funkcjonalne. |
| Komunikaty | Stan potwierdzenia funkcjonalnego (997) <br>Zaakceptowano = odebrano lub wysłano pozytywne potwierdzenie funkcjonalne. <br>Odrzucono = odebrano lub wysłano negatywną funkcję potwierdzenia funkcjonalności. <br>Oczekiwanie = oczekiwano funkcji ACK, ale nie została ona odebrana. <br>Oczekiwanie = Wygenerowano funkcję ACK, ale nie można wysłać jej do partnera. <br>Niewymagane = nie jest skonfigurowane potwierdzenie funkcjonalne. |
| Direction | Kierunek komunikatu X12 |
| Identyfikator korelacji | Identyfikator, który jest skorelowany ze wszystkimi wyzwalaczami i akcjami w aplikacji logiki |
| Typ komunikatu | Typ komunikatu EDI X12 |
| ICN | Numer kontrolny wymiany komunikatu X12 |
| TSCN | Numer kontrolny zestawu transakcji dla komunikatu X12 |
| Timestamp | Godzina przetworzenia komunikatu przez akcję X12 |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formaty nazw X12 dla pobranych plików komunikatów

Poniżej przedstawiono formaty nazw pobranych plików i folderów wiadomości X12.

| Folder lub plik | Format nazwy |
| :------------- | :---------- |
| Folder wiadomości | Sender \_\_\_[odbiornik]\_X12\_[wymiany-Control-Number] [Global-Control-Number] [Transaction-Set-Control-Number] [timestamp] \_ |
| Dane wejściowe, wyjściowe i w przypadku konfiguracji, pliki potwierdzania | **Ładunek wejściowy**\_: [nadawca] [odbiornik\_]\_X12 [wymiana-Control-\_Number] input_payload. txt </p>**Ładunek wyjściowy**: [nadawca\_] [odbiornik\_]\_X12 [wymiana-Control-Number\_]\_Output ładunek. txt </p></p>**Dane wejściowe**: [nadawca]\_[odbiornik\_]\_X12 [wymiana-Control-\_Number] Inputs. txt </p></p>Dane **wyjściowe**: [nadawca]\_[odbiornik]\_X12\_[wymiana-Control-\_Number] Output. txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Opisy właściwości komunikatów EDIFACT

Poniżej przedstawiono opisy właściwości dla każdego komunikatu EDIFACT.

| Właściwość | Opis |
| --- | --- |
| Nadawcy | Partner gościa określony w **ustawieniach odbierania**lub partner hosta określony w polu **Wyślij ustawienia** dla umowy EDIFACT |
| Odbiornik | Partner hosta określony w obszarze **Ustawienia odbierania**lub partner gościa określony w polu **Wyślij ustawienia** dla umowy EDIFACT |
| Aplikacja logiki | Aplikacja logiki, w której są konfigurowane akcje EDIFACT |
| State | Stan komunikatu EDIFACT <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat EDIFACT. Nie skonfigurowano żadnego potwierdzenia funkcjonalności. <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat EDIFACT. Zostanie skonfigurowana i odebrana potwierdzenie funkcjonalne lub zostanie wysłane potwierdzenie funkcjonalne. <br>Niepowodzenie = odebrano lub wysłano nieprawidłowy komunikat EDIFACT <br>Oczekiwanie: Odebrano lub wysłano prawidłowy komunikat EDIFACT. Zostanie skonfigurowane potwierdzenie funkcjonalne i oczekiwana jest potwierdzenie funkcjonalne. |
| Komunikaty | Stan potwierdzenia funkcjonalności (CONTRL) <br>Zaakceptowano = odebrano lub wysłano pozytywne potwierdzenie funkcjonalne. <br>Odrzucono = odebrano lub wysłano negatywną funkcję potwierdzenia funkcjonalności. <br>Oczekiwanie = oczekiwano funkcji ACK, ale nie została ona odebrana. <br>Oczekiwanie = Wygenerowano funkcję ACK, ale nie można wysłać jej do partnera. <br>Niewymagane = nie jest skonfigurowane potwierdzenie funkcjonalne. |
| Direction | Kierunek komunikatu EDIFACT |
| Identyfikator korelacji | Identyfikator, który jest skorelowany ze wszystkimi wyzwalaczami i akcjami w aplikacji logiki |
| Typ komunikatu | Typ komunikatu EDIFACT |
| ICN | Numer kontrolny wymiany komunikatu EDIFACT |
| TSCN | Numer kontrolny zestawu transakcji dla komunikatu EDIFACT |
| Timestamp | Godzina przetworzenia komunikatu przez akcję EDIFACT |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formaty nazw EDIFACT dla pobranych plików komunikatów

Poniżej przedstawiono formaty nazw pobranych plików i folderów wiadomości EDIFACT.

| Folder lub plik | Format nazwy |
| :------------- | :---------- |
| Folder wiadomości | Sender \_\_\_[odbiornik]\_EDIFACT\_[wymiany-Control-Number] [Global-Control-Number] [Transaction-Set-Control-Number] [timestamp] \_ |
| Dane wejściowe, wyjściowe i w przypadku konfiguracji, pliki potwierdzania | **Ładunek wejściowy**\_: [nadawca] [odbiornik\_]\_EDIFACT [wymiana-Control-\_Number] input_payload. txt </p>**Ładunek wyjściowy**: [nadawca\_] [odbiornik\_]\_EDIFACT [wymiana-Control-Number\_]\_Output ładunek. txt </p></p>**Dane wejściowe**: [nadawca]\_[odbiornik\_]\_EDIFACT [wymiana-Control-\_Number] Inputs. txt </p></p>Dane **wyjściowe**: [nadawca]\_[odbiornik]\_EDIFACT\_[wymiana-Control-\_Number] Output. txt |
|          |             |

## <a name="next-steps"></a>Następne kroki

* [Zapytanie dotyczące komunikatów B2B w dziennikach Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
