---
title: Śledzenie wiadomości B2B z Azure Log Analytics — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Śledzenie komunikacji B2B dla aplikacji logiki i konta integracji z usługi Analiza dzienników Azure
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 48523e1f1bc8d5b810cc7c9d1a7308f1aaadf8bb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="track-b2b-communication-with-azure-log-analytics"></a>Śledzenie komunikacji B2B z usługi Analiza dzienników Azure

Po skonfigurowaniu B2B komunikacji między dwiema uruchomionych procesów biznesowych lub aplikacji za pośrednictwem konta integracji tych jednostek mogą wymieniać komunikaty ze sobą. Aby sprawdzić, czy komunikaty te są przetwarzane prawidłowo, można śledzić AS2, X12, a EDIFACT wiadomości z [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Na przykład można użyć tych możliwości śledzenia opartych na sieci web do śledzenia komunikatów:

* Liczba komunikatów i stanu
* Stan potwierdzenia
* Korelowanie wiadomości z potwierdzenia
* Szczegółowe informacje o błędzie opisy błędów
* Możliwości wyszukiwania

## <a name="requirements"></a>Wymagania

* Aplikację logiki, który został skonfigurowany z rejestrowania diagnostyki. Dowiedz się [sposób tworzenia aplikacji logiki](quickstart-create-first-logic-app-workflow.md) i [jak skonfigurować rejestrowanie dla danej aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Konta integracji, które skonfigurowano przy użyciu rejestrowania i monitorowania. Dowiedz się [sposobu tworzenia konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [jak skonfigurować monitorowanie i rejestrowanie dla tego konta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Jeśli nie jest jeszcze, [publikowania danych diagnostycznych do analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Po zostały spełnione wymagania poprzedniej, powinien mieć obszar roboczy w analizy dzienników. Do komunikacji B2B w analizy dzienników śledzenia, należy używać tego samego obszaru roboczego. 
>  
> Dowiedz się, jeśli nie masz obszaru roboczego analizy dzienników [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="add-the-logic-apps-b2b-solution-to-log-analytics"></a>Dodaj rozwiązanie B2B aplikacji logiki do analizy dzienników

Aby analizy dzienników, śledzenie wiadomości B2B aplikacji logiki, należy dodać **B2B aplikacje logiki** rozwiązania do portalu OMS. Dowiedz się więcej o [Dodawanie rozwiązań do analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md).

1. W [portalu Azure](https://portal.azure.com), wybierz **wszystkie usługi**. Wyszukaj "analizy dzienników", a następnie wybierz pozycję **analizy dzienników** w sposób pokazany poniżej:

   ![Znajdź analizy dzienników](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. W obszarze **analizy dzienników**, Znajdź i zaznacz pozycję obszaru roboczego analizy dzienników. 

   ![Wybierz obszar roboczy analizy dzienników](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. W obszarze **zarządzania**, wybierz **portalu OMS**.

   ![Wybierz portalu OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Po otwarciu strony głównej wybierz **galerii rozwiązań**.    

   ![Wybierz galerii rozwiązań](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. W obszarze **wszystkie rozwiązania**, Znajdź i wybierz **B2B aplikacje logiki**.     

   ![Wybierz B2B aplikacje logiki](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. W obszarze **B2B aplikacje logiki**, wybierz **Dodaj**.

   ![Wybierz opcję Dodaj](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   Na stronie głównej Kafelek **wiadomości B2B aplikacje logiki** pojawi się teraz. 
   Ten Kafelek aktualizuje liczba komunikatów podczas przetwarzania wiadomości B2B.

   ![Strona główna, kafelka wiadomości B2B aplikacje logiki](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-log-analytics"></a>Śledzenie stanu komunikat i szczegóły analizy dzienników

1. Po przetworzeniu wiadomości B2B można wyświetlić stan i szczegółowe informacje dla tych wiadomości. Na stronie głównej wybierz **wiadomości B2B aplikacje logiki** kafelka.

   ![Liczba komunikatów zaktualizowane](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > Domyślnie **wiadomości B2B aplikacje logiki** kafelka zawiera dane oparte na jeden dzień. Aby zmienić zakres danych na inny interwał, wybierz kontrola zakresu w górnej części strony:
   > 
   > ![Zmień zakres danych](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. Po komunikat stanu pulpitu nawigacyjnego zostanie wyświetlony, można wyświetlić więcej szczegółów dla typu określonego komunikatu, która zawiera dane oparte na jeden dzień. Wybierz Kafelek **AS2**, **X12**, lub **EDIFACT**.

   ![Wyświetlanie komunikatów stanu](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Zostanie wyświetlona lista komunikatów dla wybranej kafelka. 
   Aby dowiedzieć się więcej na temat właściwości dla każdego typu komunikatu, zobacz poniższe opisy właściwości wiadomości:

   * [Właściwości komunikatu AS2](#as2-message-properties)
   * [Właściwości wiadomości X12](#x12-message-properties)
   * [Właściwości komunikatu EDIFACT](#EDIFACT-message-properties)

   Na przykład Oto jak może wyglądać AS2 listy komunikatów:

   ![Wyświetlanie komunikatów AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Aby wyświetlić lub wyeksportować wejścia i wyjścia dla określonych wiadomości, wybierz tych wiadomości, a następnie wybierz **Pobierz**. Po wyświetleniu monitu, Zapisz plik zip na komputerze lokalnym, a następnie Wyodrębnij tego pliku. 

   Wyodrębniony folder zawiera folder dla każdego wybranego komunikatu. 
   Jeśli konfigurujesz potwierdzeń folderu wiadomości zawiera również pliki ze szczegółami potwierdzenia. 
   Każdy komunikat znajduje się w nim co najmniej tych plików: 
   
   * Czytelny dla człowieka pliki z wprowadzony ładunek i szczegóły ładunek danych wyjściowych
   * Pliki zakodowane z wejściami i wyjściami

   Dla każdego typu komunikatu można znaleźć folderu i formatów nazwy pliku:

   * [Formaty nazwę folderowi i plikowi AS2](#as2-folder-file-names)
   * [X12 folder i nazwę pliku formatów](#x12-folder-file-names)
   * [EDIFACT formatów nazwy plików i folderów](#edifact-folder-file-names)

   ![Pobieranie plików komunikatów](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Aby wyświetlić wszystkie akcje, które mają taki sam identyfikator uruchomienia, na **wyszukiwania dziennika** wybierz wiadomości z listy wiadomości.

   Czynności te według kolumny lub wyszukaj określone wyniki można sortować.

   ![Identyfikator uruchomienia akcji o takim samym](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Aby wyszukać wyników z kwerendy wbudowane, wybierz **ulubione**.

   * Dowiedz się [jak tworzyć zapytania, dodając filtry](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Lub Dowiedz się więcej o [jak znaleźć dane z dziennika wyszukiwania w analizy dzienników](../log-analytics/log-analytics-log-searches.md).

   * Aby zmienić zapytanie w polu wyszukiwania, zaktualizuj zapytanie z kolumnami i wartości, które ma być używany jako filtry.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Opisy właściwości i formatów nazwy dla AS2, X 12 i EDIFACT wiadomości

Dla każdego typu komunikatu poniżej przedstawiono opisy właściwości i formatów nazwy plików pobranych wiadomości.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Opisy właściwości komunikatu AS2

Poniżej przedstawiono opisy właściwości dla każdego komunikatu AS2.

| Właściwość | Opis |
| --- | --- |
| Nadawca | Partner gościa określone w **ustawienia odbierania**, lub partnera hosta określona w **ustawienia wysyłania** umowy AS2 |
| Odbiornik | Partner hosta określona w **ustawienia odbierania**, lub partnera gościa określone **ustawienia wysyłania** umowy AS2 |
| Aplikacja logiki | Aplikację logiki, w którym zdefiniowano działania AS2 |
| Stan | Stan wiadomości AS2 <br>Powodzenie = odebranych lub wysłanych prawidłowego elementu message AS2. MDN nie jest skonfigurowane. <br>Powodzenie = odebranych lub wysłanych prawidłowego elementu message AS2. Ustawianie i odebranych MDN lub MDN są wysyłane. <br>Nie powiodło się = Odebrano nieprawidłowy komunikat AS2. MDN nie jest skonfigurowane. <br>Oczekujące = odebranych lub wysłanych prawidłowego elementu message AS2. MDN jest skonfigurowany, a oczekiwano MDN. |
| ACK. | Stan wiadomości MDN <br>Zaakceptowane = odebranych lub wysłanych MDN dodatnią. <br>Oczekujące = oczekiwanie na odbierać lub wysyłać MDN. <br>Odrzucone = odebranych lub wysłanych MDN ujemna. <br>Nie wymaga = MDN nie jest skonfigurowany w umowie. |
| Kierunek | Kierunek wiadomości AS2 |
| Identyfikator korelacji | Identyfikator odpowiadająca wyzwalacze i akcje w aplikacji logiki |
| Identyfikator komunikatu | Identyfikator komunikatu AS2 z nagłówków komunikatu AS2 |
| Sygnatura czasowa | Czas przetworzenia komunikatu akcji AS2 |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Nazwa AS2 formatów plików pobranych komunikatów

Poniżej przedstawiono formatów nazwy dla każdego folderu wiadomości AS2 pobrane i plików.

| Folder lub plik | Format nazwy |
| :------------- | :---------- |
| Folder wiadomości | [nadawcy] \_[odbiornika]\_AS2\_[identyfikator korelacji]\_[identyfikator komunikatu]\_[sygnatury czasowej] |
| Dane wejściowe, dane wyjściowe i jeśli skonfigurować potwierdzenia plików | **Wprowadzony ładunek**: [nadawcy]\_[odbiornika]\_AS2\_[identyfikator korelacji]\_input_payload.txt </p>**Ładunek danych wyjściowych**: [nadawcy]\_[odbiornika]\_AS2\_[identyfikator korelacji]\_dane wyjściowe\_payload.txt </p></p>**Dane wejściowe**: [nadawcy]\_[odbiornika]\_AS2\_[identyfikator korelacji]\_inputs.txt </p></p>**Dane wyjściowe**: [nadawcy]\_[odbiornika]\_AS2\_[identyfikator korelacji]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Opisy właściwości komunikatu X12

Poniżej przedstawiono opisy właściwości dla każdego X12 wiadomości.

| Właściwość | Opis |
| --- | --- |
| Nadawca | Partner gościa określone w **ustawienia odbierania**, lub partnera hosta określona w **ustawienia wysyłania** dla X12 umowy |
| Odbiornik | Partner hosta określona w **ustawienia odbierania**, lub partnera gościa określone **ustawienia wysyłania** dla X12 umowy |
| Aplikacja logiki | Aplikację logiki gdzie X12 akcje są skonfigurowane. |
| Stan | X12 komunikatu stanu <br>Powodzenie = odebranych lub wysłanych prawidłową X12 wiadomości. Nie potwierdzenia funkcjonalności jest skonfigurowany. <br>Powodzenie = odebranych lub wysłanych prawidłową X12 wiadomości. Ustawianie i otrzymano potwierdzenia funkcjonalności lub funkcjonalności potwierdzenia są wysyłane. <br>Nie powiodło się = odebranych lub wysłanych X12 nieprawidłowy komunikat. <br>Oczekujące = odebranych lub wysłanych prawidłową X12 wiadomości. Potwierdzenia funkcjonalności jest skonfigurowany, a oczekiwano funkcjonalności potwierdzenia. |
| ACK. | Funkcjonalności stan potwierdzenia (997) <br>Zaakceptowane = odebranych lub wysłanych dodatnią funkcjonalności potwierdzenia. <br>Odrzucone = odebranych lub wysłanych ujemna funkcjonalności potwierdzenia. <br>Oczekujące = oczekiwano potwierdzenia funkcjonalności, ale nie została odebrana. <br>Oczekujące = generowane potwierdzenia funkcjonalności, ale nie można wysłać do partnera. <br>Nie wymaga = funkcjonalne potwierdzenia nie jest skonfigurowany. |
| Kierunek | Kierunek wiadomości X12 |
| Identyfikator korelacji | Identyfikator odpowiadająca wyzwalacze i akcje w aplikacji logiki |
| Typ msg | Typ komunikatu 12 EDI X |
| ICN | Numer kontroli Interchange X12 wiadomości |
| TSCN | Numer transakcji do sterowania ustaw dla X12 wiadomości |
| Sygnatura czasowa | Podczas gdy X12 komunikat przetworzyć akcji |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 nazwy formatów plików pobranych komunikatów

Poniżej przedstawiono formatów nazwy każdego pobierane X12 folderów i plików komunikatów.

| Folder lub plik | Format nazwy |
| :------------- | :---------- |
| Folder wiadomości | [nadawcy] \_[odbiornika]\_X12\_[ruch numer wymiany formantu]\_[numer globalnych kontroli]\_[-zestaw kontroli — numer transakcji]\_[sygnatury czasowej] |
| Dane wejściowe, dane wyjściowe i jeśli skonfigurować potwierdzenia plików | **Wprowadzony ładunek**: [nadawcy]\_[odbiornika]\_X12\_[ruch numer wymiany formantu]\_input_payload.txt </p>**Ładunek danych wyjściowych**: [nadawcy]\_[odbiornika]\_X12\_[ruch numer wymiany formantu]\_dane wyjściowe\_payload.txt </p></p>**Dane wejściowe**: [nadawcy]\_[odbiornika]\_X12\_[ruch numer wymiany formantu]\_inputs.txt </p></p>**Dane wyjściowe**: [nadawcy]\_[odbiornika]\_X12\_[ruch numer wymiany formantu]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Opisy właściwości komunikatu EDIFACT

Poniżej przedstawiono opisy właściwości dla każdego komunikatu EDIFACT.

| Właściwość | Opis |
| --- | --- |
| Nadawca | Partner gościa określone w **ustawienia odbierania**, lub partnera hosta określona w **ustawienia wysyłania** umowy EDIFACT |
| Odbiornik | Partner hosta określona w **ustawienia odbierania**, lub partnera gościa określone **ustawienia wysyłania** umowy EDIFACT |
| Aplikacja logiki | Aplikację logiki, w którym zdefiniowano działania EDIFACT |
| Stan | Stan EDIFACT wiadomości <br>Powodzenie = odebranych lub wysłanych prawidłowego elementu message EDIFACT. Nie potwierdzenia funkcjonalności jest skonfigurowany. <br>Powodzenie = odebranych lub wysłanych prawidłowego elementu message EDIFACT. Ustawianie i otrzymano potwierdzenia funkcjonalności lub funkcjonalności potwierdzenia są wysyłane. <br>Nie powiodło się = odebranych lub wysłanych nieprawidłowy komunikat EDIFACT <br>Oczekujące = odebranych lub wysłanych prawidłowego elementu message EDIFACT. Potwierdzenia funkcjonalności jest skonfigurowany, a oczekiwano funkcjonalności potwierdzenia. |
| ACK. | Funkcjonalności stan potwierdzenia (997) <br>Zaakceptowane = odebranych lub wysłanych dodatnią funkcjonalności potwierdzenia. <br>Odrzucone = odebranych lub wysłanych ujemna funkcjonalności potwierdzenia. <br>Oczekujące = oczekiwano potwierdzenia funkcjonalności, ale nie została odebrana. <br>Oczekujące = generowane potwierdzenia funkcjonalności, ale nie można wysłać do partnera. <br>Nie wymaga = potwierdzenia funkcjonalności nie jest skonfigurowany. |
| Kierunek | Kierunek wiadomości EDIFACT |
| Identyfikator korelacji | Identyfikator odpowiadająca wyzwalacze i akcje w aplikacji logiki |
| Typ msg | Typ komunikatu EDIFACT |
| ICN | Numer formantu wymiany wiadomości EDIFACT |
| TSCN | Numer transakcji do sterowania ustaw dla komunikatu EDIFACT |
| Sygnatura czasowa | Czas przetworzenia komunikatu akcji EDIFACT |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Nazwa EDIFACT formatów plików pobranych komunikatów

Poniżej przedstawiono formatów nazwy dla każdego folderu wiadomości EDIFACT pobrane i plików.

| Folder lub plik | Format nazwy |
| :------------- | :---------- |
| Folder wiadomości | [nadawcy] \_[odbiornika]\_EDIFACT\_[ruch numer wymiany formantu]\_[numer globalnych kontroli]\_[-zestaw kontroli — numer transakcji]\_[sygnatury czasowej] |
| Dane wejściowe, dane wyjściowe i jeśli skonfigurować potwierdzenia plików | **Wprowadzony ładunek**: [nadawcy]\_[odbiornika]\_EDIFACT\_[ruch numer wymiany formantu]\_input_payload.txt </p>**Ładunek danych wyjściowych**: [nadawcy]\_[odbiornika]\_EDIFACT\_[ruch numer wymiany formantu]\_dane wyjściowe\_payload.txt </p></p>**Dane wejściowe**: [nadawcy]\_[odbiornika]\_EDIFACT\_[ruch numer wymiany formantu]\_inputs.txt </p></p>**Dane wyjściowe**: [nadawcy]\_[odbiornika]\_EDIFACT\_[ruch numer wymiany formantu]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Kolejne kroki

* [Kwerenda dotycząca wiadomości B2B analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Śledzenie niestandardowych schematów](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)