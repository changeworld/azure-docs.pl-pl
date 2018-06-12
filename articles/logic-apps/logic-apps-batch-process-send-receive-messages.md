---
title: Partii przetwarzania komunikatów przez grupę lub kolekcji — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłanie i odbieranie wiadomości dla przetwarzania wsadowego w aplikacji logiki
keywords: partii, przetwarzanie wsadowe
author: jonfancey
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: 2815ce7fe0e10aadb60eaa77b58e5395fb5c98d8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298019"
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Wyślij, odbierania i partii przetwarzania komunikatów w aplikacji logiki

Do przetwarzania komunikatów razem w grupach, możesz wysłać elementy danych lub komunikaty, do *partii*, a następnie przetwarzania tych elementów jako plik wsadowy. Takie rozwiązanie jest przydatne, gdy użytkownik chce upewnij się, że elementy danych są grupowane w szczególny sposób i przetwarzane są ze sobą. 

Można tworzyć aplikacje logiki, otrzymujących elementów jako plik wsadowy za pomocą **partii** wyzwalacza. Umożliwia tworzenie aplikacji logiki, wysyłających elementów na partię przy użyciu **partii** akcji.

W tym temacie pokazano, jak można tworzyć przetwarzanie wsadowe rozwiązanie, wykonując następujące zadania: 

