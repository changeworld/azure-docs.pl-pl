---
title: Komunikaty programu Exchange dla scenariuszy integracji przedsiębiorstwa B2B
description: Odbieranie i wysyłanie wiadomości B2B między partnerami handlowymi w usłudze Azure Logic Apps przy użyciu pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151202"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Odbieranie i wysyłanie wiadomości B2B przy użyciu aplikacji Azure Logic Apps i pakietu Enterprise Integration Pack

Jeśli masz konto integracji definiujące partnerów handlowych i umowy, możesz utworzyć zautomatyzowany przepływ pracy business to business (B2B), który wymienia wiadomości między partnerami handlowymi za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) z [pakietem Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Usługa Azure Logic Apps współpracuje ze łącznikami obsługującymi protokoły zgodne ze standardami branżowymi AS2, X12, EDIFACT i RosettaNet. Łączniki te można również łączyć z innymi [łącznikami dostępnymi w aplikacjach logiki,](../connectors/apis-list.md)na przykład Salesforce i Office 365 Outlook.

W tym artykule pokazano, jak utworzyć aplikację logiki, która odbiera żądanie HTTP przy użyciu wyzwalacza żądania, dekoduje zawartość wiadomości przy użyciu akcji AS2 i X12, a następnie zwraca odpowiedź przy użyciu akcji Odpowiedź.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji, [zarejestruj się, aby korzystać z bezpłatnego konta platformy Azure.](https://azure.microsoft.com/free/)

* Pusta aplikacja logiki, dzięki czemu można utworzyć przepływ pracy B2B przy użyciu wyzwalacza [żądania,](../connectors/connectors-native-reqres.md) po którym następuje następujące akcje:

  * [Dekodowanie AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Warunek](../logic-apps/logic-apps-control-flow-conditional-statement.md), który wysyła [odpowiedź](../connectors/connectors-native-reqres.md) na podstawie tego, czy akcja dekodowania AS2 zakończy się pomyślnie, czy nie

  * [Dekodowanie wiadomości X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i Szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md) skojarzone z subskrypcją platformy Azure i połączone z aplikacją logiki. Zarówno aplikacja logiki i konta integracji musi istnieć w tej samej lokalizacji lub regionu platformy Azure.

* Co najmniej dwóch [partnerów handlowych,](../logic-apps/logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane na koncie integracyjnym wraz z [umowami AS2 i X12](logic-apps-enterprise-integration-agreements.md) dla tych partnerów.

## <a name="add-request-trigger"></a>Dodaj wyzwalacz żądania

W tym przykładzie użyto projektanta aplikacji logiki w witrynie Azure portal, ale można wykonać podobne kroki dla projektanta aplikacji logiki w programie Visual Studio.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz pustą aplikację logiki w projektancie aplikacji logiki.

1. W polu wyszukiwania `when a http request`wprowadź i wybierz pozycję **Po odebraniu żądania HTTP,** aby użyć go jako wyzwalacza.

   ![Wybierz wyzwalacz żądania, aby uruchomić przepływ pracy aplikacji logiki](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Pozostaw **pole Żądanie JSON Schema** pole puste, ponieważ komunikat X12 jest plik płaski.

   ![Pozostaw "Żądanie treści Schemat JSON" puste](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok generuje **adres URL HTTP POST** do użycia do wysyłania żądania, które wyzwala aplikację logiki. Aby skopiować ten adres URL, wybierz ikonę kopiowania obok adresu URL.

   ![Adres URL wygenerowany dla wyzwalacza żądania do odbierania połączeń](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Dodawanie akcji dekodowania AS2

Teraz dodaj akcje B2B, których chcesz użyć. W tym przykładzie użyto as2 i X12 akcji.

1. W obszarze wyzwalacza wybierz pozycję **Nowy krok**. Aby ukryć szczegóły wyzwalacza, kliknij pasek tytułu wyzwalacza.

   ![Dodawanie kolejnego kroku do przepływu pracy aplikacji logiki](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. W obszarze **Wybierz akcję**w polu `as2 decode`wyszukiwania wprowadź i wybierz pozycję **DEkodowanie AS2 (wersja 2).**

   ![Znajdź i wybierz "AS2 Decode (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Dla **message do dekodowania** właściwości, wprowadź dane wejściowe, które mają `body` as2 akcji do dekodowania, która jest zawartość odebrana przez wyzwalacz żądania HTTP. Masz wiele sposobów, aby określić tę zawartość jako dane wejściowe, albo z dynamicznej listy zawartości lub jako wyrażenie:

   * Aby wybrać z listy zawierającej dostępne wyjścia wyzwalaczy, kliknij wewnątrz **pola Wiadomość, aby odkodować.** Po wyświetleniu dynamicznej listy zawartości w obszarze **Po odebraniu żądania HTTP**wybierz pozycję Wartość właściwości **Obiekt,** na przykład:

     ![Wybierz wartość "Treść" z wyzwalacza](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Aby wprowadzić wyrażenie, które odwołuje `body` się do danych wyjściowych wyzwalacza, kliknij wewnątrz **pola Wiadomość, aby odkodować.** Po wyświetleniu dynamicznej listy zawartości wybierz pozycję **Wyrażenie**. W edytorze wyrażeń wprowadź tutaj wyrażenie i wybierz **przycisk OK:**

     `triggerOutputs()['body']`

     Lub w **polu Wiadomość do dekodowania** wprowadź bezpośrednio to wyrażenie:

     `@triggerBody()`

     Wyrażenie jest rozpoznawane przez **Body** token.

     ![Rozwiązane wyjście ciała z wyzwalacza](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. W przypadku właściwości **Nagłówki wiadomości** wprowadź wszystkie nagłówki wymagane dla akcji AS2, `headers` które są opisane przez zawartość odebraną przez wyzwalacz żądania HTTP.

   Aby wprowadzić wyrażenie, które odwołuje `headers` się do danych wyjściowych wyzwalacza, kliknij wewnątrz pola **Nagłówki wiadomości.** Po wyświetleniu dynamicznej listy zawartości wybierz pozycję **Wyrażenie**. W edytorze wyrażeń wprowadź tutaj wyrażenie i wybierz **przycisk OK:**

   `triggerOutputs()['Headers']`

   Aby uzyskać to wyrażenie do rozwiązania jako ten token, przełącz się między projektantem i widokiem kodu, na przykład:

   ![Rozwiązane nagłówki wyjściowe z wyzwalacza](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Dodaj akcję Odpowiedzi dla powiadomienia o otrzymaniu wiadomości

Aby powiadomić partnera handlowego, że wiadomość została odebrana, można zwrócić odpowiedź, która zawiera powiadomienie o dyspozycji wiadomości AS2 (MDN) przy użyciu **odpowiedzi** akcji. Dodając tę akcję natychmiast po **as2 dekodowanie** akcji, jeśli ta akcja nie powiedzie się, aplikacja logiki nie kontynuuje przetwarzania.

1. W obszarze akcji **Dekodowanie AS2** wybierz pozycję **Nowy krok**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wybierz pozycję **Wbudowane**. W polu wyszukiwania wpisz `condition`. Z listy **Akcje** wybierz pozycję **Warunek**.

   ![Dodaj akcję "Warunek"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Teraz pojawia się kształt warunku, w tym ścieżki, czy warunek jest spełniony, czy nie.

   ![Kształt warunku ze ścieżkami decyzyjnymi](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Teraz określ warunek do oceny. W polu **Wybierz wartość** wprowadź to wyrażenie:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   W środkowym polu upewnij się, że `is equal to`operacja porównywania jest ustawiona na . W polu po prawej stronie `Expected`wprowadź wartość . Aby uzyskać wyrażenie do rozwiązania jako ten token, przełącz się między projektantem i widoku kodu.

   ![Kształt warunku ze ścieżkami decyzyjnymi](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Teraz określ odpowiedzi, aby zwrócić, czy akcja **dekodowania AS2** powiedzie się, czy nie.

   1. W przypadku, gdy akcja **dekodowania AS2** powiedzie się, w **If true** kształt, wybierz dodaj **akcję**. W obszarze **Wybierz akcję**w polu `response`wyszukiwania wprowadź i wybierz pozycję **Odpowiedź**.

      ![Znajdź i wybierz akcję "Odpowiedź"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Aby uzyskać dostęp do as2 MDN z wyjścia akcji **DEkodowanie AS2,** określ następujące wyrażenia:

      * We właściwości **Nagłówki** akcji **Odpowiedź** wprowadź to wyrażenie:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * We właściwości **Body** akcji **Odpowiedź** wprowadź to wyrażenie:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Aby uzyskać wyrażenia do rozpoznania jako tokeny, przełącz się między projektantem a widokiem kodu:

      ![Rozwiązane wyrażenie w celu uzyskania dostępu do AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. W przypadku niepowodzenia akcji **dekodowania AS2** w kształcie **Jeśli false** wybierz pozycję **Dodaj akcję**. W obszarze **Wybierz akcję**w polu `response`wyszukiwania wprowadź i wybierz pozycję **Odpowiedź**. Skonfiguruj akcję **Odpowiedź,** aby zwrócić odpowiedni stan i błąd.

1. Zapisz aplikację logiki.

## <a name="add-decode-x12-message-action"></a>Dodaj akcję wiadomości Dekoduj X12

1. Teraz dodaj akcję **dekoduj wiadomość X12.** W obszarze **Akcja Odpowiedź** wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu `x12 decode`wyszukiwania wprowadź i wybierz pozycję **Dekoduj wiadomość X12**.

   ![Znajdź i wybierz akcję "Dekoduj wiadomość X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Jeśli akcja X12 monituje o podanie informacji o połączeniu, podaj nazwę połączenia, wybierz konto integracji, którego chcesz użyć, a następnie wybierz pozycję **Utwórz**.

   ![Tworzenie połączenia X12 z kontem integracji](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Teraz określ dane wejściowe dla akcji X12. W tym przykładzie użyto danych wyjściowych z akcji AS2, która jest zawartością wiadomości, ale należy pamiętać, że ta zawartość jest w formacie obiektu JSON i jest zakodowana base64. Tak więc musisz przekonwertować tę zawartość na ciąg.

   W **polu X12 Flat file message to dekoduj** wprowadź to wyrażenie, aby przekonwertować dane wyjściowe AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Aby uzyskać wyrażenie do rozwiązania jako ten token, przełącz się między projektantem i widoku kodu.

    ![Konwertowanie zawartości zakodowanej base64 na ciąg](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Zapisz aplikację logiki.

   Jeśli potrzebujesz dodatkowych kroków dla tej aplikacji logiki, na przykład do dekodowania zawartości wiadomości i danych wyjściowych tej zawartości w formacie obiektu JSON, kontynuuj tworzenie aplikacji logiki.

Teraz skończysz konfigurować aplikację logiki B2B. W aplikacji w świecie rzeczywistym możesz chcieć przechowywać zdekodowane dane X12 w aplikacji biznesowej (LOB) lub magazynie danych. Zobacz na przykład następujące artykuły:

* [Łączenie z systemami SAP z usługi Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitorowanie, tworzenie i zarządzanie plikami SFTP przy użyciu aplikacji SSH i Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Aby połączyć własne aplikacje LOB i korzystać z tych interfejsów API w aplikacji logiki, możesz dodać więcej akcji lub [napisać niestandardowe interfejsy API](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Następne kroki

* [Odbieranie przychodzących połączeń HTTPS i odpowiadanie na nie](../connectors/connectors-native-reqres.md)
* [Komunikaty Programu Exchange AS2 dla integracji przedsiębiorstwa B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Komunikaty programu Exchange X12 dla integracji przedsiębiorstwa B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Dowiedz się więcej o [pakiecie integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)