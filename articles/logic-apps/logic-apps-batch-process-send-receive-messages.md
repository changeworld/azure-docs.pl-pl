---
title: Komunikaty procesu wsadowego jako grupa
description: Wysyłanie i odbieranie wiadomości w grupach między przepływami pracy przy użyciu przetwarzania wsadowego w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e48d2bb2ffce0dd4f9293417534165165d426784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666758"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Wysyłanie, odbieranie i przetwarzanie wsadowe wiadomości w usłudze Azure Logic Apps

Aby wysyłać i przetwarzać wiadomości razem w określony sposób jako grupy, można utworzyć rozwiązanie przetwarzania wsadowego, które zbiera wiadomości do *partii,* dopóki nie zostaną spełnione określone kryteria zwalniania i przetwarzania wiadomości wsadowych. Przetwarzanie wsadowe można zmniejszyć, jak często aplikacja logiki przetwarza wiadomości. W tym artykule pokazano, jak utworzyć rozwiązanie wsadowe, tworząc dwie aplikacje logiki w ramach tej samej subskrypcji platformy Azure, regionu platformy Azure i zgodnie z tą określoną kolejnością: 

* Aplikacja logiki ["odbiorca wsadowy",](#batch-receiver) która akceptuje i zbiera wiadomości do partii, dopóki nie zostanie spełnione określone kryteria zwalniania i przetwarzania tych wiadomości.

  Upewnij się, że najpierw utworzysz odbiornik wsadowy, aby później można było wybrać miejsce docelowe partii podczas tworzenia nadawcy partii.

* Co najmniej jedna aplikacja logiki ["nadawca wsadowy",](#batch-sender) które wysyłają wiadomości do wcześniej utworzonego odbiornika wsadowego. 

   Można również określić unikatowy klucz, taki jak numer klienta, który *dzieli* lub dzieli partię docelową na logiczne podzbiory na podstawie tego klucza. W ten sposób aplikacja odbiorcy może zbierać wszystkie elementy za pomocą tego samego klucza i przetwarzać je razem.

Upewnij się, że odbiorca wsadowy i nadawca partii współużytkują tę samą subskrypcję platformy Azure *i* region platformy Azure. Jeśli tak nie jest, nie można wybrać odbiornika wsadowego podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten przykład, potrzebujesz tych elementów:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Możesz też [zarejestrować się w ramach subskrypcji Pay-As-You-Go](https://azure.microsoft.com/pricing/purchase-options/).

* Konto e-mail z dowolnym [dostawcą poczty e-mail obsługiwanym przez usługę Azure Logic Apps](../connectors/apis-list.md)

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Aby używać programu Visual Studio zamiast witryny Azure portal, upewnij się, że [program Visual Studio został skonfigurowany do pracy z aplikacjami logiki.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Tworzenie odbiornika wsadowego

Przed wysłaniem wiadomości do partii, że partia musi najpierw istnieć jako miejsce docelowe, gdzie można wysłać te wiadomości. Dlatego najpierw należy utworzyć aplikację logiki "odbiornik wsadowy", która rozpoczyna się od wyzwalacza **partii.** W ten sposób podczas tworzenia aplikacji logiki "nadawca partii" można wybrać aplikację logiki odbiornika wsadowego. Odbiornik wsadowy kontynuuje zbieranie wiadomości, dopóki nie zostanie spełnione określone kryteria zwalniania i przetwarzania tych komunikatów. Podczas gdy odbiorniki wsadowe nie muszą wiedzieć nic o nadawców partii, nadawcy partii muszą znać miejsce docelowe, gdzie wysyłają wiadomości. 

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio utwórz aplikację logiki o tej nazwie: "BatchReceiver" 

2. W Logic Apps Designer dodaj wyzwalacz **partii,** który uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wpisz "partia" jako filtr. Wybierz ten wyzwalacz: **wiadomości wsadowe**

   ![Dodaj wyzwalacz "Wiadomości wsadowe"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Ustaw następujące właściwości odbiornika wsadowego: 

   | Właściwość | Opis | 
   |----------|-------------|
   | **Tryb wsadowy** | - **Inline**: Do definiowania kryteriów uwalniania wewnątrz wyzwalacza partii <br>- **Konto integracji**: Do definiowania wielu konfiguracji kryteriów wydania za pośrednictwem [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Za pomocą konta integracji można zachować te konfiguracje w jednym miejscu, a nie w oddzielnych aplikacjach logiki. | 
   | **Nazwa partii** | Nazwa partii, która jest "TestBatch" w tym przykładzie i dotyczy tylko **wbudowanego** trybu wsadowego |  
   | **Kryteria uwalniania** | Dotyczy tylko **trybu wsadowego inline** i wybiera kryteria, które należy spełnić przed przetworzeniem każdej partii: <p>- **Liczba wiadomości na podstawie:** Zwolnij partię na podstawie liczby wiadomości zebranych przez partię. <br>- **Na podstawie rozmiaru:** Zwolnij partię na podstawie całkowitego rozmiaru w bajtach dla wszystkich wiadomości zebranych przez tę partię. <br>- **Harmonogram**: Zwolnij partię na podstawie harmonogramu cyklu, który określa interwał i częstotliwość. W opcjach zaawansowanych można również wybrać strefę czasową i podać datę i godzinę rozpoczęcia. <br>- **Wybierz wszystkie**: Użyj wszystkich określonych kryteriów. | 
   | **Liczba wiadomości** | Liczba wiadomości do zbierania w partii, na przykład 10 wiadomości. Limit partii wynosi 8000 wiadomości. | 
   | **Rozmiar partii** | Całkowity rozmiar w bajtach do zebrania w partii, na przykład 10 MB. Limit rozmiaru partii wynosi 80 MB. | 
   | **Harmonogram** | Interwał i częstotliwość między wersjami wsadowymi, na przykład 10 minut. Minimalny cykl wynosi 60 sekund lub 1 minutę. Ułamkowe minuty są skutecznie zaokrąglane do 1 minuty. Aby określić strefę czasową lub datę i godzinę rozpoczęcia, wybierz pozycję **Pokaż opcje zaawansowane**. | 
   ||| 

   > [!NOTE]
   > 
   > Jeśli zmienisz kryteria wydania, podczas gdy wyzwalacz nadal ma wsadowe, ale niewysłane wiadomości, wyzwalacz używa zaktualizowanych kryteriów wydania do obsługi niewysłanych wiadomości. 

   W tym przykładzie przedstawiono wszystkie kryteria, ale w przypadku własnych testów wypróbuj tylko jedno kryterium:

   ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Teraz dodaj jedną lub więcej akcji, które przetwarzają każdą partię. 

   W tym przykładzie dodaj akcję, która wysyła wiadomość e-mail po uruchomieniu wyzwalacza partii. 
   Wyzwalacz uruchamia się i wysyła wiadomość e-mail, gdy partia ma 10 wiadomości, osiągnie 10 MB lub po upływie 10 minut.

   1. W obszarze wyzwalacza partii wybierz pozycję **Nowy krok**.

   2. W polu wyszukiwania wprowadź „wyślij wiadomość e-mail” jako filtr.
   Na podstawie dostawcy poczty e-mail wybierz łącznik wiadomości e-mail.

      Na przykład, jeśli masz konto osobiste, takie jak @outlook.com lub @hotmail.com, wybierz łącznik Outlook.com. 
      Jeśli masz konto Gmail, wybierz łącznik Gmaila. 
      W tym przykładzie użyto usługi Office 365 Outlook. 

   3. Wybierz tę akcję: **Wyślij wiadomość e-mail — <*dostawcą* > poczty e-mail**

      Przykład:

      ![Wybierz akcję "Wyślij wiadomość e-mail" dla swojego dostawcy poczty e-mail](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. W razie potrzeby zaloguj się do swojego konta e-mail. 

6. Ustaw właściwości dodanej akcji.

   * W polu **Do** wprowadź adres e-mail adresata. 
   Do celów testowych możesz użyć własnego adresu e-mail.

   * W polu **Temat** po wyświetleniu dynamicznej listy zawartości wybierz pole **Nazwa partycji.**

     ![Z dynamicznej listy zawartości wybierz "Nazwa partycji"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Później w nadawcy partii można określić unikatowy klucz partycji, który dzieli partię docelową na logiczne podzbiory, w których można wysyłać wiadomości. 
     Każdy zestaw ma unikatowy numer, który jest generowany przez aplikację logiki nadawcy partii. 
     Ta funkcja umożliwia użycie pojedynczej partii z wieloma podzbiorami i zdefiniowanie każdego podzbioru o nazwie, która jest podasz.

     > [!IMPORTANT]
     > Partycja ma limit 5000 komunikatów lub 80 MB. Jeśli którykolwiek warunek jest spełniony, aplikacje logiki może zwolnić partii, nawet wtedy, gdy warunek zdefiniowanego zwolnienia nie jest spełniony.

   * W polu **Treść** po wyświetleniu dynamicznej listy zawartości wybierz pole **Identyfikator wiadomości.** 

     Projektant aplikacji logiki automatycznie dodaje pętlę "Dla każdego" wokół akcji wysyłania wiadomości e-mail, ponieważ ta akcja traktuje dane wyjściowe z poprzedniej akcji jako kolekcję, a nie partię. 

     ![Dla "Body", wybierz "Identyfikator wiadomości"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Zapisz aplikację logiki. Utworzono odbiornik wsadowy.

    ![Zapisywanie aplikacji logiki](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Jeśli używasz programu Visual Studio, upewnij się, że [wdrożyłeś aplikację logiki odbiornika wsadowego na platformie Azure.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) W przeciwnym razie nie można wybrać odbiornika wsadowego podczas tworzenia nadawcy partii.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Tworzenie nadawcy wsadowego

Teraz utwórz jedną lub więcej aplikacji logiki nadawcy wsadowego, które wysyłają wiadomości do aplikacji logiki odbiornika wsadowego. W każdym nadawcy partii należy określić odbiornik wsadowy i nazwę partii, zawartość wiadomości i inne ustawienia. Opcjonalnie można podać unikatowy klucz partycji, aby podzielić partię na logiczne podzbiory do zbierania wiadomości za pomocą tego klucza. 

* Upewnij się, że odbiornik partii został już [utworzony,](#batch-receiver) więc podczas tworzenia nadawcy partii można wybrać istniejący odbiorca wsadowy jako partię docelową. Podczas gdy odbiorniki wsadowe nie muszą wiedzieć nic o nadawców partii, nadawcy partii muszą wiedzieć, gdzie wysyłać wiadomości. 

* Upewnij się, że odbiorca wsadowy i nadawca partii współużytkują ten sam region platformy Azure *i* subskrypcję platformy Azure. Jeśli tak nie jest, nie można wybrać odbiornika wsadowego podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

1. Utwórz inną aplikację logiki o tej nazwie: "BatchSender"

   1. W polu wyszukiwania wpisz "cyklu" jako filtr. 
   Wybierz ten wyzwalacz: **Cykl - Harmonogram**

      ![Dodaj wyzwalacz "Cykl - Harmonogram"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Ustaw częstotliwość i interwał, aby uruchamiać aplikację logiki nadawcy co minutę.

      ![Ustawianie częstotliwości i interwału wyzwalacza cyklu](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Dodaj nową akcję do wysyłania wiadomości do partii.

   1. W obszarze wyzwalacza cyklu wybierz pozycję **Nowy krok**.

   2. W polu wyszukiwania wpisz "partia" jako filtr. 
   Wybierz listę **Akcje,** a następnie wybierz tę akcję: **Wybierz przepływ pracy aplikacji logiki z wyzwalaczem wsadowym — Wysyłanie wiadomości do partii**

      ![Wybierz opcję "Wybierz przepływ pracy aplikacji logiki z wyzwalaczem wsadowym"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Wybierz aplikację logiki odbiornika wsadowego, która została wcześniej utworzona.

      ![Wybierz aplikację logiki "odbiornik wsadowy"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > Lista zawiera również inne aplikacje logiki, które mają wyzwalacze partii. 
      > 
      > Jeśli używasz programu Visual Studio i nie widzisz żadnych odbiorników wsadowych do wyboru, sprawdź, czy wdrożono odbiornik wsadowy na platformie Azure. Jeśli nie, dowiedz się, jak [wdrożyć aplikację logiki odbiornika wsadowego na platformie Azure.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) 

   4. Wybierz tę czynność: **Batch_messages - <*odbiornika* > wsadowego**

      ![Wybierz tę akcję: "Batch_messages - <> aplikacji-logic-app"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Ustaw właściwości nadawcy partii:

   | Właściwość | Opis | 
   |----------|-------------| 
   | **Nazwa partii** | Nazwa partii zdefiniowana przez aplikację logiki odbiornika, która jest "TestBatch" w tym przykładzie <p>**Ważne:** Nazwa partii zostanie zweryfikowana w czasie wykonywania i musi być zgodna z nazwą określoną przez aplikację logiki odbiornika. Zmiana nazwy partii powoduje niepowodzenie nadawcy partii. | 
   | **Treść wiadomości** | Zawartość wiadomości, którą chcesz wysłać | 
   ||| 

   W tym przykładzie dodaj to wyrażenie, które wstawia bieżącą datę i godzinę do zawartości wiadomości wysyłanej do partii:

   1. Kliknij wewnątrz **pola Zawartość wiadomości.** 

   2. Po wyświetleniu dynamicznej listy zawartości wybierz pozycję **Wyrażenie**. 

   3. Wprowadź wyrażenie `utcnow()`, a następnie wybierz przycisk **OK**. 

      ![W "Zawartość wiadomości" wybierz "Wyrażenie", wpisz "utcnow()" i wybierz "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Teraz skonfiguruj partycję dla partii. W akcji "BatchReceiver" wybierz pozycję **Pokaż opcje zaawansowane** i ustaw następujące właściwości:

   | Właściwość | Opis | 
   |----------|-------------| 
   | **Nazwa partycji** | Opcjonalny unikatowy klucz partycji używany do dzielenia partii docelowej na logiczne podzbiory i zbieranie wiadomości na podstawie tego klucza | 
   | **Identyfikator wiadomości** | Opcjonalny identyfikator wiadomości, który jest wygenerowanym globalnie unikatowym identyfikatorem (GUID) | 
   ||| 

   W tym przykładzie w polu **Nazwa partycji** dodaj wyrażenie, które generuje losową liczbę od jednego do pięciu. Pozostaw pole **Identyfikator wiadomości** puste.
   
   1. Kliknij wewnątrz pola **Nazwa partycji,** aby wyświetlić dynamiczną listę zawartości. 

   2. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**.
   
   3. Wprowadź wyrażenie `rand(1,6)`, a następnie wybierz przycisk **OK**.

      ![Konfigurowanie partycji dla partii docelowej](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Ta funkcja **rand** generuje liczbę od jednego do pięciu. 
      Więc dzielisz tę partię na pięć ponumerowanych partycji, które to wyrażenie dynamicznie ustawia.

5. Zapisz aplikację logiki. Aplikacja logiki nadawcy wygląda teraz podobnie do tego przykładu:

   ![Zapisywanie aplikacji logiki nadawcy](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązanie do przetwarzania wsadowego, pozostaw aplikacje logiki uruchomione na kilka minut. Wkrótce zaczniesz dostawać e-maile w grupach po pięć osób, wszystkie z tym samym kluczem partycji.

Aplikacja logiki nadawcy wsadowego jest uruchamiana co minutę, generuje liczbę losową od jednego do pięciu i używa tej wygenerowanej liczby jako klucza partycji dla partii docelowej, w której wysyłane są wiadomości. Za każdym razem, gdy partia ma pięć elementów z tym samym kluczem partycji, aplikacja logiki odbiornika wsadowego jest uruchamiana i wysyła pocztę dla każdej wiadomości.

> [!IMPORTANT]
> Po zakończeniu testowania upewnij się, że wyłączysz aplikację logiki BatchSender, aby zatrzymać wysyłanie wiadomości i uniknąć przeciążenia skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partii i wysyłanie komunikatów EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Twórz definicje aplikacji logiki przy użyciu języka JSON](../logic-apps/logic-apps-author-definitions.md)
* [Tworzenie aplikacji bezserwerowej w programie Visual Studio za pomocą aplikacji i funkcji logiki azure](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Obsługa wyjątków i rejestrowanie błędów dla aplikacji logiki](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