* [Tworzenie aplikacji logiki, która odbiera i zbiera elementów jako plik wsadowy](#batch-receiver). Ta aplikacja logiki "partii odbiorcy" Określa kryteria nazwa i wersja partii do spełnienia przed aplikacji logiki odbiornika zwalnia i przetwarza elementy. 

* [Tworzenie aplikacji logiki, która wysyła elementów na partię](#batch-sender). Ta aplikacja logiki "partii sender" Określa, gdzie można wysłać elementy, które muszą być istniejących aplikacji logiki odbiornika partii. Można również określić unikatowy klucz, takich jak numer klienta, aby "partycji" lub dzielenia partii docelowego na podzbiory na podstawie tego klucza. W ten sposób wszystkie elementy z tego klucza są zbierane i przetwarzane razem. 

## <a name="requirements"></a>Wymagania

Aby użyć tego przykładu, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). W przeciwnym razie możesz [skorzystać z subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/purchase-options/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Konto e-mail ze wszystkimi [dostawcy poczty e-mail obsługiwane przez usługi Azure Logic Apps](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Tworzenie aplikacji logiki, który odbiera komunikaty jako partii

Przed wysłaniem wiadomości do partii, należy najpierw utworzyć "partii odbiornika" aplikacji logiki z **partii** wyzwalacza. W ten sposób można wybrać tę aplikację logiki odbiornika podczas tworzenia aplikacji logiki nadawcy. Dla odbiornika należy określić nazwę instancji, kryteriów wersji i inne ustawienia. 

Aplikacje logiki nadawcy musi wiedzieć, gdzie wysłać elementy, gdy aplikacje logiki odbiorcy nie muszą mieć żadnych informacji o nadawcy.

1. W [portalu Azure](https://portal.azure.com), tworzenie aplikacji logiki o tej nazwie: "BatchReceiver" 

2. W Projektancie aplikacji logiki, Dodaj **partii** wyzwalacza, która uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wprowadź "partii" jako filtr. Wybierz ten wyzwalacz: **partii — komunikaty wsadowe**

   ![Dodaj wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Podaj nazwę dla partii, a następnie określ kryteria wydanie partii, na przykład:

   * **Nazwa partii**: Nazwa używana do identyfikowania partii, czyli "TestBatch" w tym przykładzie.
   * **Zwolnij kryteria**: kryteria wersji usługi partia zadań, które mogą być oparte na liczbie komunikatów i harmonogram.
   
     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Liczba komunikatów**: liczba komunikatów do przechowywania jako partii przed zwolnieniem do przetwarzania, czyli "5" w tym przykładzie.

     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Harmonogram**: harmonogram zlecenia partii do przetworzenia, czyli "co 5 minut" w tym przykładzie.

     ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Dodaj inną akcję, która wysyła wiadomość e-mail, gdy partii wyzwalacza. Zawsze partii jest pięć lub jego ostatnich 5 minut aplikacji logiki wysyła wiadomość e-mail.

   1. W obszarze wyzwalacza partii wybierz **+ nowy krok** > **Dodaj akcję**.

   2. W polu wyszukiwania wprowadź wartość „e-mail” jako filtr.
   W oparciu o dostawcę poczty e-mail, wybierz łącznik poczty e-mail.
   
      Na przykład jeśli masz konto służbowe, wybierz łącznik programu Outlook pakietu Office 365. 
      Jeśli masz konto usługi Gmail, wybierz łącznik usługi Gmail.

   3. Wybierz tę akcję dla Twojego łącznika: **{*dostawcy e-mail*} — Wyślij wiadomość e-mail**

      Na przykład:

      ![Wybierz akcję "Wyślij wiadomość e-mail" dla dostawcy usługi poczty e-mail](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Jeśli wcześniej nie utworzono połączenia dla dostawcy usługi poczty e-mail, wprowadź swoje poświadczenia poczty e-mail dla uwierzytelniania po wyświetleniu monitu. Dowiedz się więcej o [uwierzytelniania poświadczeń e-mail](../logic-apps/quickstart-create-first-logic-app-workflow.md).

6. Ustaw właściwości akcji, którą właśnie został dodany.

   * W polu **Do** wprowadź adres e-mail adresata. 
   Do celów testowych możesz użyć własnego adresu e-mail.

   * W **podmiotu** pole, jeśli **zawartości dynamicznej** zostanie wyświetlona lista, wybierz **nazwa partycji** pola.

     ![Wybierz z listy "Zawartość dynamiczna", "Nazwa partycji"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     W dalszej części artykułu można określić klucz partycji unikatowy, który dzieli partii docelowy logicznej zestawów do wysyłania wiadomości. 
     Każdy zestaw ma unikatowy numer, który jest generowany przez aplikację logiki nadawcy. 
     Ta funkcja umożliwia pojedyncza partia za pomocą wielu podzestawy i zdefiniować każdy podzbiór o nazwie podane.

   * W **treści** pole, jeśli **zawartości dynamicznej** zostanie wyświetlona lista, wybierz **identyfikator komunikatu** pola.

     !["Identyfikator komunikatu" Wybierz "Treść"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Ponieważ dane wejściowe dla działania wysyłania poczty e-mail jest tablicą, Projektant automatycznie dodaje **dla każdego** pętli wokół **wysłać wiadomość e-mail** akcji. 
     Ta pętla dokonuje wewnętrzny akcji każdego elementu w partii. 
     Dlatego z wyzwalaczem partii ustawioną pięć elementów, możesz uzyskać pięć wiadomości czasu uruchamiany wyzwalacza.

7.  Teraz, gdy utworzono aplikacji logiki odbiornika partii, Zapisz aplikację logiki.

    ![Zapisywanie aplikacji logiki](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Partycja ma limit 5000 komunikatów lub 80 MB. Jeśli albo warunek jest spełniony, partii może być zwolnione, nawet wtedy, gdy nie jest spełniony warunek zdefiniowane przez użytkownika.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Tworzenie aplikacji logiki, który wysyła wiadomości do partii

Teraz należy utworzyć co najmniej jedną aplikację logiki, wysyłających elementów do partii zdefiniowane przez aplikację logiki odbiornika. Dla nadawcy należy określić aplikację logiki odbiornik i nazwa usługi partia zadań, zawartość komunikatu i inne ustawienia. Opcjonalnie można podać klucz partycji unikatowy do dzielenia partii na podzestawy służąca do gromadzenia elementów z tym kluczem.

Aplikacje logiki nadawcy musi wiedzieć, gdzie wysłać elementy, gdy aplikacje logiki odbiorcy nie muszą mieć żadnych informacji o nadawcy.

1. Tworzenie aplikacji logiki innej o tej nazwie: "BatchSender"

   1. W polu wyszukiwania wprowadź "cyklu" jako filtr. 
   Wybierz wyzwalacz **Harmonogram — cyklicznie**

      ![Dodaj wyzwalacza "Harmonogram cyklu"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Ustaw częstotliwość i interwał uruchamiania nadawca aplikacji logiki co minutę.

      ![Ustaw częstotliwość i interwał cyklu wyzwalacza](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Dodaj nowy krok do wysyłania wiadomości do partii.

   1. W obszarze wyzwalacza cyklu wybierz **+ nowy krok** > **Dodaj akcję**.

   2. W polu wyszukiwania wprowadź "partii" jako filtr. 

   3. Wybierz tę akcję: **wysyłanie komunikatów do partii — wybierz przepływ pracy aplikacji logiki z wyzwalaczem partii**

      ![Zaznacz pole wyboru "Wyślij komunikaty do przetwarzania wsadowego"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Teraz wybierz "BatchReceiver" aplikację logiki utworzony wcześniej, która jest teraz wyświetlany jako akcja.

      ![Wybierz aplikację logiki "partii odbiornika"](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > Lista zawiera również inne aplikacje logiki mających wyzwalaczy partii.

3. Ustaw właściwości partii.

   * **Nazwa partii**: Nazwa instancji zdefiniowane przez aplikację logiki odbiornika, który jest "TestBatch" w tym przykładzie i sprawdzania poprawności w czasie wykonywania.

     > [!IMPORTANT]
     > Upewnij się, że nie zmieniaj nazwy usługi partia zadań, która musi odpowiadać nazwie partii, określonym przez aplikację logiki odbiornika.
     > Zmiana nazwy partii powoduje, że nadawca aplikację logiki, aby zakończyć się niepowodzeniem.

   * **Zawartość komunikatu**: zawartość komunikatu, który chcesz wysłać. 
   Na przykład dodać to wyrażenie, które wstawia bieżącą datę i godzinę do zawartość komunikatu, który możesz wysłać do partii:

     1. Gdy **zawartości dynamicznej** zostanie wyświetlona lista, wybierz **wyrażenia**. 
     2. Wprowadź wyrażenie **utcnow()** i wybierz polecenie **OK**. 

        ![W "Zawartość komunikatu" Wybierz opcję "Wyrażenie". Wprowadź "utcnow()".](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Teraz należy skonfigurować partycję dla partii. W operacji "BatchReceiver" Wybierz **Pokaż zaawansowane opcje**.

   * **Nazwa partycji**: klucz opcjonalne unikatowy partycji do użycia podczas dzielenia partii docelowej. Na przykład Dodaj wyrażenie, które generuje losową liczbę między jeden a pięć.
   
     1. Gdy **zawartości dynamicznej** zostanie wyświetlona lista, wybierz **wyrażenia**.
     2. Wprowadź wyrażenie: **rand(1,6)**

        ![Konfigurowanie partycji dla partii użytkownika docelowego](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        To **rand** funkcja generuje do zakresu od jednej do pięciu. 
        Dlatego są podzielenie tej partii na pięć numerowane partycje, które dynamicznie ustawia tego wyrażenia.

   * **Identyfikator wiadomości**: identyfikator komunikatu opcjonalne i jest generowanym identyfikatorze GUID, gdy parametr jest pusty. 
   W tym przykładzie pozostaw to pole puste.

5. Zapisz aplikację logiki. Aplikację logiki nadawcy powinna wyglądać w tym przykładzie:

   ![Zapisz aplikację logiki nadawcy](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązania przetwarzanie wsadowe, pozostaw aplikacje logiki uruchomione kilka minut. Szybko możesz uruchomić uzyskiwania wiadomości e-mail w grupach pięć wszystkich mających taki sam klucz partycji.

Aplikację logiki BatchSender działa co minutę, generuje losową liczbę między jeden a pięć i używa tego numeru wygenerowane jako klucza partycji dla partii docelowego, którego wysyłane są wiadomości. Zawsze partii ma pięć elementów z tym samym kluczem partycji aplikacji logiki BatchReceiver generowane i wysyła poczty dla każdego komunikatu.

> [!IMPORTANT]
> Po zakończeniu testowania, upewnij się, że wyłączenie BatchSender aplikację logiki, aby zatrzymać wysyłanie wiadomości i nie przeciążać skrzynki odbiorczej.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie w definicji aplikacji logiki za pomocą formatu JSON](../logic-apps/logic-apps-author-definitions.md)
* [Tworzenie aplikacji bez serwera w programie Visual Studio z usługi Azure Logic Apps i funkcje](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Obsługa wyjątków i rejestrowania błędów dla usługi logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
